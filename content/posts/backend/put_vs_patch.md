---
title: "[HTTP Method] PUT과 PATCH의 차이점"
date: 2020-06-02T18:04:02+09:00
categories: ["백엔드"]
---
RESTful API를 설계할 때 내가 만난 대부분은 주로 "수정" 기능에 대헤 **PUT** 을 사용하게 됩니다. 그러나 수정할 때 무조건 **PUT** 을 이용하는 것을 옳지 못한 방법입니다. 수정할 때는 **PUT** 말고 **PATCH** 가 있는데, 오늘 이 둘의 차이점을 간략하게 소개하고자 합니다.


### PUT

> 자원의 **전체** 수정, 자원내 모든 필드가 필요하다.


### PATCH

> 자원의 **일부분** 수정, 자원내 일부 필드가 필요하다.


예를 들어, 다음과 같은 User 정보가 있다고 가정해봅시다.

~~~
{
  name : "test_name"
  nickname : "test_nickname"
  email : "test@test.com"
}
~~~

**/users/{userId}** URl에 PUT과 PATCH 로 다음과 같이 수정된 값을 요청해보면

~~~
{   
 "nickname": "test_nicknmae2"
}
~~~


먼저 PUT의 경우에는

~~~
{
  name : null
  nickname : "test_nickname2"
  email : null
}
~~~

PATCH는

~~~
{
  name : "test_name"
  nickname : "test_nickname2"
  email : "test@test.com"
}
~~~

로 보내게됩니다. 위의 상황에서 PUT은 자원의 전체를 보내지않고 일부분만 보내게되면 나머지 데이터가 유실된채 null값이 들어가게 됩니다. 그렇기에 수정할 때 무조건적으로 PUT을 사용하는게 아닌, 적절하게 PUT과 PATCH를 이용하여 사용해야된다는 것을 알 수 있습니다.

사실 너무 당연한 이야기로 들리시는 분들도 있지만, 의외로 모르시는 분들이 많아 포스팅을 간략하게 작성해보았습니다. 오해가 있을만한 정보가 있거나 궁금한게 있으시면 언제든지 댓글 남겨주세요🙋‍♂️
