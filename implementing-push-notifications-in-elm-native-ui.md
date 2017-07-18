# Push Notifications in elm-native-ui

## Ian C. Anderson
## thoughtbot

---

# Purple Train

- MBTA Commuter Rail app
- First written in React Native, ported to elm-native-ui

---

## elm-native-ui

- Experimental!
- Write mobile apps in elm
- Uses react-native to render, receive events from OS

---

## Push Notifications

In order to send push notifications to a particular device, we need its "device token"
<br />


```js
import { PushNotificationIOS } from 'react-native';
function onRegister(deviceToken) {
  // send to server
}
PushNotificationIOS.addEventListener('register', onRegister);
PushNotificationIOS.requestPermissions();
```

---

# React Native Push Notifications

- React Native has `PushNotificationIOS` library
