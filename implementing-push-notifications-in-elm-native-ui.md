# Push Notifications in elm-native-ui

## Ian C. Anderson
## thoughtbot

---

# Purple Train

- MBTA Commuter Rail app
- First written in React Native, ported to elm-native-ui

![right90%](purple_train_promo_image.png)

---

## elm-native-ui

- Experimental!
- Write mobile apps in elm
- Uses react-native to render, receive events from OS

---

## MBTA Alerts

- Your train is cancelled!
- Want to know before going to the station

![inline](purple_train_push_example.png)

---

## Push Notifications

In order to send push notifications to a particular device, we need its "device token"

SCREENSHOTS OF THE PUSH NOTIFICATION DIALOGS/FLOW

---

# React Native Example

```js
import { PushNotificationIOS } from 'react-native';
function onRegister(deviceToken) {
  // send token to server so we can notify later
}
PushNotificationIOS.addEventListener('register', onRegister);
PushNotificationIOS.addEventListener('registrationError', console.log);
PushNotificationIOS.requestPermissions();
```

---

# Let's elmify this!

- Need to write a "native" elm module:
  - Elm module that defines the interface for the library
  - Elm module calls into custom JavaScript module that wraps
    `PushNotificationIOS`

---

# Elm Module

```elm
-- src/NativeApi/PushNotifications.elm
module NativeApi.PushNotificationIOS exposing (register)

import Native.NativeUi.PushNotificationIOS
import Task exposing (Task)

register : Task String String
register =
    Native.NativeUi.PushNotificationIOS.register
```

---

```js
// src/Native/NativeUi/PushNotificationIOS.js
const _ohanhi$elm_native_ui$Native_NativeUi_PushNotificationIOS = function () {
  const { PushNotificationIOS } = require("react-native");

  function register() {
    // Need to return a `Task String String`
  }

  return {
    register,
  };
}();
```

---

```js
// body of register function
return _elm_lang$core$Native_Scheduler.nativeBinding(function(callback) {
  PushNotificationIOS.addEventListener('register', token => {
    callback(_elm_lang$core$Native_Scheduler.succeed(token));
  });

  PushNotificationIOS.addEventListener('registrationError', e => {
    callback(_elm_lang$core$Native_Scheduler.fail(e.message));
  });

  PushNotificationIOS.requestPermissions();
});
```

---

# Now let's use it in the app!

```elm
import NativeApi.PushNotificationIOS as PushNotificationIOS exposing (register)
import Task

-- attempt : (Result x a -> msg) -> Task x a -> Platform.Cmd.Cmd msg
Task.attempt ReceivePushToken register
```

---

# Update function


```elm
-- In update function:
ReceivePushToken result ->
    case result of
        Ok tokenString ->
            ( { model | deviceToken = Just (DeviceToken tokenString) }
            , receiveDeviceToken model.selectedStop (DeviceToken
tokenString)
            )

        Err registerError ->
            Debug.crash registerError
```

---

# Send token to server

```elm
receiveDeviceToken : Maybe Stop -> DeviceToken -> Cmd Msg
receiveDeviceToken maybeStop deviceToken =
    case maybeStop of
        Just stop ->
            upsertInstallation stop deviceToken

        Nothing ->
            Cmd.none
```
