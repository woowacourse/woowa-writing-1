# 그래서 뭘 배웠어?
&nbsp;“그래서 뭘 배웠어?”  
&nbsp;얼마 전 우아한 테크코스에 대해 지인에게 이야기하던 도중 받았던 질문이다. 처음에는 신이 나서 운을 떼기 시작했다. 어떤 이야기부터 해야 할까? TDD를 소개할까? 나약한 프로그래머들에게 TDD가 주는 믿음과 용기에 대해 이야기 해줘야겠다. 객체 지향 설계도 많이 배우지 않았나? 설계에는 정답이 없고, 스스로의 답을 찾아가는 과정이라는 걸 알려줘야겠다. 그리고 자기주도 학습과 관련된 이야기도 빠질 수 없지. 프로그래밍은 끝없는 수련이고, 장인 정신이고, 철학이다····· 설명이 점점 장황해지며 산으로 가기 시작했다. 그러다 급기야는 테크코스의 아름답고 환경 친화적인 라운지에 대해 설명하기 시작했는데, 이 즈음 되어서 그 친구는 내가 잘 꾸며 놓은 신전에서 기묘한 종교철학 같은 것을 배운다고 생각하기 시작했다. 참 난감했다. '프로그래밍 기본에 대해 배운다'고만 이야기하기에는 뭔가 아쉬웠고, 그래서 조금 더 설명을 하자니 뭘 배우는지 간단하게 정리하기 어려웠다. 지식은 복잡한 내용들을 단순하게 재구성할 수 있어야 비로소 스스로의 것이 된다더라. 나는 이것저것 머리에 집어넣기만 했지 무엇을 배웠는지 아직 잘 모르는 것 같았다.  
&nbsp;한 달 동안 테크코스에서 뭘 배웠을까? 지난 수업들을 되짚어가며 한참을 고민했다. 우리는 TDD를 배웠다. 페어프로그래밍을 했다. 코드리뷰를 받았다. 리펙토링을 했다. 객체지향 설계에 대해 공부했다. 이렇게 쭉 놓고 보니 그제야 정리가 조금 된다. 우리는 한 달간 품질이 좋은 코드를 작성하기 위해 무엇을 어떻게 연습해야 하는지 배웠다. 연습은 오로지 나의 몫이다. 아래에서는 코스에서 배운 연습 방법들 중에 몇 가지를 소개해보려고 한다. TDD와 코드 리뷰, 페어 프로그래밍을 연습해 보면서 생각했던 것들을 간단히 정리했다.

### 단위 테스트와 테스트 주도 개발 (TDD; Test Driven Development)
 &nbsp;『클린코드』의 저자 로버트 C. 마틴은 코드 품질에 대해 이렇게 이야기한다.
 > "테스트 케이스가 없는 코드는 깨끗한 코드가 아니다. 아무리 코드가 우아해도, 아무리 가독성이 높아도, 테스트 케이스가 없으면 깨끗하지 않다."
 
테스트 케이스는 프로덕션 코드가 제대로 동작하는지 검증하기 위한 코드인데, 코드를 깨끗하게 만드는 것과 무슨 관련이 있을까? 물론 큰 관련이 있다. 첫째, 테스트 케이스는 프로덕션 코드가 어떻게 동작해야 하는지 알려준다. 훗날 코드를 사용하거나 수정할 누군가에게 요구사항을 명시할 수 있게 된다. 둘째, 테스트 케이스는 프로그래머가 보다 안정감을 갖고 리펙토링할 수 있게 해준다. 그러면 설계를 보다 자유롭게 변경할 수도 있고, 당장은 좋지 못한 코드에 깨끗해질 수 있는 가능성이 열리기도 한다. 이런 이유에서 깨끗하고 품질 좋은 코드를 만들기 위해서는 테스트 케이스를 반드시 작성해야 한다.  
&nbsp; TDD는 테스트 케이스를 어떻게 작성할 것인가에 대한 개발 방법론이다. 실제 코드를 짜기 전에 테스트부터 작성할 것을 요구하는 것이 특징이다. 그런데 막상 TDD를 연습하려면 어디서부터 테스트를 시작해야 할지 몰라 난감할 때가 많다. 이 때 도움이 많이 되었던 것이 ‘그냥 막 해보고 날리기’였다. 설계를 완벽하게 하고 시작하지 말고, 테스트해볼 수 있는 기능부터 아무거나 만들어본다. 그러다가 설계가 마음에 들지 않으면 다 지우고 처음부터 다시 시작한다. 처음에는 열심히 작성한 코드를 버리기 쉽지 않은데, 도메인에 대해 학습하는데 드는 기회비용이라고 생각하면 마음 편하게 지워버릴 수 있다. 지우고 만들기를 반복하며 빠르게 테스트 사이클을 줄여가는 것이 TDD 연습에 상당히 효과적인 방법일 수 있다.
 
