# Firebase authentication
If you need fast and simple authentication for your web app, Firebase is the right tool for you. You can support custom email and password authentication and/or services like Google, Facebook, etc.

## React/Redux app usage
Everything about web authentication is well documented in [Firebase docs](https://firebase.google.com/docs/auth/web/start).

### Tips
 * Install firebase through npm: `npm i --save firebase`
 * Initialize Firebase among with store (typically in `createStore.js` file)
 * Save info about authenticated user in Redux store
 * Wrapp your `App` component with *higher order component* observing user's authentication state
 * Use [Redux Saga channels](https://redux-saga.js.org/docs/advanced/Channels.html) for Firebase auth state observing logic

```javascript
// Login handler
function* handleLoginForm(action) {
    ...
    yield firebase.auth().signInWithEmailAndPassword(email, password);
    ...
}

// Helper function returning channel with Firebase auth state observer
function getAuthChannel() {
    return eventChannel(emit => {
        return firebase.auth().onAuthStateChanged(user => emit({ user }));
    });
}

// Auth state observing logic
function* observeUserFirebase() {
    yield put(authActions.firebaseAuthFetching(true));
    const authChannel = yield call(getAuthChannel);

    try {
        while (true) {
            const { user } = yield take(authChannel);
            yield put(authActions.setUser(user));
        }
    } finally {
        if (yield cancelled()) {
            authChannel.close();
        }
    }
}
 ```