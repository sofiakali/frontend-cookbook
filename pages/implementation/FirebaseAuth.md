# Firebase authentication
If you need fast and simple authentication for your web app, Firebase is the right tool for you. You can support custom email and password authentication and/or services like Google, Facebook, etc.

## Table of contantes

* [Basic React/Redux app authentication](#basic-reactredux-app-authentication)
* [Social authentication providers](#social-authentication-providers)
    * [UI part](#ui-part)
    * [Business logic](#business-logic)
    * [Helper functions](#helper-functions)

## Basic React/Redux app authentication
Everything about web authentication is well documented in [Firebase docs](https://firebase.google.com/docs/auth/web/start).

### Tips
 * Install firebase through npm: `npm i --save firebase`
 * Initialize Firebase among with store (typically in `createStore.js` file)
 * Save info about authenticated user in Redux store
 * Wrap your `App` component with *higher order component* observing user's authentication state
 * Use [Redux Saga channels](https://redux-saga.js.org/docs/advanced/Channels.html) for Firebase auth state observing logic

```js
// Login form submit handler
function* handleLoginForm(action) {
    ...
    yield firebase.auth().signInWithEmailAndPassword(email, password);
    ...
}

// Factory function returning channel with Firebase auth state observer
function getAuthChannel() {
    return eventChannel(emit => {
        return firebase.auth().onAuthStateChanged(user => emit({ user }));
    });
}

// Auth state observing logic
function* observeUserFirebase() {
    yield put(authActions.firebaseAuthFetching(true));
    const authChannel = yield call(getAuthChannel);

    while (true) {
        const { user } = yield take(authChannel);
        yield put(authActions.setUser(user));
    }
}
 ```

 ## Social authentication providers

This is a brief description of how to implement with [Google](https://firebase.google.com/docs/auth/web/google-signin) or [Facebook](https://firebase.google.com/docs/auth/web/facebook-login) account in the Redux saga driven app having flow controlled by our authentication library [Petrus](https://www.npmjs.com/package/@ackee/petrus).

> It's not a complete copy/paste-in example. There are some minor parts missing or simplified for the sake of keeping the recipe short & readable so don't hesitate to adjust it to your needs 😉

### UI part

For social login you can use [`react-facebook-login`](https://www.npmjs.com/package/react-facebook-login) and [`react-google-login`](https://www.npmjs.com/package/react-google-login) components which are pretty well customizable and have suitable interface.

```js
const SocialLoginType = {
    GOOGLE: 'google',
    FACEBOOK: 'facebook',
};
```

the `SocialLoginType` enum is used to identify which login type was used by user

```jsx
<GoogleLogin
    clientId={config.google.clientId}
    buttonText="Login with Google"
    cookiePolicy="single_host_origin"
    onSuccess={response => loginWithSocialToken(response.tokenId, SocialLoginType.GOOGLE)}
    onFailure={(error, details) => loginFailure(error)}
/>
```

### Business logic

As described at [Petrus's documentation](https://www.npmjs.com/package/@ackee/petrus#usage), we must provide `authenticate`, `refreshTokens` and `getAuthUser` sagas to it's `configure` function.

```js
// import firebase package and also its auth module
import firebase from 'firebase/app';

import 'firebase/auth';

const firebaseApp = firebase.initializeApp(Config.firebase.app);
const firebaseAuth = app.auth();

 function* authenticate(payload) {
    yield firebaseAuth.setPersistence(firebaseAuth.Auth.Persistence.LOCAL);

    const user = yield retrieveUser(payload);
    const accessToken = yield getAccessToken(user);

    return {
        tokens: {
            accessToken,
            refreshToken: {
                token: null,
            },
        },
        user: getUserData(user),
    };
}

function* refreshTokens({ refreshToken }) {
    const user = yield retrieveUser();
    const accessToken = yield getAccessToken(user);

    return {
        accessToken,
        refreshToken,
    };
}

export default function* getAuthUser({ accessToken }) {
    const user = yield retrieveUser();

    return getUserData(user);
}
```

as you certainly noticed, there is few helpers used in the functions. Each of them will be described separately in next section.

Last step to wire components up with Petrus, is to catch action dispatched by`loginWithSocialToken`  and tell Petrus to begin logging process. Payload of the action is an [object dispatched from Login component](#ui-part).

```js
function* handleLoginForm(action) {
    yield put(Petrus.loginRequest(action.payload));
}

function handleLoginFailed({ error }) {
    Log.error('Error encountered', error);
}

export default function*() {
    yield all([
        yield takeEvery(types.LOGIN_WITH_SOCIAL_TOKEN, handleLoginForm),
        yield takeEvery(types.LOGIN_FAILURE, handleLoginFailed),
    ]);
}
```


### Helper functions

**`getAccessToken`**

Main purpose of this helper is to return token along with its expiration period. This is important since otherwise Petrus doesn't recognize expired token and user would be stuck in state where he is logged in, but his access token wouldn't work for API requests.

`user`is [`firebase.User`](https://firebase.google.com/docs/reference/js/firebase.User) object containing information about logged user.  

```js
import jwtDecode from 'jwt-decode';

function* getAccessToken(user) {
    const token = yield user.getIdToken(true);
    const { exp } = jwtDecode(token);
    const expiration = new Date(exp * 1000).toISOString();

    return {
        token,
        expiration,
    };
}
```

**`getUserData`**

Common helper to extract basic user information from the data provided by Google/Facebook stored in object [`providerData`](https://firebase.google.com/docs/reference/js/firebase.User.html#providerdata). Can be extended as needed, but often this set of information is enough.

```js
import { get } from 'lodash';

const getUserData = user => {
    const data = get(user, 'providerData[0]', user);

    return {
        name: data.displayName,
        email: data.email,
        photoURL: data.photoURL,
    };
};
```

**`retrieveUser`**

This is crucial part of the flow, althought its name implies to be about getting user, it does much more. It's true that 
when called, it will return `firebase.User` as soon and his is available.  
When the user is not logged in yet and `loginData` are provided (that's a case of use inside [`authenticate`](#social-providers-authentication)) it'll try to authenticate using one of defined social login providers and instantly return just authenticated user.

```js
function authStateChanged() {
    return new Promise(resolve => {
        firebaseApp.auth.onAuthStateChanged(user => {
            resolve(user);
        });
    });
}

async function getUserFromFirebase() {
    const { currentUser } = firebaseApp.auth;

    if (currentUser) {
        return currentUser;
    }

    const user = await authStateChanged();

    return user;
}

export default async function retrieveUser(loginData = {}) {
    const { token, type } = loginData;
    const user = await getUserFromFirebase();

    if (!user && token) {
        let credential;

        switch (type) {
            case SocialLoginType.GOOGLE:
                credential = firebase.auth.GoogleAuthProvider.credential(token);
                break;
            case SocialLoginType.FACEBOOK:
                credential = firebase.auth.FacebookAuthProvider.credential(token);
                break;
            default:
                Log.warn(`Unknown social login type ${type}`);
                return null;
        }

        const result = await firebaseApp.auth.signInWithCredential(credential);

        return result.user;
    }

    return user;
}
```

---

Now just configure Petrus the standard way, create needed actions, use code described in the recipe and it should work!  
All the code is extracted from a real project, just a bit simplified and deprived of project specific code.