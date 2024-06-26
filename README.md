This is the code I used to practice Next JS

- youtube: https://www.youtube.com/watch?v=ZVnjOPwW4ZA (1 hour)

## Concepts in Course
- aspects of NextJS
- environment setup
- create project
- project structure
- routing and navigation
- client component / server component
- data fetching


## Create project
$npx create-next-app@13.4
[details]
- install: y
- project name: next-app
- typescript: yes
- esLint(find syntax errors): yes
- tailwind: yes
- src directory: no
- app router: yes
- customize import alias? no

-> installs dependencies

$cd next-app

$npm run dev (node version should be above 16.8: nvm use 20.9)

-> http://localhost:3000/

## Project Structure
[app] folder: container for routing system
- layout.tsx: common layout for pages
  - {children} in body is replaced by a page dynamically at runtime depending on where the user is
- page.tsx: homepage 
```
//page.tsx
import Image from "next/image";

export default function Home() {
 return (
   <main>
     <h1>Hello World</h1>
   </main>
 );
}
```
[public] folder: put assets like images

## Routing and Navigation
- routing system in NextJS is based on convention, and it is rendered when the user is at that location
: 
1. make folder inside [app] folder
2. and then make [page.tsx] file inside folder
3. export react component

- you can use [rafce] to quickly make a page.tsx
```
//using rafce to make page.tsx 
import React from 'react'


const page = () => {
 return (
   <div>page</div>
 )
}


export default page
```

- when moving between pages, use ```<Link>``` component, not ```<a>``` anchor component

: to prevent reload of all sources, and just reload relevant content

```
import Image from "next/image";
import Link from "next/link";


export default function Home() {
 return (
   <main>
     <h1>Hello World</h1>
     <Link href="/users">Users</Link>
   </main>
 );
}
```



## Client Component / Server Component
- in nextjs, there are two environments where you can render components and generate HTML markup (on Client on web browser(client-side rendering)/or Server on Node.js Runtime(server-side rendering))

- **client-side rendering**: send large bundles to client (more memory to load-> resource heavy), no seo, less secure(api key exposed)
- **server-side rendering**: only send essential components to client (resource efficient for client), seo friendly, more secure(api keys on server)

- **server components cannot have interactivity**, that is:
  
  - listen to browser events(click,change)
  - access browser api(local storage)
  - maintain state
  - use effects

-> therefore, you need to use the two types of components flexibly

: **default is server component, use client when needed**

```
Unhandled Runtime Error
Error: Event handlers cannot be passed to Client Component props.
  <button onClick={function} children=...>
                  ^^^^^^^^^^
If you need interactivity, consider converting part of this to a Client Component.
```
에러: 서버 컴포넌트에 이벤트 만들어서 발생. 클라이언트 컴포넌트로 만들어주어야 함
해결: ‘use client’;

딱 상호작용 필요한 컴포넌트만 떼어내서 클라이언트 컴포로 만듬(ex. product card에서 addToCart만)
```
//Product Card 중에서도 딱 이벤트 걸린 버튼만 클라이언트 컴포로 따로 만듬
//ProductCard.tsx
import React from "react";
import AddToCart from "./AddToCart";


const ProductCard = () => {
 return (
   <div>
     <AddToCart />
   </div>
 );
};


export default ProductCard;
```

```
//AddtoCart.tsx
"use client";
import React from "react";


const AddToCart = () => {
 return (
   <div>
     <button onClick={() => console.log("Click")}>Add to Cart</button>
   </div>
 );
};


export default AddToCart;
```

## Data Fetching
- fetch data on client or on server
- fetch on client: usestate + useeffect: resource intensive, extra roundtrip to backend 2번 감
- fetch on server:

dummy data: jsonplaceholder.typicode.com

always fetch data on server component. 그러면 굳이 상태변수에 안 담아도 보여줄 수 있고, 가볍고, 이미 불러온 상태에서 렌더링됨! 대박

fetch on server: can do caching!

