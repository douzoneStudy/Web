# DOM?
문서 객체 모델(The Document Object Model, 이하 DOM) 은 HTML, XML 문서의 프로그래밍 interface 입니다. DOM은 문서(HTML)의 구조화된 표현을 제공하며 프로그래밍 언어가 DOM 구조에 접근할 수 있는 방법을 제공하여 그들이 문서 구조, 스타일, 내용 등을 변경할 수 있게 돕는 역할을 합니다.

## DOM 특징
- '객체 지향'적 설계
  - HTML 문서 내의 모든 요소를 객체화해서 표현하고 접근토록 함
- 문서를 '트리 구조'로 표현
  - HTML 문서를 텍스트가 아닌, 트리 구조를 갖는 객체들의 계층적 구조로 표현
- JavaScript와 같은 script언어를 통해 동적 제어 기능을 제공
  - (삭제,추가,바꾸기,이벤트 처리,수정 등)


## DOM은 어떤 형태일까?
DOM은 원본 HTML 문서의 객체 기반 표현 방식입니다. 둘은 서로 비슷하지만, DOM이 갖고 있는 근본적인 차이는 단순 텍스트로 구성된 HTML 문서의 내용과 구조가 객체 모델로 변환되어 다양한 프로그램에서 사용될 수 있다는 점입니다.
DOM의 개체 구조는 “노드 트리”로 표현됩니다.

아래 HTML 예시에서,

<img src="https://github.com/douzoneStudy/Web/blob/main/Images/DOM/%231.png"/>

아래와 같은 노드 트리로 표현 됩니다.

<img src="https://github.com/douzoneStudy/Web/blob/main/Images/DOM/%232.png"/>

노드 트리를 자바스크립트 객체로도 표현 할 수 있습니다.

```
{	
  type: 'body',
  props: {},
  children: [
    {
      type: 'h1',
  	  props: {},
      children:['Hello, World!']
    },
   {
      type: 'p',
  	  props: {},
      children:['How are you?']
    }
  ]
}
```

DOM을 이해하기 위해 웹 브라우저의 Workflow에 대해 간략하게 설명하겠습니다.

## 웹 브라우저 Workflow
웹 브라우저가 원본 HTML 문서를 읽어들인 후, 스타일을 입히고 대화형 페이지로 만들어 뷰 포트에 표시하기까지의 과정을 거칩니다.

<img src="https://github.com/douzoneStudy/Web/blob/main/Images/DOM/%233.png"/>

DOM tree 생성
CSSOM tree 생성
Render tree 가 브라우저에 그려짐
node 가 변경 될 때 마다 위의 과정을 새롭게 해야 하기 때문에 속도가 느려지게 됩니다.

Virtual DOM은 어떻게 위의 문제를 해결할까요?

## How Does Virtual DOM Work?
데이터가 업데이트 되면, 전체 UI를 Virtual DOM에 리렌더링 합니다.

이전 Virtual DOM에 있던 내용과 현재의 내용을 비교합니다.

바뀐 부분만 실제 DOM에 적용이 됩니다.

<img src="https://github.com/douzoneStudy/Web/blob/main/Images/DOM/%234.png"/>

# Virtual DOM 특징
DOM의 가벼운 복사 본
in-memory에 존재해서 실제 렌더 되지 않음
JavaScript 객체로 이루어진 tree data structure
React와 같은 UI 라이브러리에 널리 쓰임


### Virtual DOM이 렌더 횟수를 줄여주는 예시

```
<!DOCTYPE html>
<html>
  <head> </head>
  <body>
    <div id="root">
      <ul class="list">
        <li>red apple</li>
        <li>yellow banana</li>
        <li>yellow mango</li>
      </ul>
    </div>
  </body>
</html>
```
### 비효율적 업데이트 (DOM)

```
const root = document.querySelector('#root')
const liTags = root.querySelectorAll('li')

// 화면 렌더링 발생 1
liTags[0].textContent = 'red apple'

// 화면 렌더링 발생 2
liTags[1].textContent = 'yellow banana'

// 화면 렌더링 발생 3
liTags[2].textContent = 'yellow mango'
```

### 효율적인 업데이트 (Virtual DOM)

```
const root = document.querySelector('#root')
const virtualDOM = document.createElement('ul')
virtualDOM.classList.add('list')

const appleLI = document.createElement('li')
appleLI.textContent = 'red apple'

const bananaLI = document.createElement('li')
bananaLI.textContent = 'yellow banana'

const mangoLI = document.createElement('li')
mangoLI.textContent = 'yellow mango'

virtualDOM.appendChild(appleLI)
virtualDOM.appendChild(bananaLI)
virtualDOM.appendChild(mangoLI)

// 이 때 한번 뷰가 다시 렌더링 되어 진다.
root.replaceChild(virtualDOM, root.querySelector('ul'))
```

