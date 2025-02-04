# 중첩된 경로 %{#nested-routes}%

<VueSchoolLink
href="https://vueschool.io/lessons/nested-routes"
title="Learn about nested routes"
/>

일부 앱의 UI는 여러 단계로 중첩된 컴포넌트로 구성됩니다. 이 경우, URL 세그먼트가 중첩된 컴포넌트 중 특정 구조와 일치되어야 하는 것은 매우 일반적입니다. 예를 들면 다음과 같습니다:

```
/user/johnny/profile                     /user/johnny/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------>  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```

Vue Router를 사용하는 경우, 중첩된 경로 구성을 통해 이러한 구조적 관계를 구현할 수 있습니다.

지난 장에서 만든 앱을 예로들면:

```html
<div id="app">
  <router-view></router-view>
</div>
```

```js
const User = {
  template: '<div>유저 {{ $route.params.id }}</div>',
}

// 이것은 `createRouter`로 전달됨
const routes = [{ path: '/user/:id', component: User }]
```

여기서 `<router-view>`는 최상위 `router-view`입니다. 최상위 경로와 일치하는 컴포넌트를 렌더링합니다. 마찬가지로 렌더링된 컴포넌트는 자체 중첩된 `<router-view>`를 포함할 수도 있습니다. 예를 들어 `User` 컴포넌트의 템플릿 내부에 `<router-view>`를 하나 추가하면:

```js
const User = {
  template: `
    <div class="user">
      <h2>유저 {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  `,
}
```

이 중첩된 `router-view`에 컴포넌트를 렌더링하려면, `/user/:id` 경로 내부의 모든 경로에 `children` 옵션을 사용해야 합니다:

```js
const routes = [
  {
    path: '/user/:id',
    component: User,
    children: [
      {
        // /user/:id/profile 와 매치된 경우,
        // User 컴포넌트 내부의 <router-view>에서 렌더링됨: UserProfile
        path: 'profile',
        component: UserProfile,
      },
      {
        // /user/:id/posts 와 매치된 경우,
        // User 컴포넌트 내부의 <router-view>에서 렌더링됨: UserPosts
        path: 'posts',
        component: UserPosts,
      },
    ],
  },
]
```

**`/`로 시작하는 중첩 경로는 루트 경로로 처리됩니다. 이를 통해 중첩 URL을 사용하지 않고도 컴포넌트 중첩을 활용할 수 있습니다.**

보시다시피 `children` 옵션은 `routes`처럼 경로를 나타내는 배열입니다. 따라서 필요한 만큼 중첩된 `router-view`를 사용할 수 있습니다.

위의 구성 상태로 `/user/eduardo`를 방문하면 일치하는 중첩 경로가 없으므로, `User`의 `router-view` 내부에 아무것도 렌더링 되지 않습니다. 이럴 때, 그곳에 무언가를 렌더링하고 싶을 수 있습니다. 이 경우, 빈 중첩 경로를 제공할 수 있습니다.

```js
const routes = [
  {
    path: '/user/:id',
    component: User,
    children: [
      // /user/:id 와 매치된 경우,
      // User 컴포넌트 내부의 <router-view>에서 렌더링됨: UserHome
      { path: '', component: UserHome },

      // ...다른 하위 경로
    ],
  },
]
```

참고: [예제](https://codesandbox.io/s/nested-views-vue-router-4-examples-hl326?initialpath=%2Fusers%2Feduardo)

## 중첩된 이름이 있는 경로 %{#nested-named-routes}%

[이름이 있는 경로](./named-routes.md)를 처리할 때, 일반적으로 **"자식 경로의 이름"만 지정**합니다:

```js
const routes = [
  {
    path: '/user/:id',
    component: User,
    // 자식 경로에만 이름이 있음.
    children: [{ path: '', name: 'user', component: UserHome }],
  },
]
```

이렇게 하면 `/user/:id`로 이동하면 항상 중첩 경로가 표시됩니다.

일부 시나리오에서는 중첩 경로로 이동하지 않고 이름이 있는 경로로 이동하고자 할 수 있습니다. 예를 들어 중첩된 이름이 있는 경로(위 예제의 `name: user` 경로)를 표시하지 않고 `/user/:id`로 이동하려는 경우입니다.

```js
const routes = [
  {
    path: '/user/:id',
    name: 'user-parent',
    component: User,
    children: [{ path: '', name: 'user', component: UserHome }],
  },
]
```
