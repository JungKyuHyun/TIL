# 리액트는 어떻게 동작할까?

## React 엘리먼트

호스트 환경(DOM, IOS)에서 호스트 객체는(DOM Node 같은) 제일 작은 구성 요소입니다. React에서는 제일 작은 구성 요소를 React 엘리먼트라고 합니다.

React 엘리먼트는 호스트 객체를 그릴 수 있는 일반적인 자바스크립트 객체입니다.

```javascript
// JSX는 아래 오브젝트를 만들기 위한 편의구문입니다.
// <button className="blue" />
{
  type: 'button',
  props: { className: 'blue' }
}

```

호스트 객체처럼 React 엘리먼트도 트리로 구성될 수 있습니다.

```javascript
// JSX는 아래 오브젝트를 만들기 위한 편의구문입니다.
// <dialog>
//   <button className="blue" />
//   <button className="red" />
// </dialog>
{
  type: 'dialog',
  props: {
    children: [{
      type: 'button',
      props: { className: 'blue' }
    }, {
      type: 'button',
      props: { className: 'red' }
    }]
  }
}
```

React 엘리먼트는 영속성을 가지지 않기때문에, 매번 새로 만들어지고 버려집니다.

React 엘리먼트는 불변합니다. 예를 들어 React 엘리먼트의 children이나 props를 수정할 수 없습니다. 다른 렌더링을 하고 싶다면 새로운 React 엘리먼트 트리를 생성해야합니다.

## Reconciliation(재조정)

`ReactDOM.render()`가 같은 컨테이너에 두번 호출되면 무슨 일이 일어날까요?

React의 목표는 주어진 React 엘리먼트 트리와 호스트 트리를 일치시키는 것입니다. 새로운 React 엘리먼트 트리가 전달됐을 때 호스트 객체 트리에 어떤 작업을 해야 할지 파악하는 프로세스를 [재조정](https://ko.reactjs.org/docs/reconciliation.html)이라고 부릅니다.

재조정 과정에서, 트리의 같은 위치에 있는 엘리먼트 타입이 이전 렌더링과 현재 렌더링 동일하면 React는 기존 호스트 객체를 다시 사용합니다.

```javascript
// container 엘리먼트에 button 엘리먼트 렌더
// let domNode = document.createElement('button');
// domNode.className = 'blue';
// domContainer.appendChild(domNode);
ReactDOM.render(
  <button className="blue" />,
  document.getElementById('container')
)

// 변경할 엘리먼트의 타입이 같아서(button → button) 호스트 객체를 다시 사용할 수 있으므로 수정된 클래스 네임만 변경
// domNode.className = 'red';
ReactDOM.render(
  <button className="red" />,
  document.getElementById('container')
)

// 변경할 엘리먼트의 타입이 달라서(button → p) 호스트 객체를 다시 사용할 수 없으므로 새롭게 엘리먼트 렌더
// domContainer.removeChild(domNode);
// domNode = document.createElement('p');
// domNode.textContent = 'Hello';
// domContainer.appendChild(domNode);
ReactDOM.render(<p>Hello</p>, document.getElementById('container'))

// 변경할 엘리먼트의 타입이 같아서(p → p) 호스트 객체를 다시 사용할 수 있으므로 textContent만 변경
// domNode.textContent = 'Goodbye';
ReactDOM.render(<p>Goodbye</p>, document.getElementById('container'))
```

## Lists

트리의 동일한 위치에서 엘리먼트 타입을 비교하면 일반적으로 재사용할지 다시 만들지 결정하기에 충분합니다.

하지만 위 방법은 자식들의 위치가 정적이고 순서를 바꾸지 않는 경우에만 작동합니다. 동적 리스트에서는 같은 순서인지 알 수 없습니다.

그래서 `list`가 다시 정렬된다면 React는 아이템 자체가 변화했지 순서가 변경됐다고 알진 못합니다.

`key`는 React에 렌더링 할 때마다 아이템이 다른 위치에 있다는 것을 알려줍니다.

React가 <form> 안쪽의 <p key="42">를 볼 때 이전 렌더링에서 <p key="42">가 같은 <form>에 있었는지 검사합니다. 이 방법은 <form>의 자식 순서가 바뀌더라도 작동합니다. React는 같은 key를 가지는 이전 호스트 객체를 재사용하고 시블링 순서를 다시 정렬합니다.

## Reference

- https://overreacted.io/react-as-a-ui-runtime/