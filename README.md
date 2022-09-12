# Vue Socket.IO Chat

### Description

A real time chat application in Vue, Socket.IO, MongoDB.

### Requirements

- Node.js, NPM, MongoDB

### Instructions

Client: install dependencies and run client on port 8080

```
cd client
npm install
npm run serve
```

Server: install dependencies and run server on port 1337

```
cd server
npm install
npm run dev
```

### Flowchart

![screenshot](flowchart.png?raw=true "Flowchart")

### Dependencies

Client:

- [vue](https://github.com/vuejs/vue)
  - frontend framework
- [vuetify](https://github.com/vuetifyjs/vuetify)
  - material design ui library
- [vee-validate](https://github.com/logaretm/vee-validate)
  - vue compatible validation
- [socket.io-client](https://github.com/socketio/socket.io-client)
  - client library for socket.io

Server:

- [express](https://github.com/expressjs/express)
  - server framework
- [nodemon](https://github.com/remy/nodemon)
  - server hot reload
- [mongoose](https://github.com/Automattic/mongoose)
  - mongodb model library
- [socket.io](https://github.com/socketio/socket.io)
  - real time socket communicator
