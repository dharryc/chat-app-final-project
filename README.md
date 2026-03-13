# chat-app-final-project

``` mermaid
graph TB
    subgraph Clients["Clients"]
        Mobile[".NET MAUI App"]
        Web["Blazor web app"]
    end
 
    subgraph AzureAppServices["Azure App Services"]
        REST["RESTful API: Auth, User Management, Friends, Settings"]
        RT["Real-Time API (SignalR or gRPC, not sure yet): Live Messages, Typing, Voice Calls"]
    end
 
    subgraph AzureData["Azure Data Storage"]
        PG[("PostgreSQL: Users, Friends, Messages, Groups, Settings, Logs")]
        Blob["Blob Storage: Photos, Videos, Media"]
    end
 
    subgraph MobileCache["Mobile Cache"]
        Cache["Auth Token, User Preferences, Recent Chats"]
    end
 
    Mobile -->|"REST calls"| REST
    Mobile -->|"WebSocket or gRPC"| RT
    Web -->|"REST calls"| REST
    Web -->|"WebSocket or gRPC"| RT
    RT <-->|"Probably some communication for Auth"| REST
    REST --> PG
    RT --> PG
    REST --> Blob
 
    Mobile <--> Cache
```

I'm sure there's something (or lots of stuff) that we've missed, but this is a rough draft of what we've got so far

# Groundwork/Rough plan of attack

- get azure services up
    - database
    - blob storage
    - azure app services
    - Github actions
- database design
- set up auth (we probably shouldn't do that ourselves)
- RESTful API
- get signalR or gRPC working
- flesh out layout and design
- figure out how we're going to do voice chats
- how are messages written (probably a gRPC/signalR sub-service?)