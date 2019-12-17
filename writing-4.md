# Spring Security OAuth2에 대한 테스트 코드를 작성해보자

기본적으로 Spring Security라는 내용은 보안과 관련된 내용으로 정말 많은 내용이 추상화되어 있기 때문에 사용하기에는 편하지만 그 개념을 익히는데 많은 어려움이 따른다. 특히 나같은 초보자에게 말이다.

거기에 OAuth2까지 사용하게 되면 로그인과 관련된 테스트 코드를 짜는 것은 매우 고역일 수 있다. 뭐 하나를 해도 로그인이 되어 권한이 있는 사용자의 요청에 대해서만 내부 api가 돌아가게 하는 것이 Security와 OAuth2의 합작품이기 때문이다.

테스트 코드를 작성할 때 이전에 Security와 OAuth2를 적용하지 않았을 경우에는 `setUp()` 과 같은 `@BeforeEach` 어노테이션이 붙어있는 메서드에서 사용자 로그인을 행하는 테스트 로직을 작성하고 그렇게 얻은 사용자 정보를 가지고 CRUD를 비롯한 그 외 기능에 대한 테스트 코드를 작성한게 일반적일 것이다.

하지만 Security와 OAuth2를 적용하게 되는 순간 이런 일반적인 내용은 씨알 하나 먹히지 않는다. `@WithMockUser`라는 어노테이션을 사용하면 손쉽게 테스트 코드를 작성할 수 있겠지만 이는 임시방편의 대안일 뿐이다. 특정 타입에 대한 사용자 정보를 기대하고 작성해야하는 테스트코드인 경우에는 `@WithMockUser` 어노테이션은 무용지물이 된다.

그래서 특정 사용자 정보를 가지고 테스트를 해야하는 경우 (Spring Security + Spring OAuth2 환경에서), 위 문제를 해결하기 위한 방법을 소개하려한다. (물론 이에 대한 더 좋은 방법이 있을 수 있지만 직접 사용해본 코드에 대해 나의 생각을 이야기 해볼 것이다. 즉, best practice는 아닐 수 있다.)

## @WithSecurityContext

우선 어노테이션 하나를 직접 만들어보자.

    @Retention(RetentionPolicy.RUNTIME)
    @WithSecurityContext(factory = WithMockCustomUserSecurityContextFactory.class)
    public @interface WithMockCustomUser {
    
    	String oauthId() default "12345";
    
      String username() default "rob";
    
      String email() default "example@gmail.com";
    }

이제부터 우리는 테스트를 위한 `@WithMockCustomUser` 라는 어노테이션을 사용할 것이다. 이 어노테이션을 보면 `@WithSecurityContext` 라는 어노테이션이 위에 붙어있다.

이 어노테이션은 Spring Security에게 테스트를 위한 `SecurityContext`를 만들어달라는 신호다. `@WithSecurityContext` 어노테이션은 속성으로 factory를 가지고 있는데 이 속성값에 할당되는 클래스는 `WithSecurityContextFactory`라는 인터페이스의 구현체이어야 한다. 

다시 말해, `@WithSecurityContext` 어노테이션은 `@WithMockCustomUser` 어노테이션이 주어졌을 때 factory 속성값에 할당된 클래스 내부에 재정의되어 있는 메서드 `createSecurityContext()` 를 호출하여 `SecurityContext`를 생성한다.

    public class WithMockCustomUserSecurityContextFactory
            implements WithSecurityContextFactory<WithMockCustomUser> {
    
        @Override
        public SecurityContext createSecurityContext(final WithMockCustomUser customUser) {
    				// 빈 context 생성
            SecurityContext context = SecurityContextHolder.createEmptyContext();
    
    				// 커스텀 OAuth2User 구현체인 SecurityUser 생성
    				// SecurityUser 생성과 편의를 위해 @WithMockCustomUser의 속성 필드도 SecurityUser과 통일 시킴
            SecurityUser principal = SecurityUser.builder()
                    .oauthId(Long.valueOf(customUser.oauthId()))
                    .username(customUser.username())
                    .email(customUser.email())
                    .build();
    
    				// Authentication에 위에서 생성한 principal 주입 & 생성
            Authentication auth =
                    new UsernamePasswordAuthenticationToken(principal, "password", principal.getAuthorities());
    
    				// 빈 context에 Authentication 넣어줌
            context.setAuthentication(auth);
            return context;
        }
    }

