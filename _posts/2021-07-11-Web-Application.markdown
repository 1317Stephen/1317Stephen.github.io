---
# layout: posts
title: "[SpringBoot/Vue.js Project] Blog"
date: 2021-07-11 20:08:08 +0900
categories:
  - Backend
  - Frontend
  - Project
tags:
  - Spring Boot
  - Vue.js
  - Node.js
  - Oracle ATP Database
  - Oracle Cloud
  - Backend
  - Frontend
  - Database
excerpt: "Develop an Web Application from start to finish! (with Oracle Cloud, Spring Boot, Vue.js)"
toc: true

web-application-structure:
  - url: /assets/images/structure-white-background.png
    image_path: assets/images/structure-white-background.png
    alt: "project structure"

frontend-plan-draft:
  - url: /assets/images/fronted-draft.png
    image_path: assets/images/fronted-draft.png
    alt: "Frontend plan draft"

v1.0.0_screenshots:
  - url: /assets/images/Home_page.png
    image_path: assets/images/Home_page.png
    alt: "Home page"
  - url: /assets/images/Home_page_with_sidebar_open.png
    image_path: assets/images/Home_page_with_sidebar_open.png
    alt: "Home page with sidebar"
  - url: /assets/images/Post_page.png
    image_path: assets/images/Post_page.png
    alt: "Post page"
  - url: /assets/images/Article_page.png
    image_path: assets/images/Article_page.png
    alt: "Article page"

---
Develop an Web Application from start to finish! (with Oracle Cloud, Spring Boot, Vue.js)


# 1. Requirements
## Project Objectives
- Develop an Web Application that works as a personal Blog.

## Specification
- Article, Category CRUD.
- Parent/Child relationships in categories.
- Show projects by year.
- Backend API -> Frontend.
- Docker.

# 2. Structure
{% include gallery id="web-application-structure" caption="Web Application Project stucture." %}

- Database: Oracle ATP Database, (Partial) Mongo DB
- Backend: SpringBoot
- Frontend: Node.js, Vue.js

# 3. Function
## Backend
: There are three APIs that hand over data from ATP Database to frontend.

- `Menu`
  - One `Menu` is consisted of several(or one) `Article`.
  - `Menu` has Parent/Child relationship. (ex. Computer Science Menu - iOS/Android Menu)
- `Article`
  - One `Article` is a page of writing.
- `Project`
  - Project is the has information(ex. started date, ended date, project title...) about previously conducted projects.
  - `Project` API is mainly used on the homepage.


## Frontend
: There are five main pages. (+ one sidebar)

- Main features and design were implemented using `Bootstrap-vue`.
- Axios used to call Springboot APIs.


{% include gallery id="frontend-plan-draft" caption="Frontend Vue.js plan draft." %}



# 4. Implementation
## Backend
### Implemented
- Basic Settings
  - `application.properties` file: [See Code...][application.properties file]

- Packages
  - 5 packages for basic functions have been developed.
    - Two packages are for setting: `ApiResponse` and `Config`.
    - The other packages are for API: `ArticleItem`, `MenuItem` and `ProjectItem`.

  - Except `Config` and `ApiResponse` package, one package consists of `Item`, `Adapter`, `Controller`, `Repository`, `Request`, `Response` and `Service`.

    ![One package](/assets/images/backend-package.png)

  
- `Config` package
: This package is for the Database, Frontend and (+Security) connection settings.
  - Class: `MongoConfig`, `OracleConfig`, `WebConfig`
  - Detailed implementation of the `Config` package: [See Code...][Config package implementation]

- `ApiReseponse` package
  - This class is for the abstract `ApiResponse`.
  - API Response classes in other main three packages' API Response classes are inherited from this Abstract.
  - `ApiReseponse` Abstart: [See Code..][ApiResponse package implementation]

- `ArticleItem`, `MenuItem`, `ProjectItem` packages
  - One package consists of 6 classes and 1 interface: `Item`, `Repository`, `Service`, `Adapter`, `Controller`, `Request`, `Response`.
  - `Item` Class: Model class.
  - `Adapter` Class: `ItemRespoense` and `Item` methods exist to build object.
  - `Repository` Interface: Send queries to Database.
  - `Service` Class: Send `Item` to browser. (`get`, `getAll`, `create`, `update`, `delete` methods)
  - `Controller` Class: URL resolution.
  - `Response` and `Request` Class
  - [See Code...][Main API packages implementation]


### To Do / Ongoing
- Membership related parts: `Security` package, `SecurityConfig` Class.
- CI/CD with Docker!

## Frontend
### Implemented
- Main three Pages implemented. Screenshots of the pages are at the below.
  - `Home.vue`: Projects and Articles.
  - `Posts.vue`: Entire articles with Menu(ex. Physics, Computer Science).
  - `Aricle.vue`: Descriptions.

### To Do / Ongoing
 - `Admin` page for the Blog. (This shoud be linked with the articleEdit, menuEdit pages.)



# 5. Result
## v1.0.0
 - Backend: Finish CRUD APIs for the Blog's Article, Menu and Project.
 - Frontend: Development of pages for visitor completed.(include edit article/menu which is not below screenshots.)

{% include gallery id="v1.0.0_screenshots" caption="Blog v1.0.0 Homepage, Homepage with sidebar, Post page, Article page" %}
 > The above page is the acutually working, but Oracle Cloud does not support free domain services in my region, I can't attach Blog URL.


[application.properties file]: /project/Web-Application-SpringBoot-Impementation/#applicationproperties
[Config package implementation]: /project/Web-Application-SpringBoot-Impementation/#config-package

[ApiResponse package implementation]: /project/Web-Application-SpringBoot-Impementation/#apiresponse-package


[Main API packages implementation]: /project/Web-Application-SpringBoot-Impementation/#main-api-packages