- 예시를 들기 위해 사용한 코드입니다. 위의 virtuadlDOM이란 변수가 실제 Virtual DOM을 뜻하지는 않습니다.

Virtual DOM는 어떻게 변경 되는 부분만을 알아 낼 수 있을까요? 그 답은 Reconciliation에 있습니다.

## Reconciliation(재조정)
전체 DOM 트리를 탐색하고 비교하는 일반적인 알고리즘(diff algorithm)이 있지만, 이 알고리즘은 O(n^3)의 복잡도를 갖습니다.

예를 들어, 1000개의 엘리먼트를 그리기 위해 10억 번의 비교 연산을 수행해야 합니다. React는 대신, 두 가지 가정을 기반하여 O(n) 복잡도의 휴리스틱 알고리즘을 구현했습니다.

Reconciliation 두 가지 가정
1. 서로 다른 타입의 두 엘리먼트는 서로 다른 트리를 만들어낸다.(엘리먼트가 다르면 비교하지 않는다)

개발자가 key prop을 통해, 여러 렌더링 사이에서 어떤 자식 엘리먼트가 변경되지 않아야 할지 표시해 줄 수 있다.(key를 통해 비교를 한다)
부모 노드의 타입이 다르면 자식 노드는 비교하지 않는다
react는 DOM 트리를 level-by-level로 탐색합니다. 너비 우선 탐색의 일종이라 볼 수 있습니다.

<img src="https://github.com/douzoneStudy/Web/blob/main/Images/DOM/%235.png"/>

엘리먼트의 타입이 다른 경우
```
<div>
  <Counter />
</div>
⬇︎
<span>
  <Counter />
</span>
```
위의

루트 노드가 아래 으로 바뀌면, 자식 노드인 컴포넌트는 완전히 해제되고 state 또한 파괴됩니다. 부모 엘리먼트 타입이 다를 경우 처음부터 랜더링 하는 게 더 효율적입니다. 이는 dispaly: block 규칙이 무너지기 때문에 랜더링 결과물이 어차피 달라지게 되기 때문입니다.
DOM 엘리먼트의 타입이 같은 경우
같은 타입의 두 React DOM 엘리먼트를 비교할 때, React는 두 엘리먼트의 속성을 확인하여, 동일한 내역은 유지하고 변경된 속성들만 갱신합니다. 예를 들어,
```
<div className="before" title="stuff" />
<div className="after" title="stuff" />
```  
이 두 엘리먼트를 비교하면, React는 현재 DOM 노드 상에 className 만을 수정합니다.

자식에 대한 재귀적 처리
자식 엘리먼트의 순서가 다른 경우는 아래와 같이 매우 비효율적으로 작업을 수행하게 된다.

```
<ul>
  <li>first</li>
  <li>second</li>
</ul>
```
⬇︎
```
<ul>
  <li>zero</li> {/* first가 zero가 됐군. 새로 렌더링! */}
  <li>first</li> {/* second가 first가 됐군. 새로 렌더링! */}
  <li>second</li> {/* 추가됐네. 새로 렌더링! */}
</ul>
```

위와 같은 비효율적인 문제는 key를 통해 해결 될 수 있다.
```
<ul>
  <li key="a">first</li>
  <li key="b">second</li>
</ul>
```
⬇︎
```
<ul>
  <li key="c">zero</li> {/* 추가됐네. 새로 렌더링! */}
  <li key="a">first</li> {/* 아까 그 녀석이군. 순서만 바꾸자. */}
  <li key="b">second</li> {/* 아까 그 녀석이군. 순서만 바꾸자. */}
</ul>
```

아래의 그림은 Reconciliation Process를 보여줍니다.

Component render() — updating the Virtual DOM, running the diffing algorithm and finally updating the DOM

<img src="https://github.com/douzoneStudy/Web/blob/main/Images/DOM/%236.png"/>

# 결론
이렇듯 React는 Virtual DOM을 통해 렌더를 한 번으로 줄여 효율적으로 HTML을 제어하게 됩니다.
추가로 리액트를 사용하는것이 무조건적인 성능 향상이 아닙니다. 성능적인 측면도 중요하지만 프론트엔드 뷰 라이브러리를 사용했을 때의 장점은 데이터의 흐름과 구성을 일관되게 해준다는 것이 장점입니다. 리액트를 사용하기 전에는 데이터의 소스와 DOM의 조작에 대한 자유도가 높기 때문에 일관된 패턴의 코드를 짜기 어려웠습니다. 하지만 리액트를 사용함으로써 데이터의 흐름을 강제하고, 그리고 데이터의 변화에 따라 화면에 변화를 쉽게 확인 할 수 있게 되었습니다.
