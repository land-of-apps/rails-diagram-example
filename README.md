# How to use this repo

This project is a fork of the [Rails Tutorial 7th Edition](https://github.com/learnenough/rails_tutorial_sample_app_7th_ed) project. You can view the original [README here](OLD_README.md). 
This an ideal Ruby project to use with the [AppMap Navie](https://appmap.io/navie/) AI Architect.

You can use this project to ask various questions of AppMap Navie using the `@diagram` command prefix.  
This will generate Mermaid diagrams that you can use within GitHub Issues & Pull Requests, or otherwise share 
with members of your team. 


## Example Diagrams
- [Sequence Diagram](#sequence-diagram)
- [Entity Relationship](#entity-relationship)
- [Flow Chart](#flow-chart)
- [Class Map](#class-map)

### Sequence Diagram

```
@diagram the steps that happen when a user logs into my service.
```

```mermaid
sequenceDiagram
    participant User as "User"
    participant Browser as "Browser"
    participant App as "Application"
    participant DB as "Database"
    
    User->>Browser: Navigate to login page
    Browser->>App: GET /login
    App->>Browser: Render login form
    
    User->>Browser: Submit login form
    Browser->>App: POST /login with email and password
    App->>DB: SELECT user by email
    DB->>App: Return user record
    App->>User: check password, authenticated?
    
    opt valid credentials
        App->>DB: Update last login timestamp
        DB->>App: Success
        App->>Browser: Set cookie with session token
        App->>Browser: Redirect to user dashboard
    end
    
    opt invalid credentials
        App->>Browser: Render login form with error
    end
```

![Navie Sequence Diagram](navie-ruby-sequence-diagram.png)

### Entity Relationship

```
@diagram the entitiy relationships between the major parts of my data such as users, followers, following, and microposts.
```

```mermaid
erDiagram
  USER {
    int id
    string name
    string email
    string remember_token
    string activation_token
    string reset_token
  }

  RELATIONSHIP {
    int id
    int follower_id
    int followed_id
    datetime created_at
    datetime updated_at
  }

  MICROPPOST {
    int id
    int user_id
    text content
    datetime created_at
    datetime updated_at
  }

  USER ||--o{ MICROPPOST : writes
  USER ||--o{ RELATIONSHIP : follows
  USER ||--o{ RELATIONSHIP : followed_by

  RELATIONSHIP }o--|| USER : follower
  RELATIONSHIP }o--|| USER : followed
```

![Navie Entity Relationship](navie-ruby-entity-relationship.png)

### Flow Chart

```
@diagram a flow chart of what happens when a user posts a new micropost
```

```mermaid
flowchart TD
    A["POST /microposts"] --> B{"User logged in?"}
    B -->|No| C["Redirect to login page"]
    B -->|Yes| D["current_user"]
    D --> E["current_user.microposts.build(micropost_params)"]
    E --> F["Attach image to micropost"]
    F --> G{"Micropost save?"}
    G -->|No| H["Fetch current user's feed"]
    H --> I["Paginate feed items"]
    I --> J["Render static_pages/home"]
    G -->|Yes| K["Flash success message: 'Micropost created!'"]
    K --> L["Redirect to root_url"]
```

![Navie Flow Chart](navie-ruby-flow-chart.png)

### Class Map
```
@diagram a class map of the users, followers, and microposts
```

```mermaid
classDiagram
  direction LR

  class User {
      +id: int
      +name: string
      +email: string
      +remember_digest: string
      +session_token() string
      +remember() string
      +follow(other_user: User)
      +unfollow(other_user: User)
      +following?(other_user: User) bool
      +microposts: List~Micropost~
  }

  class Micropost {
      +id: int
      +content: string
      +user_id: int
      +created_at: datetime
      +updated_at: datetime
  }

  class Relationship {
      +id: int
      +follower_id: int
      +followed_id: int
      +created_at: datetime
      +updated_at: datetime
  }

  class SessionsHelper {
      +current_user?(user: User) bool
      +logged_in?() bool
      +forget(user: User)
      +log_out()
  }

  class UsersHelper {
      +gravatar_for(user: User, options: dict): string
  }

  User "1" --> "*" Micropost
  User "1" --> "*" Relationship : follower
  User "1" --> "*" Relationship : followed
```

![Navie Class Map](navie-ruby-class-map.png)