# Client Endpoint

_The client side of simplified socket.io messaging._

See also [@cameronwp/server-endpoint](https://github.com/cameronwp/server-endpoint). Testing happens in @cameronwp/server-endpoint.

```sh
npm i -S @cameronwp/client-endpoint
```

## Sample Usage

All examples assume that on{message,request} handlers were set before any {message,request} were sent.

```js
// server.js
const ServerEndpoint = require('@cameronwp/server-endpoint');

const port = 8000;
const server = new ServerEndpoint(port);
const namespace = server.createNamespace('chat'); // create as many namespaces as you want

// example server -> client push
namespace.pushMessage('new-message', 'lorem ipsum...');

// example client -> server post
namespace.onmessage('config-change', val => {
    console.log(JSON.stringify(val)); // logs "{config: 'object'}"
});

// example server response to a request
namespace.onrequest('test-request', 'ack');

// example server responding to a request with a function
namespace.onrequest('test-request-doubler-function', data => {
  return data * 2;
});
```

```js
// client.js
const ClientEndpoint = require('@cameronwp/client-endpoint');

const client = new ClientEndpoint('http://localhost:8000/chat');  // note the port and "/chat" namespace

// example server -> client push
client.onmessage('new-message', val => {
    console.log(val); // logs "lorem ipsum..."
});

// example client -> server post
client.postMessage('config-change', {config: 'object'});

// example server response to a request
client.request('test-request').then(console.log); // logs "ack"

// example server response to a request
client.request('test-request-doubler-function', 2).then(console.log); // logs 4
```

## API

<a name="ClientEndpoint"></a>

### ClientEndpoint ⇐ <code>Endpoint</code>
**Kind**: global class
**Extends**: <code>Endpoint</code>

* [ClientEndpoint](#ClientEndpoint) ⇐ <code>Endpoint</code>
    * [new ClientEndpoint(location)](#new_ClientEndpoint_new)
    * [.postMessage(type, [data])](#ClientEndpoint+postMessage) ⇒ <code>Promise</code>
    * [.request(type, [key])](#ClientEndpoint+request) ⇒ <code>Promise</code>

<a name="new_ClientEndpoint_new"></a>

#### new ClientEndpoint(location)
Exposes a wrapper around a socket.io client connection to a server.


| Param | Type |
| --- | --- |
| location | <code>string</code> |

<a name="ClientEndpoint+postMessage"></a>

#### clientEndpoint.postMessage(type, [data]) ⇒ <code>Promise</code>
Post a message to the server. Resolves when the message is acknowledged. Nothing is sent to the acknowledgement.

**Kind**: instance method of [<code>ClientEndpoint</code>](#ClientEndpoint)
**Returns**: <code>Promise</code> - Promise that resolves when the server acknowledges the message.

| Param | Type | Description |
| --- | --- | --- |
| type | <code>string</code> | Type of message to send to the server. |
| [data] | <code>\*</code> | Contents of the message. |

<a name="ClientEndpoint+request"></a>

#### clientEndpoint.request(type, [key]) ⇒ <code>Promise</code>
Send a request. Returns a promise that resolves to the response.

**Kind**: instance method of [<code>ClientEndpoint</code>](#ClientEndpoint)
**Returns**: <code>Promise</code> - Promise that receives the response.

| Param | Type | Description |
| --- | --- | --- |
| type | <code>string</code> | Info you are requesting. |
| [key] | <code>string</code> | nconf style key |

