---
title: "Next.js Learn"
date: "2021-07-10"
---

이 프로젝트는 [Learn Next.js](https://nextjs.org/learn)에서 제공하는 스타터 템플릿을 기반으로 제작되었으며, 지금 보고있는 문서는 필자가 Next.js를 공부하면서 중요하다 생각하는 내용을 요약해 놓은 문서입니다.

해당 프로젝트는 TypeScript를 사용합니다.

## Create a Next.js App

### 앱 생성

[Create Next App](https://nextjs.org/docs/api-reference/create-next-app)을 이용해 Next.js 앱을 자동으로 만들 수 있습니다.

```shell
npx create-next-app
# Typescript
npx create-next-app --typescript
```

### CLI에서 앱 구동

[CLI](https://nextjs.org/docs/api-reference/cli) 환경에서 서버를 실행할 수 있습니다. 생성된 디렉토리에서 다음 커맨드를 실행하여 개발 서버를 구동합니다.

```shell
npm run dev
```

개발 서버는 [Fast Refresh](https://nextjs.org/docs/basic-features/fast-refresh)가 적용되어 있어, 파일 내용의 변경점을 브라우저에 즉시 반영합니다.

## Navigate Between Pages

Next.js에서의 페이지는 [`pages` 디렉토리](https://nextjs.org/docs/basic-features/pages)의 파일에서 내보낸 React 컴포넌트입니다.

페이지의 경로는 페이지에 해당하는 **파일의 경로**와 동일합니다. 예외적으로 `pages/index.js(혹은 jsx, ts, tsx)`의 경로는 `/`입니다.

### Link 컴포넌트

[라우팅](https://nextjs.org/docs/routing/introduction)은 페이지 링크에 사용되는 `<a>` HTML 태그를 [Link 컴포넌트](https://nextjs.org/docs/api-reference/next/link)로 감싸서 사용합니다.

```jsx
import Link from "next/link";

<h1 className="title">
  Read{" "}
  <Link href="/posts/first-post">
    <a>this page!</a>
  </Link>
</h1>;
```

### Client-Side Navigation

Link 컴포넌트는 같은 Next.js 앱에 있는 두 페이지 간에 **client-side navigation**을 활성화합니다.

Client-side navigation이란 페이지 전환이 *JavaScript*로 이루어지는 것으로, 이는 기존 브라우저의 탐색보다 빠릅니다.

#### Code splitting 및 Prefetch

Next.js는 code splitting을 자동으로 수행하여, 각 페이지는 필요에 의해서만 로드됩니다. 이를 통해 수백개의 페이지가 있어도 홈페이지가 빠르게 로드됩니다.

또한, 프로덕션 상태에서 Link 컴포넌트가 사용자의 화면에 표시될 때, Next.js는 백그라운드에서 자동으로 링크된 페이지에 해당하는 코드를 prefetch합니다. 그 결과로 링크를 클릭할 때 페이지 전환이 빠르게 이루어집니다.

## Assets, Metadata, CSS

Next.js는 CSS와 Sass 지원이 기본 탑재되어 있습니다.

### Assets

Next.js는 이미지같은 정적 리소스를 [`public` 디렉토리](https://nextjs.org/docs/basic-features/static-file-serving)에 두어 활용할 수 있습니다.

#### Image 컴포넌트 및 최적화

[Image 컴포넌트](https://nextjs.org/docs/api-reference/next/image)는 일반 `<img>` HTML 태그의 확장형이며, 최적화를 자동으로 수행합니다.

```jsx
import Image from "next/image";

const YourComponent = () => (
  <Image
    src="/images/profile.jpg" // Route of the image file
    height={144} // Desired size with correct aspect ratio
    width={144} // Desired size with correct aspect ratio
    alt="Your Name"
  />
);
```

### Metadata

`<tlte>` 등의 `<head>` 태그에 해당하는 메타데이터는 [Head 컴포넌트](https://nextjs.org/docs/api-reference/next/head)를 활용하여 변경합니다.

```jsx
import Head from "next/head";

export default function FirstPost() {
  return (
    <>
      <Head>
        <title>First Post</title>
      </Head>
      <h1>First Post</h1>
    </>
  );
}
```

또한 `./pages/document.js` 파일을 생성하여 [커스텀 `Document`](https://nextjs.org/docs/advanced-features/custom-document)로 `<html>` 에 해당되는 어트리뷰트(`lang` 등)를 수정할 수 있습니다.

### CSS

Next.js는 기본적으로 여러 형태의 [CSS 지원](https://nextjs.org/docs/basic-features/built-in-css-support)을 기본적으로 포함합니다.

### Layout 컴포넌트

Layout 컴포넌트를 만들어 모든 페이지에서 활용할 수 있게 만들 수 있습니다. 예로, `components/layout.js` 파일을 만들어 CSS Modules을 적용한 예시입니다.

```jsx
import styles from "./layout.module.css";

export default function Layout({ children }) {
  return <div className={styles.container}>{children}</div>;
}
```

### 전역 스타일

`pages/_app.js`의 `App` 컴포넌트는 모든 페이지를 아우르는 top-level 컴포넌트입니다. 이를 통해 전역 CSS를 적용하는 등, 다른 페이지를 탐색할 때도 상태를 유지하는 데 사용할 수 있습니다.

```jsx
import "../styles/global.css";

export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />;
}
```

## Pre-rendering, Data Fetching

### Pre-rendering

Next.js는 기본적으로 모든 페이지를 **pre-render**합니다. 즉, _모든 페이지에 대한 HTML을 사전에 생성하며_, 이는 client-side의 JavaScript에 맞기는 것이 아니라는 의미입니다. Pre-render는 성능과 SEO에 유리합니다.

생성된 각 HTML 문서는 작은 JavaScript 코드를 포함하며, 페이지가 로드될 때 JavaScript 코드가 수행되어 페이지를 완전히 인터렉티브하게 만들어주는 **hydration** 과정이 수행됩니다.

#### Plain React 앱과의 차이

Plain한 React 앱은 pre-render 과정이 없기에 모든 웹 요소가 hydration을 통해 초기화되며, JavaScript를 비활성화하면 아무 것도 표시되지 않습니다. 이와 달리 Next.js는 Pre-rendered HTML이 표시된 후, JavaScript가 React 컴포넌트를 초기화하는 방식으로 이루어집니다.

### 두 가지의 Pre-rendering

Next.js의 pre-rendering에는 페이지에 대한 HTML을 **어느 시점에** 생성하는 지에 따라 두 가지로 나누어집니다.

- [**Static Generation(정적 생성)**](https://nextjs.org/docs/basic-features/pages#static-generation-recommended): **빌드될 때** HTML이 생성됩니다.
- [**Server-side Rendering(SSR)**](https://nextjs.org/docs/basic-features/pages#server-side-rendering): **각 요청마다** HTML이 생성됩니다.

> 개발 모드에서는 무조건 SSR로 동작합니다.

중요한 점은, Next.js에서는 각 페이지 별로 어떤 Pre-redering 방식을 사용할 지 **선택**할 수 있습니다.

요청마다 페이지를 렌더링하는 것보다 한 번 생성된 페이지를 CDN에서 제공하는 것이 더욱 빠르기에, 가급적 **Static Generation**을 사용하는 것이 좋습니다.

자기 자신에게 "사용자의 요청에 **앞서서** 페이지를 pre-render할 수 있나?" 라는 질문을 던지는 것이 방법 결정에 도움이 됩니다. 만약 대답이 "할 수 있다." 이면 **정적 생성**을 선택하는 것이 좋습니다.

만약 페이지에 자주 갱신되는 데이터를 표시해야 하고, 페이지 내용이 요청마다 매번 변경된다면 정적 생성은 좋은 선택이 아닙니다. **Server-side Rendering**은 다소 느리지만, 항상 최신 정보를 페이지에 표시할 수 있습니다. 혹은, pre-render 없이 Client-side JavaScript를 통해 표시할 수도 있습니다.

### Static Generation, Data Fetching

정적 생성은 데이터 유무와 관계없이 이루어질 수 있습니다.

외부 데이터 페칭이 필요하지 않은 페이지는 프로덕션 빌드 때 자동으로 정적 생성이 이루어집니다.

혹은, HTML을 렌더링하기 전에 먼저 외부 데이터 페칭(파일 시스템 접근, 외부 API 호출, DB 쿼리 등)이 필요한 경우인 [**데이터를 포함한** 정적 생성](https://nextjs.org/docs/basic-features/pages#static-generation-with-data) 또한 기본적으로 지원합니다.

Next.js에서는 데이터 페칭을 포함한 페이지 생성을 위해 [**`getStaticProps`**](https://nextjs.org/docs/basic-features/data-fetching#getstaticprops-static-generation) 라는 async 함수를 사용할 수 있습니다. 페이지 컴포넌트를 export할 때 같이 export할 수 있으며, 기본적인 내용은 다음과 같습니다.

- 프로덕션 빌드 때 수행되며,
- 함수 내에서 외부 데이터를 페치하고, 페이지에 props로 전달할 수 있습니다.

```jsx
export default function Home(props) {
  // ...
}

export async function getStaticProps() {
  // Get external data from the file system, API, DB, etc.

  // The value of the `props` key will be passed to the `Home` component
  return {
    props: {},
  };
}
```

`getStaticProps`를 사용함으로서, Next.js에게 *이 페이지는 데이터 의존성이 있어서 pre-render 이전에 먼저 수행해야 함*이라 안내할 수 있습니다.

이를 통해 Markdown 형태의 블로그 포스트를 불러와 블로그 사이트를 만들 수 있습니다. 다음은 이를 활용한 블로그 사이트 코드의 일부입니다.

```jsx
import { getSortedPostsData } from "../lib/posts";

export async function getStaticProps() {
  const allPostsData = getSortedPostsData();
  return {
    props: {
      allPostsData,
    },
  };
}

export default function Home({ allPostsData }) {
  /* ... */
}
```

### getStaticProps 세부 사항

`getStaticProps`는 **server-side에서만 작동**합니다. client-side에선 절대로 작동하지 않기에, 브라우저에 전송하는 것 없이 DB에 직접적으로 쿼리를 보낼 수도 있습니다.

**개발 모드**에서는 `getStaticProps`는 매 요청마다 실행됩니다. **프로덕션 모드**에서는 _빌드 시_ 수행되지만, [`getStaticPaths`](https://nextjs.org/docs/basic-features/data-fetching#getstaticpaths-static-generation)가 반환하는 [`fallback` key](https://nextjs.org/docs/basic-features/data-fetching#the-fallback-key-required) 를 이용해 변경할 수 있습니다.

`getStaticProps`는 **페이지에 의해서만** export될 수 있습니다.

### 요청 시 데이터 페칭

#### `getServerSideProps`를 이용한 SSR

만약 데이터를 빌드가 아닌 **요청 시** 페치해야 한다면, Server-side Rendering을 이용할 수 있습니다. 이는 `getStaticProps` 대신 [`getServerSideProps`](https://nextjs.org/docs/basic-features/data-fetching#getserversideprops-server-side-rendering)를 export합니다.

```jsx
export async function getServerSideProps(context) {
  return {
    props: {
      // props for your component
    },
  };
}
```

`getServerSideProps`는 요청 시 호출되는 특성으로 인해, 파라미터인 `context`에는 요청과 관련된 파라미터들이 포함되어 있습니다.

#### Client-side Rendering

페이지에서 외부 데이터를 필요로 하지 않는 부분은 정적으로 생성 후, 페이지가 로드될 대 JavaScript를 통해 외부 데이터를 페치하여 남은 부분을 배치하는 [Client-side Rendering](https://nextjs.org/docs/basic-features/data-fetching#fetching-data-on-the-client-side)을 사용할 수도 있습니다.

CSR은 사용자 대시보드와 같이 개인적이고, 사용자 특정적이고, SEO와는 상관없고, pre-render될 필요없는 페이지에 적합합니다. 데이터는 자주 갱신되어 요청 시 페칭이 필요합니다.

CSR을 위해 Next.js에는 [SWR](https://swr.vercel.app/)이라는 React hook 사용을 권장합니다. SWR은 캐싱, 재검증, 포커스 트래킹, 주기마다 다시 패칭 등을 관리할 수 있습니다.

```jsx
import useSWR from "swr";

function Profile() {
  const { data, error } = useSWR("/api/user", fetch);

  if (error) return <div>failed to load</div>;
  if (!data) return <div>loading...</div>;
  return <div>hello {data.name}!</div>;
}
```
