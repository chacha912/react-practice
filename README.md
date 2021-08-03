# React 렌더링 (웹 브라우저)

✅ 웹브라우저 환경에서 React API 를 사용하여 렌더링 해보기  

## DOM 스크립팅  

[💾 commit bb1bd38](https://github.com/chacha912/react-practice/commit/bb1bd3858eeecadc7410ff94e893b3837069dac7)   

### ✅ 하드코딩된 HTML 마크업을 DOM표준 API를 사용해 렌더링 하기  
- 빈 칸(공백 띄어쓰기)도 자식으로 추가하기   
- `innerHTML`, `innerText` 대신 `document.createElement`, `setAttribute`, `textContent`. `insertAdjacentElement` 사용하기    
- `appendChild`와 비교했을 때, `append`는 여러개의 자식을 한번에 추가할 수 있고, `node`와 `text`모두 추가 가능하다.  
- `Node.insertBefore(newNode, referenceNode)`에서 `referenceNode`은 필수이며, `null`을 전달하면 `append`처럼 동작한다.  
- `insertAdjacent~(position, text)` 에는 `Text`, `Element`, `HTML`이 있다. 위치는 `beforebegin`, `afterbegin`, `beforeend(appendChild와 같다)`, `afterend` 가 있다.  
- `document.createTextNode` 를 사용하여 text노드를 만들 수 있다. 노드의 내용 수정은 `nodeValue`로 가능하다. `append(Child)`를 사용하여 노드를 추가할 수 있다.  
- 태그를 파싱하여 삽입하는 경우(보안상 문제) : `innerHTML`, `insertAdjacentHTML`, `insertAdjacentElement`  
- 문자 그대로 삽입하는 경우 : `textContent`, `createTextNode`, `insertAdjacentText`    
- [innerHTML vs textContent vs createTextNode](https://plnkr.co/edit/?p=preview&preview)  


### ✅ 실제 DOM 요소가 가지는 속성 확인하기  

```javascript
console.dir(appNode) 

// attributes: NamedNodeMap
// childNodes: NodeList
// children: HTMLCollection
// className: "app"
// localName: "div"
// nodeName: "DIV"
// nodeType: 1 
```

## React API  

### ✅ React 라이브러리를 사용해 렌더링하기  

Library | for what | document | CDN
:---:|:--|:---:|:--
React | React앱을 구성하는 가상 DOM을 생성 | [📄](https://ko.reactjs.org/docs/react-api.html#createelement) | `<script src="//unpkg.com/react/umd/react.development.js" crossorigin></script>`
ReactDOM | 가상 DOM을 실제 DOM에 렌더링하는 render() 메서드 사용 | [📄](https://ko.reactjs.org/docs/react-dom.html#render) | `<script src="//unpkg.com/react-dom/umd/react-dom.development.js" crossorigin></script>`
ReactDOMServer | 실제 DOM에 렌더링하는 대신, **서버**에서 렌더링 | [📄](https://ko.reactjs.org/docs/react-dom-server.html#rendertostring) | `<script src="//unpkg.com/react-dom/umd/react-dom-server.browser.development.js" crossorigin></script>`
Babel Standalone | 웹브라우저 환경에서 Babel 컴파일러가 실시간 컴파일하도록하는 Babel의 독립형 빌드 | [📄](https://babeljs.io/docs/en/babel-standalone) | `<script src="https://unpkg.com/babel-standalone/babel.min.js" crossorigin></script>`

### ✅ 1. React 모듈 추가 - 가상 DOM 노드(React element) 만들기  

[💾 commit abb690b](https://github.com/chacha912/react-practice/commit/abb690b8e41751f87db8c05cffa1066d1407bfb0)    

- `console.log(React)`로 어떤 api있는지 확인  
- `createElement(type, props, child)`를 사용하여 `react element(virtual node)` 만들 수 있다.  
- 만들어진 react element를 콘솔에서 보면 실제 DOM을 추상화한 객체를 볼 수 있다. 필요한 것만 추상화했기 때문에 트리구조가 단순해서 빠르다.   
- 자식을 추가하는 2가지 방법: props 이후에 child로 추가 / 결국 props에 추가되므로, props에 `{children: []}`로 추가(2개 이상일 경우 배열로) [💾 commit 4206a9f](https://github.com/chacha912/react-practice/commit/4206a9f9d900d01691e34d8ea5d6d54840e28443)   

```javascript
const appElement = React.createElement(
    'div',
    {
        className: 'app',
        lang: 'en'
    },
    headingElement
);

console.log(appElement); // react요소, 가상 DOM 노드

// $$typeof: Symbol(react.element) 리액트에서의 타입
// key
// props: (= 실제 DOM의 attributes)
// ref
// type: 'div' (= 실제 DOM의 nodeName,localName)
```

### ✅ 2. ReactDOM 모듈 추가 - 실제 DOM 노드에 적용하기  

[💾 commit 699c17e](https://github.com/chacha912/react-practice/commit/699c17e4af5068e33bfc3d7dedaad214b89a7aab)  

- `const { createElement: h } = React;` 로 구조분해할당을 사용했다. `h`는 `hyperscript`를 의미한다.   
- ReactDOM 모듈 추가 후, 콘솔 확인  
    ```javascript
    console.log(ReactDOM);
    
    // 리액트 앱에서 realDOM으로 차원이동
    createPortal(children, container) 
    
    // SSR 에서 사용
    hydrate(element, container, callback) 
    
    // 리액트요소(element)를 실제DOM루트노드(container)에 추가 
    render(element, container, callback) 
    ```  
- render 메서드를 사용해 실제 DOM노드로 렌더링할 수 있다. 
    ```javascript
    const { render } = ReactDOM;

    // React VDOM Tree → render → Actual DOM Mount
    render(appElement, rootNode, () => {
        console.log('React VDOM Tree → render → Actual DOM Mount');
    });
    ```

### ✅ 3. babel 컴파일러 사용하여 JSX로 변환하기

[💾 commit ae41fed](https://github.com/chacha912/react-practice/commit/ae41fed8d18ea1f65455a0572c20a6ede74d94c9)   
[💾 commit 0e8160d](https://github.com/chacha912/react-practice/commit/0e8160d5b11eee8554c408f1cdf97eecaebc5701) 

- `render`메서드의 첫번째 인자에는 리액트 요소를 전달하는데, `jsx문법`을 사용할 수 있고, 함수 컴포넌트의 참조값을 전달할 수도 있다.  
- JSX를 사용하면 React요소를 쉽게 만들 수 있다.  
    [📄 JSX github](http://facebook.github.io/jsx/) /  [📄 React JSX](https://ko.reactjs.org/docs/introducing-jsx.html)
    > ***JSX 특징***  
    > - `React element`를 만든다. 즉, `React.createElement()`로 컴파일된다.  
    > - 값(표현식)을 삽입할 때 `{}`을 사용한다. `interpolation`(보간)  
    > - `XML-Like syntax`로 `XML` 문법을 준수하기 때문에 `empty element`에 대해 `self closing` 이 필요하다. `<img src={img.src} alt={img.alt} />`   
    > - `attribute`는 `camelCase`를 사용한다. `class`는 `className`, `tabindex`는 `tabIndex`가 된다.  
    > - 데이터 속성, wai-aria 속성은 케밥케이스를 사용한다. `data-*`, `aria-*`  
    > - 스타일 속성은 `style={{ key: value }}`로 사용한다.  
    > - JSX에 삽입된 모든 값을 렌더링하기 전에 `escape`하여 문자열로 변환되기 때문에 `XSS(cross-site-scripting)`공격을 방지한다.   
- JSX로 변경하고 브라우저에서 실행하면 `Uncaught SyntaxError: Unexpected token '<'` 에러가 발생한다. JS로 해석을 못하기 때문에 JSX를 `React.createElement()`로 변경해주는 babel 또는 typescript 컴파일러가 필요하다.  
- `babel standalone`은 보통 playground에서처럼 실시간으로 어떻게 컴파일되는지 보여줘야할 때 사용한다. 아니라면 미리 컴파일하는게 빠르다.  
- `babel standalone`을 추가하고, 바벨을 적용할 script에 `type='text/babel'`을 추가해야한다! 브라우저에서 보면 `<head>`에 ES6코드로 변경된 script 코드를 확인할 수 있다.  

### ✅ 4. ReactDOMServer 모듈 추가하기  

[💾 commit 9e7b7f4](https://github.com/chacha912/react-practice/blob/9e7b7f442df1747652271abfebd26d886d7be79e/index.html) 

- 서버에서 렌더링한 결과를 문자열로 확인할 수 있다.  
- `renderToStaticMarkup`메서드를 사용하면 가상노드가 변경된 순수 HTML 코드를 확인할 수 있다.  

```javascript
console.log('ReactDOMServer', ReactDOMServer);
// { version: "17.0.2", 
// renderToNodeStream: ƒ, 
// renderToStaticMarkup: ƒ, 
// renderToStaticNodeStream: ƒ, 
// renderToString: ƒ }

console.log(ReactDOMServer.renderToStaticMarkup(appElement));
// <div class="app" lang="en"><h1 class="app__headline">React <abbr title="Application Programming Interface">API</abbr></h1><ul class="app__list list--api"><li>React</li><li>ReactDOM</li></ul></div>

console.log(ReactDOMServer.renderToString(appElement));
// <div class="app" lang="en" data-reactroot=""><h1 class="app__headline">React<!-- --> <abbr title="Application Programming Interface">API</abbr></h1><ul class="app__list list--api"><li>React</li><li>ReactDOM</li></ul></div>
```