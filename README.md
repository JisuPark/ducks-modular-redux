# Ducks: Redux Reducer Bundles

<img src="duck.jpg" align="right"/>

저는 Redux 어플리케이션을 구축하는 과정에서, 한 번에 기능 한 부분을 만들 때, 각각의 use case에 따라 `{actionTypes, actions, reducer}` 한 벌을 계속 추가해야 된다는 것을 깨달았습니다. 저는 각각을 별도의 파일 심지어 폴더로 나누어두었지만, 95%의 경우에는 reducer/actions 한 쌍만이 서로 연관된 동작을 필요로 합니다.

제가 봤을 땐, 이러한 조각들이 포함된 하나의 독립된 모듈로 묶는 것이 더  의미가 있고, 라이브러리로 패키징하기에도 쉽습니다.


## 제안

### 예시

[Common JS 예시](CommonJs.md)도 참고하세요.

```javascript
// widgets.js

// Actions
const LOAD   = 'my-app/widgets/LOAD';
const CREATE = 'my-app/widgets/CREATE';
const UPDATE = 'my-app/widgets/UPDATE';
const REMOVE = 'my-app/widgets/REMOVE';

// Reducer
export default function reducer(state = {}, action = {}) {
  switch (action.type) {
    // do reducer stuff
    default: return state;
  }
}

// Action 생성자
export function loadWidgets() {
  return { type: LOAD };
}

export function createWidget(widget) {
  return { type: CREATE, widget };
}

export function updateWidget(widget) {
  return { type: UPDATE, widget };
}

export function removeWidget(widget) {
  return { type: REMOVE, widget };
}
```
### 규칙

하나의 모듈은...

1. **항상** `reducer()`란 이름의 함수를 `export default` 해야합니다.
2. **항상** 모듈의 action 생성자들을 함수형태로 `export` 해야합니다.
3. **항상** `npm-module-or-app/reducer/ACTION_TYPE` 형태의 action 타입을 가져야합니다.
3. **어쩌면** action 타입들을 `UPPER_SNAKE_CASE`로 `export` 할 수 있습니다. 만약, 외부 `reducer`가 해당 action들이 발생하는지 계속 기다리거나, 재사용할 수 있는 라이브러리로 퍼블리싱할 경우에 말이죠.

재사용가능한 Redux 라이브러리 형태로 공유하는 `{actionType, action, reducer}` 묶음에도 위 규칙을 추천합니다.

### 명칭

Java에는 jars와 beans이 있습니다. Ruby에는 gems이 있고요. 저는 위와 같은 reducer 묶음을 "redux"의 마지막 음절을 따서 "ducks"라고 부르는 것을 제안합니다.

### 적용 예

여전히 아래와 같이 사용할 수 있습니다.

```javascript
import { combineReducers } from 'redux';
import * as reducers from './ducks/index';

const rootReducer = combineReducers(reducers);
export default rootReducer;
```

여전히 아래와 같이 사용할 수 있습니다.

```javascript
import * as widgetActions from './ducks/widgets';
```
...그리고 `bindActionCreators()`에 바로 넘겨줄 수 있도록 action 생성자만을 `import` 합니다.

그리고 어쩌면 action 생성자가 아닌 것을 `export` 하고 싶을 때가 있을 겁니다. 그것도 좋습니다. 위 규칙은 **항상** action 생성자만을 `export`하라고 말하는 것은 아닙니다. 만약 action 생성자가 아닌 것을 추가했을 경우엔, action 생성자들을  나열하면 됩니다. 별 것 아닙니다.

```javascript
import {loadWidgets, createWidget, updateWidget, removeWidget} from './ducks/widgets';
// ...
bindActionCreators({loadWidgets, createWidget, updateWidget, removeWidget}, dispatch);
```

### 예시

[React Redux Universal Hot Example](https://github.com/erikras/react-redux-universal-hot-example)은 ducks를 따릅니다. [`/src/redux/modules`](https://github.com/erikras/react-redux-universal-hot-example/tree/master/src/redux/modules)를 보세요.

### 구현

이러한 코드 구조를 적용하는 것은 [쉬웠습니다](https://github.com/erikras/react-redux-universal-hot-example/commit/3fdf194683abb7c40f3cb7969fd1f8aa6a4f9c57). 그리고 추후에 개발을 할 때에도 고통이 감소할 것이라고 예상합니다.

피드백을 환영합니다! issue를 통해 피드백을 남겨주시거나 [@erikras](https://twitter.com/erikras)에 트윗을 주시면 정말 감사하겠습니다.

즐거운 코딩하세요!

-- Erik Rasmussen

---

![C'mon! Let's migrate all our reducers!](migrate.jpg)
> 사진의 저작권은 [Airwolfhound](https://www.flickr.com/photos/24874528@N04/3453886876/)에게 있습니다.
