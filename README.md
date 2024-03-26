This is the code I practiced to learn Next JS

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