### 코드 리뷰
&nbsp; 코스에서는 매주 주어지는 과제가 있고, 과제를 제출하면 작성한 코드에 대한 피드백을 받는다. 현업에 계신 개발자분들이 리뷰어로 활동하며 리뷰해 주시는데, 이 피드백으로부터 배우는 것들이 굉장히 많다. 객체지향적인 설계와 좋은 코드에 대해 정말 많이 배운다. 하나하나 주옥같은 피드백이고, 텍스트에서 쉽게 배울 수 없는 것들이다. 여기에 더해서 배울 수 있는 게 하나 더 있다. 리뷰어들의 코드 리뷰 방식이다.  
&nbsp; 요새 괜찮은 기업에서는 코드 리뷰를 많이 한다. 신입 개발자들도 코드리뷰를 한다. 그런데 리뷰를 해본 적도 받아본 적도 없으면 코드 리뷰를 어떻게 해야 하는지 당연히 모른다. 익숙해지기 전까지는 다른 사람의 코드를 읽고 이해하는 것만 해도 생각보다 시간이 많이 걸리는데, 거기에 더해 피드백까지 할 수 있을까? 어려운 일이다. 리뷰도 미리 연습해야 한다. 리뷰어들이 매 과제마다 전달해주는 피드백은 어떻게 코드 리뷰를 연습해야 하는지에 대한 힌트가 된다. 주로 어떤 내용을 리뷰하는지, 피드백을 어떻게 효과적으로 명시하고 전달하고 있는지, 어떤 용어와 표현들을 사용하는지 등을 살펴보면 배울 것이 참 많다. 이렇게 얻은 힌트를 바탕으로 실제로 코드리뷰를 연습해볼 수도 있다. 잘 찾아보면 주변에 코드리뷰를 받고 싶어 하는 사람들이 꽤 있다. 적당한 코드리뷰 마루타를 찾아 스터디를 제안해보는 것도 좋은 방법이다. 생각보다 서로에게 도움이 많이 된다.
 
### 페어 프로그래밍
&nbsp; '러버덕 디버깅'이라는 말이 있다. 프로그래머가 코딩 중 막힐 때 누군가에게(심지어 비전문가나 고무 오리 같은 무생물일지라도) 설명을 하다 보면 스스로 해결책을 찾는 경우가 많다는 데서 유래한 말이다. 페어 프로그래밍은 러버덕 디버깅의 연장선상에 있는 프로그래밍 방식이다. 과제를 해결하기 위해 두 명이 짝을 맺고 하나의 키보드로 번갈아 코딩하며 과제를 진행한다. 이렇게 함께 코드를 작성하면서 왜 이렇게 코드를 짜야 하는지 끊임없이 의견을 교환하고 합의해 나가야 한다. 페어 프로그래밍을 하더라도 반드시 좋은 코드가 나오지는 않는다. 하지만 상대방에게 코드를 작성하는 의도를 계속 밝혀야 하기 때문에 좋은 코드를 작성하는 연습을 하는 데에 큰 도움이 된다. 로버트 C. 마틴은 『클린 코드』에서 "코드를 읽는 사람에게 프로그래머가 단호하다는 인상을 줘야 한다"고 이야기한다. 코드는 의도가 분명하고 명확해야 한다는 뜻이다. 페어 프로그래밍을 할 때 이런 부분에 초점을 두고 연습하는 것도 무척 의미 있게 시간을 보내는 방법일 것 같다.  

<br>
&nbsp; 코스에서 한 달간 배운 연습 방법들을 살펴봤다. 어떤 프로그래머가 이야기하기를, 좋은 품질의 코드는 하늘에서 뚝 떨어진 위대한 방법론이 아니라 사심 없이 기울이는 무수한 관심으로부터 나온다고 한다. 테크코스에서 배운 것들은 방법론이다. 결국 좋은 품질의 코드를 만들어내려면 내가 의식적으로 연습하는 수밖에 없다. 배운 것들을 잘 갈고 닦아 좋은 개발자가 될 수 있기를 바란다.