#caching:storing data somewhere that is faster to access
-you can fetch data from 3 places: memory, file system, network(slowest)
when you fetch, the result is stored in nextjs data cache, which is based on file system, so next time you need data(같은 url 부르면) 거기 들어가서 데이터 가져오는 게 아니라 data cache 에서 가져옴(데이터 빈번하게 바뀌는 경우 이를 disable 등등 할 수 있음)
```
import React from "react";


interface User {
 id: number;
 name: string;
}


const Userspage = async () => {
 const res = await fetch("https://jsonplaceholder.typicode.com/users");
 const users: User[] = await res.json();
 return (
   <>
     <h1>
       <ul>
         {users.map((user) => (
           <li key={user.id}>{user.name}</li>
         ))}
       </ul>
     </h1>
   </>
 );
};


export default Userspage;
```
1) disable cache: 빈번하게 바뀌는 경우 사용
```
const res = await fetch(
   "https://jsonplaceholder.typicode.com/users",
   {cache: 'no-store'});
```
<p align="center">
<img width="600" alt="image" src="https://github.com/yurright/mosh-nextjs-tutorial/blob/main/public/nextjsCache.png">
</p>


2) 10초마다 백엔드에서 새로운 데이터 가져올것이다.
```
const res = await fetch("https://jsonplaceholder.typicode.com/users", {
   next: {revalidate: 10},
 });
```


**cache behavior only implemented in fetch function  (axios 에서는 안 됨)**


## Static Rendering / Dynamic Rendering
-static render: app build 시 한 번만 렌더링, 그 다음에 필요할 때는 재렌더링 대신 cache에서 가져옴 (rendering at build time)
-dynamic rendering: rendering at request time
```
import React from "react";


interface User {
 id: number;
 name: string;
}


const Userspage = async () => {
 const res = await fetch("https://jsonplaceholder.typicode.com/users");
 const users: User[] = await res.json();
 return (
   <>
     <h1>
       <p>{new Date().toLocaleTimeString()}</p>
       <ul>
         {users.map((user) => (
           <li key={user.id}>{user.name}</li>
         ))}
       </ul>
     </h1>
   </>
 );
};


export default Userspage;

```
-> timestamp rendered statically(새로고침해도 안 바뀜)

캐시 끄면 동적 인식.
```
import React from "react";


interface User {
 id: number;
 name: string;
}


const Userspage = async () => {
 const res = await fetch("https://jsonplaceholder.typicode.com/users", {
   cache: "no-store",
 });
 const users: User[] = await res.json();
 return (
   <>
     <h1>
       <p>{new Date().toLocaleTimeString()}</p>
       <ul>
         {users.map((user) => (
           <li key={user.id}>{user.name}</li>
         ))}
       </ul>
     </h1>
   </>
 );
};


export default Userspage;

```
-> npm run build, npm start 하면 얘는 새로고침 시 타임스탬프 바뀜

rendering: client-side, server-side
-server-side: static(at build time), dynamic(at request time)

## Styling
styling next.js applications
global styles

css modules
-css module: css file that is scoped to a component/page
file ProductCard.module.css
.module.css 파일에서 card-container 처럼 - 쓰면 안 됨!use camel case



## Daisy UI
daisyui.com
$npm i -D daisyui@latest
좋다. 테마. 버튼. 
use theme:
```
import type { Config } from "tailwindcss";


const config: Config = {
 content: [
   "./pages/**/*.{js,ts,jsx,tsx,mdx}",
   "./components/**/*.{js,ts,jsx,tsx,mdx}",
   "./app/**/*.{js,ts,jsx,tsx,mdx}",
 ],
 theme: {
   extend: {
     backgroundImage: {
       "gradient-radial": "radial-gradient(var(--tw-gradient-stops))",
       "gradient-conic":
         "conic-gradient(from 180deg at 50% 50%, var(--tw-gradient-stops))",
     },
   },
 },
 plugins: [require("daisyui")],
 daisyui: {
   themes: ["winter"],
 },
};
export default config;
```

## 꿀팁
page 검색법: command+p 한 다음 page 검색해서 해당 페이지로 이동
npm run dev 해야 변화 즉각 반영


## 질문
-link component는 버튼 눌러 링크 이동인데, 이건 client component 아니어도 되는지?
-static rendering/dynamic rendering 어떻게 활용할지 잘 모르겠음

## 배운 내용으로 프로젝트 만들어보기
- json 예시 데이터를 데이지ui로 이쁘게 보여주고,
- 각 페이지로 데이터 분류해서 받아오기,
- 각 페이지로 이동할 수 있는 링크 버튼 메인 페이지에 만들기.
- 타임스탬프와 데이터 캐시 저장 옵션 달리해서 불러와보기
- 버튼 눌러 보기 완료로 바꿀 수 있는 클라이언트 컴포넌트 만들어보기




