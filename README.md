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
- 3/18 HEBER FEEDBACK:
  - can refer to the fk user(id) for the message sender, instead of a bool flag
  - have 2 rows for any friendship: requester -> requestee, requestee -> requester (for faster lookup)
  - actually, just treat all messages "like group chat message"
  - add many-to-many relationship tables "Status" to keep track of read status, and "Reaction" to allow multiple reactions per message per person
  - CALL HISTORY modifications - per person, so if you leave a group call early, the duration reflects how long you were in there, not how long the call lasted
  - GROUP_CHAT_MEMBER - soft delete flag (active/inactive flag)... no DELETEs... and also admin-revoke to actually delete everything about the guy ("you can't send anymore, but you can still read past stuff" vs. "you can't send, you can't even see, you won't even know you ever existed in the chat or calls")
  - Group Chat invitations? - only owner can add a new member? Have a Pending Invite status? and everything about the group chat disappears if the owner leaves?  

  



