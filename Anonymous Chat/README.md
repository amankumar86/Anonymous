# ANONYMOUS chat
### VERSION - 1.0

ANONYMOUS chat on your device with additional AES256 Encryption and File sharing (NO SIGNALING SERVER REQUIRED)


ANONYMOUS P2P chat on your device which do not require any signaling servers. It should work in both Chrome and Firefox. In this Project I used some publicly available endpoints:
- stun:stun.l.google.com:19302
- turn:turn.anyfirewall.com:443?transport=tcp (webrtc:webrtc)

Additional features:
- AES256 encryption to all messages and files
- file sharing
- chat available as a single HTML file with no dependencies over the network so you can just save that file locally [index.html](https://raw.githubusercontent.com/michal-wrzosek/p2p-chat/master/example/build/index.html)

Since there's no signaling server in between, you have to send a WebRTC connection description manually to your friend ,sometimes you have to try to connect one more time by reloading a chat)

# Library

I made a small wrapper around WebRTC for the purpose of constructing a chat. It boils down to a function I called `createPeerConnection`.

### To install:
```
npm install --save p2p-chat
```

### To use:

To initiate a new connection (as a HOST):
```typescript
import { createPeerConnection } from 'p2p-chat';

const iceServers: RTCIceServer[] = [
  {
    urls: 'stun:stun.l.google.com:19302',
  },
  {
    urls: 'turn:turn.anyfirewall.com:443?transport=tcp',
    username: 'webrtc',
    credential: 'webrtc',
  },
];

const someAsyncFunc = async () => {
  const onChannelOpen = () => console.log(`Connection ready!`);
  const onMessageReceived = (message: string) => console.log(`New incomming message: ${message}`);
  
  const { localDescription, setAnswerDescription, sendMessage } = await createPeerConnection({ iceServers, onMessageReceived, onChannelOpen });
  
  // you will send localDescription to your SLAVE and he will give you his localDescription. You will set it as an answer to establish connection
  const answerDescription = 'This is a string you will get from a SLAVE trying to connect with your localDescription';
  setAnswerDescription(answerDescription);
  
  // later on you can send a message to SLAVE
  sendMessage('Hello SLAVE');
}
```

To join a connection (as a SLAVE):
```typescript
import { createPeerConnection } from 'p2p-chat';

const iceServers: RTCIceServer[] = [
  {
    urls: 'stun:stun.l.google.com:19302',
  },
  {
    urls: 'turn:turn.anyfirewall.com:443?transport=tcp',
    username: 'webrtc',
    credential: 'webrtc',
  },
];

const someAsyncFunc = async () => {
  const remoteDescription = 'This is a string you will get from a host...';
  const onChannelOpen = () => console.log(`Connection ready!`);
  const onMessageReceived = (message: string) => console.log(`New incomming message: ${message}`);
  
  const { localDescription, sendMessage } = await createPeerConnection({ remoteDescription, iceServers, onMessageReceived, onChannelOpen });
  
  // Send your local description to HOST and wait for a connection to start
  
  // Later on you can send a message to HOST
  sendMessage('Hello HOST');
};
```

