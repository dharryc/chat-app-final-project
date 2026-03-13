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

# Future problems to solve  
- cache invalidation for media
  - encrypted local device cache: media & messages
  - encryption sources:
      - https://learn.microsoft.com/en-us/dotnet/standard/security/how-to-use-data-protection
      - https://learn.microsoft.com/en-us/aspnet/core/security/data-protection/introduction?view=aspnetcore-10.0  
- authenticator app / 2FA support
- passkey support
- voice messages(?): web RTC (Real-Time Communication API)
- Message reactions

# ACTIONABLE To-Do's  
- 3/13 DATABASE SCHEMA (FIND HEBER): treat all messages almost like as group chats? Have a flag "is_groupchat" because group chats can have new members added and see past messages, whereas you can't add someone to a direct message chat. So if it's a direct message, the "group" associated to each message will only have 2 members & a FALSE is_groupchat flag, and we note who sends it via the "group_chat_member(id)" (which automatically exposes the group(id))  
MESSAGE  
- id/message/blob/timestamp stuff  
- sent_by_member --> fk group_chat_member(id)  
GROUP  
- id/name stuff  
- is_groupchat: bool  
GROUPCHAT_MEMBER: current table we have still works with this  
