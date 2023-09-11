# Event Bubbling

한 요소에 이벤트가 발생하면, 발생 요소의 핸들러가 동작하고, 이어서 부모 요소의 핸들러도 작동한다.

최상단 요소의 핸들러까지 거슬러 올라가며 작동되는데, 이를 이벤트 버블링이라 한다.

## delegation

동일한 동작을 하는 버튼이 수십개 있다고 생각 해 보자.

하나하나 이벤트 핸들러를 붙여주면 자원의 낭비가 된다.

부모에 핸들러를 달아주어 delegation(위임)하자.

버튼이 추가된다고 하더라도 핸들러를 바인딩해줄 필요가 없어진다.

## event.target, event.currentTarget

event.target은 이벤트가 발생 한 요소를 가리키고, event.currentTarget은 핸들러가 부착된 요소를 가리킨다.

```html
<div onclick="핸들러">
  <span></span>
</div>
```

div 를 클릭한 경우, event.target과 event.currentTarget div로 동일하다.

span을 클릭한 경우, event.target은 span, event.currentTarget은 div가 된다.
