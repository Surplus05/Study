# HTMLCollection, NodeList

둘 다 DOM 접근시 사용되는 객체 유형이다.  
둘다 유사 배열 객체로, 인덱스를 통해 접근이 가능하며 length 프로퍼티가 존재한다.

## HTMLCollection

getElementBy... 등의 DOM API 메서드 호출시 반환된다.

## NodeList

querySelectorAll 를 호출시 반환된다.

## 차이점

실시간으로 업데이트 여부의 차이이다.  
HTMLCollection은 실시간으로 업데이트되며, NodeList는 그렇지 않다.

만약 변화가 생긴다면, HTMLCollection 은 실시간으로 변경되는데, NodeList는 변경되지 않는다.

다음 코드를 한번 보자.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>예제</title>
  </head>
  <body>
    <div id="container">
      <div class="item">아이템 1</div>
      <div class="item">아이템 2</div>
    </div>

    <button onclick="updateItems()">업데이트</button>

    <script>
      function updateItems() {
        const itemsCollection = document.getElementsByClassName("item");
        const itemsNodeList = document.querySelectorAll(".item");
        const container = document.getElementById("container");

        const newItem = document.createElement("div");
        newItem.setAttribute("class", "item");
        newItem.innerHTML = "새로운 아이템";
        container.appendChild(newItem);

        console.log("HTMLCollection 길이:", itemsCollection.length);
        console.log("NodeList 길이:", itemsNodeList.length);
      }
    </script>
  </body>
</html>
```

버튼을 누르면 새로운 아이템이 추가되고, 각각 길이를 출력한다.

HTMLCollection 은 실시간으로 업데이트되기에 새로운 길이인 3을 출력한다.

NodeList는 실시간으로 업데이트되지 않기에, 기존 길이인 2를 출력한다.

한번 더 누른다고 해도, NodeList는 querySelectorAll 의 호출 시점에서의 결과를 출력할 것이다.

뭐가 더 좋고 나쁜게 아니라 사용성이 다르니, 개념을 잘 알아두고, 잘 적용하자.

# Node, Element

Node는 HTML 의 구성 요소라고 볼 수 있다.

HTML의 구성요소? div나 head, body와 같은 것들밖에 없지 않느냐? 라고 생각할 수 있겠지만, 그뿐 만 아니라 주석이나 텍스트 등 다양한 종류의 노드가 존재한다.

그래서 Element는 Node의 다양한 종류 중 하나이며, 닫는 태그와 열린 태그로 구성된다.  
div, p, body 등이 해당한다.
