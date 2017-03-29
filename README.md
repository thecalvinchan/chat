# README

This is a sample application for a small RESTful chat API.
The service allows users to have conversations with each other.

## Spec

### Models

#### Users

__Relations__
```
HAS MANY Conversations THROUGH UserConversations
HAS MANY Messages
```

__Fields__
```
id
username
first_name
last_name
```

#### Conversations

__Relations__
```
HAS MANY Messages
HAS MANY Users THROUGH UserConversations
```

__Fields__
```
id
```

#### UserConversations

__Relations__
```
HAS ONE Conversation
HAS ONE User
```

__Fields__
```
user_id
conversation_id
```

#### Messages

__Relations__
```
BELONGS TO Conversation
BELONGS TO User
```

__Fields__
```
id
user_id
conversation_id
content
```

### API

`GET /users`
gets a list of users

`POST /users`
creates a new user

`GET /users/{user_id}`
gets a user

`GET /users/{user_id}/conversations`
gets a users conversations

`GET /conversations/{conversation_id}`
gets a conversation

`POST /conversations/{conversation_id}/users`
adds a user to the conversation

`DEL /conversations/{conversation_id}`
deletes a conversation

`GET /conversation/{conversation_id}/messages?page={0,1,2,...}`
gets the messages in a conversation

`POST /conversations/{conversation_id}/messages`
adds a new message to the conversation

`DEL /messages/{message_id}`
deletes a message from the conversation

### Tests

- GET /users
  - ASSERT returns a list of user objects ordered by `created_at`
  - ASSERT response has a `previous_page` attribute that contains a url to fetch
    previous set of results (may be null)
  - ASSERT response has a `next_page` attribute that contains a url to fetch next set
    of results (may be null)

- POST /users
  - ASSERT returns the created user object

- GET /users/{user_id}
  - ASSERT returns the specified user object

- GET /users/{user_id}/conversations
  - CONTEXT User is the user specified by user_id
    - ASSERT response has a list of conversation objects ordered by 'updated_at'
    - ASSERT response has a `previous_page` attribute that contains a url to fetch
      previous set of results (may be null)
    - ASSERT response has a `next_page` attribute that contains a url to fetch next set
      of results (may be null)
  - CONTEXT User is not a participant in the conversation
    - ASSERT response is 403 forbidden
    - ASSERT response is an error

- GET /conversations/{conversation_id}
  - CONTEXT User is a participant in the conversation
    - ASSERT returns the specified conversation object
    - ASSERT response has a list of user objects that belong to the conversation
  - CONTEXT User is not a participant in the conversation
    - ASSERT response is 403 forbidden
    - ASSERT response is an error

- POST /conversations/{conversation_id}/users
  - CONTEXT User is a participant in the conversation
    - ASSERT new User is added
  - CONTEXT User is not a participant in the conversation
    - ASSERT response is 403 forbidden
    - ASSERT new User is not added

- DEL /conversations/{conversation_id}
  - CONTEXT User is a participant in the conversation
    - ASSERT Conversation is deleted
  - CONTEXT User is not a participant in the conversation
    - ASSERT response is 403 forbidden
    - ASSERT Conversatoin is not deleted

- GET /conversation/{conversation_id}/messages?page={0,1,2,...}
  - CONTEXT User is a participant in the conversation
    - ASSERT response has a list of messages in a conversation ordered by
      'created_at'
    - ASSERT response has a `previous_page` attribute that contains a url to fetch
      previous set of results (may be null)
    - ASSERT response has a `next_page` attribute that contains a url to fetch next set
      of results (may be null)
  - CONTEXT User is not a participant in the conversation
    - ASSERT response is 403 forbidden
    - ASSERT response is an error

- POST /conversations/{conversation_id}/messages
  - CONTEXT User is a participant in the conversation
    - ASSERT message is created in the specified conversation
  - CONTEXT User is not a participant in the conversation
    - ASSERT response is 403 forbidden
    - ASSERT message is not added to the conversation

- DEL /messages/{message_id}
  - CONTEXT User is a participant in the conversation
    - ASSERT message is deleted from the specified conversation
  - CONTEXT User is not a participant in the conversation
    - ASSERT response is 403 forbidden
    - ASSERT message is not deleted from conversation