위 클래스는 실제 프로젝트에 적용한 `WithSecurityContextFactory` 의 구체 클래스다. 주석의 설명과 같이 특정 사용자 정보`SecurityUser`를 가지는 테스트 용 `SecurityContext`를 생성하고 있다.

이제부터 우리는 우리 입맛에 맞는 로그인된 사용자 정보를 가지고 마음껏 테스트를 할 수 있을 것이다.

## @WithSecurityContext 적용

*com.gaejangmo.apiserver.model.user.controller.UserApiController*

    @RestController
    @RequestMapping("/api/v1/users")
    public class UserApiController {
        private final UserService userService;
    
        public UserApiController(final UserService userService) {
            this.userService = userService;
        }
    
        @GetMapping("/logined")
        public ResponseEntity<UserResponseDto> find(@LoginUser SecurityUser user) {
            UserResponseDto response = userService.findUserResponseDtoByOauthId(user.getOauthId());
            return ResponseEntity.ok().body(response);
        }
    }

위 코드는 현재 로그인된 사용자 정보를 확인하는 api 코드다. `@LoginedUser` 어노테이션을 이용해 ArgumentResolver를 구현했고 `SecurityUser`  타입의 객체를 인자로 받는다.

`SecurityUser`는 실제 프로덕션 코드에서도 Security + OAuth2 로그인을 시도할 경우 인가 완료 시 생성되어 `SecurityContext`에 저장되는 객체다.

*com.gaejangmo.apiserver.model.user.controller.UserApiControllerTest*

    @SpringBootTest
    @AutoConfigureMockMvc
    class UserApiControllerTest {
    		// /api/v1/users
        private static final String USER_API = linkTo(UserApiController.class).toString();
        private static final ObjectMapper MAPPER = new ObjectMapper();
    
        @Autowired
        private MockMvc mockMvc;
    
        @Test
    		// @WithSecurityContext 호출을 위한 어노테이션 적용 (+ 속성 값 설정)
        @WithMockCustomUser(oauthId = "20608121", username = "JunHoPark93", email = "abc@gmail.com")
        void 사용자_로그인_시_로그인_정보_반환() throws Exception {
            ResultActions resultActions = mockMvc.perform(get(USER_API + "/logined")
                    .accept(MediaType.APPLICATION_JSON)
                    .contentType(MediaType.APPLICATION_JSON))
                    .andDo(print());
    
            byte[] contentAsByteArray = resultActions.andExpect(status().isOk())
                    .andExpect(content().contentType(MediaType.APPLICATION_JSON))
                    .andReturn().getResponse().getContentAsByteArray();
    
            UserResponseDto userResponseDto = MAPPER.readValue(contentAsByteArray, UserResponseDto.class);
    
    				// UserTestData.RESPONSE_DTO는 테스트 용으로 만든 확인용 객체
    				// 로그인 정보 확인 api인 find() 메서드의 반환값과 확인용 객체 비교
            assertThat(userResponseDto).isEqualTo(UserTestData.RESPONSE_DTO);
        }
    }

위 테스트 코드를 실행시킬 시 `@WithMockCustomUser(oauthId = "20608121", username = "JunHoPark93", email = "abc@gmail.com")` 를 통해 실제 api 코드인 `find(@Logined SecurityUser user)` 의 user 파라미터에 

    SecurityUser.builder()
    			.oauthId(20608121L)
    			.username("JunHoPark93")
    			.email("abc@gmail.com")
    			.build();

위와 같은 객체가 인자로 들어가게 된다. 이로써 우리 입맛에 맞는 커스텀한 로그인된 사용자 정보를 사용할 수 있게 된다.
