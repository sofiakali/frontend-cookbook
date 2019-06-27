# 🛰 Handling network status with saga channels

A part of proper error handling is to handle network connection status. I am going to show you how to handle changing network connection status with the `Navigator.connection` API and Redux Saga `eventChannel` API.

> If you know nothing about Redux saga event channels, please check out the documentation first - [Redux Saga `eventChannel` docs](https://github.com/redux-saga/redux-saga/blob/master/docs/advanced/Channels.md#using-the-eventchannel-factory-to-connect-to-external-events).

## The objective

Log into console message when application changes its network status, i.e. when it's online or offline.

## The solution

-   Create a custom event channel called `connectionChannel`.
-   Listen on changes from that channel with `takeLatest` effect, pass those changes to `handleConnectionChange` callback function.

```js
import { eventChannel } from 'redux-saga';

const Status = {
    ONLINE: true,
    OFFLINE: false,
};

function* createConnectionChannel() {
    // TODO: implement createConnectionChannel saga
}

function* handleConnectionChange(status) {
    switch (status) {
        case Status.OFFLINE:
            console.log(`You're offline, check your internet connection.`);
            break;
        case Status.ONLINE:
            console.log(`Welcome back online!`);
            break;
    }
}

export default function* handleNetworkConnection() {
    const connectionChannel = yield createConnectionChannel();

    yield takeLatest(connectionChannel, handleConnectionChange);
}
```

### Implementing the `createConnectionChannel` saga

```js
function createConnectionChannel() {
    const connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;

    return eventChannel(emit => {
        function handleConnectionStatusChange() {
            const status = connection.downlink === 0 ? Status.OFFLINE : Status.ONLINE;

            // Sent new network status out of the connectionChannel to the handleConnectionChange
            emit(status);
        }

        connection.addEventListener('change', handleConnectionStatusChange);

        // Return unsubscribe function
        return () => connection.removeEventListener('change', handleConnectionStatusChange);
    });
}
```

Unfortunatally, [`navigator.connection` isn't supported everywhere](https://caniuse.com/#search=navigator.connection), so we need to fall back to an older API, the [`online` and `offline` events](https://caniuse.com/#feat=online-status).

So `createConnectionChannel` saga is extended as follow:

```js
// ....
const connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;

if (!connection) {
    return eventChannel(emit => {
        function onlineHandler() {
            emit(Status.ONLINE);
        }

        function offlineHandler() {
            emit(Status.OFFLINE);
        }

        window.addEventListener('online', onlineHandler);
        window.addEventListener('offline', offlineHandler);

        return () => {
            window.removeEventListener('online', onlineHandler);
            window.removeEventListener('offline', offlineHandler);
        };
    });
}
// ....
```

## The result

```js
import { sagaEffects, eventChannel, messageActions } from '../../dependencies';

import { Status } from '../../constants';

const { takeLatest, put } = sagaEffects;

function createConnectionChannel() {
    const connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;

    if (!connection) {
        return eventChannel(emit => {
            function onlineHandler() {
                emit(Status.ONLINE);
            }

            function offlineHandler() {
                emit(Status.OFFLINE);
            }

            window.addEventListener('online', onlineHandler);
            window.addEventListener('offline', offlineHandler);

            return () => {
                window.removeEventListener('online', onlineHandler);
                window.removeEventListener('offline', offlineHandler);
            };
        });
    }

    return eventChannel(emit => {
        function handleConnectionStatusChange() {
            const status = connection.downlink === 0 ? Status.OFFLINE : Status.ONLINE;

            emit(status);
        }

        connection.addEventListener('change', handleConnectionStatusChange);

        return () => connection.removeEventListener('change', handleConnectionStatusChange);
    });
}

function* handleConnectionChange(status) {
    switch (status) {
        case Status.OFFLINE:
            console.log(`You're offline, check your internet connection.`);
            break;
        case Status.ONLINE:
            console.log(`Welcome back online!`);
            break;
    }
}

export default function* handleNetworkConnection() {
    const connectionChannel = yield createConnectionChannel();

    yield takeLatest(connectionChannel, handleConnectionChange);
}
```

## Why using both APIs?

You may be asking, why did I use the first API at all, when the older one can do the trick as well? Well, because for **the older API the "online" status does not always mean connection to the internet, it can also just mean connection to some network**. By checking the actual `downlink` value, we can be sure wheter the app has internet connection or not.

---

## Resources

-   [Navigator.connection
    ](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/connection)
-   [Navigator.onLine
    ](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorOnLine/onLine)
-   [Redux Saga - Using Channels](https://github.com/redux-saga/redux-saga/blob/master/docs/advanced/Channels.md#using-channels)
