# Preparo

For integrated stockpile management.

# Arch

```mermaid
%%{init:{'theme':'neutral'}}%%

graph TD;
  classDef Actor fill:#7BCCAC,fill-opacity:0.5
  classDef Resource fill:#FBCCAC

  User((ユーザー)):::Actor
  Admin((管理者)):::Actor

  UserWebBack(Back\nNest)
  UserWebFront(Web\nNext)
  UserMobile(Mobile\nFlutter)

  AdminWebBack(Back\nNest)
  AdminWebFront(Front\nNext)

  CLI[Command\nCommander]
  DB[/PostgreSQL\nNeon/]:::Resource

  Scraper(Scraper\nCrawlee)
  MQ(Queue\nApache Kafka)
  Sites[/Sites/]:::Resource

  subgraph ユーザー画面
    User --> UserWebFront
    User --> UserMobile

    UserWebBack -->|sse| UserWebFront

    UserMobile <-->|connectrpc| UserWebBack
    UserWebFront <-->|connectrpc| UserWebBack
  end
  style ユーザー画面 fill:#fffff0

  subgraph 管理画面
    Admin --> AdminWebFront
    Admin --> CLI

    AdminWebFront <-->|tRPC| AdminWebBack
    CLI <-->|tRPC| AdminWebBack
  end
  style 管理画面 fill:#fff0ff

  UserWebBack <-->|connectrpc| MQ
  MQ -->|connectrpc| Scraper
  Scraper --> Sites

  AdminWebBack <-->|Drizzle-orm| DB
  UserWebBack <-->|Drizzle-orm| DB
  ```
