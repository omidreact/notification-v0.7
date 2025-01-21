# notification-v0.7
Notification setup instructions RN0.7
برای راه‌اندازی **`react-native-notifications`** در پروژه React Native، می‌توانید از مراحل زیر استفاده کنید. این کتابخانه برای ارسال نوتیفیکیشن‌های محلی و ریموت (از طریق Firebase) به کار می‌رود و قابلیت‌های زیادی دارد.

در این راهنما، مراحل کامل نصب و پیکربندی کتابخانه **`react-native-notifications`** به همراه کد برای ارسال نوتیفیکیشن محلی را بررسی خواهیم کرد.

### مراحل راه‌اندازی `react-native-notifications`:

---

### **گام 1: نصب کتابخانه**

1. ابتدا کتابخانه `react-native-notifications` را به پروژه خود اضافه کنید:

   ```bash
   npm install react-native-notifications --save
   ```

   یا اگر از `yarn` استفاده می‌کنید:

   ```bash
   yarn add react-native-notifications
   ```

---

### **گام 2: لینک کردن کتابخانه**

اگر از React Native نسخه 0.60 یا بالاتر استفاده می‌کنید، این کتابخانه به طور خودکار لینک می‌شود. اما اگر از نسخه‌های پایین‌تر استفاده می‌کنید، باید به صورت دستی کتابخانه را لینک کنید:

```bash
react-native link react-native-notifications
```

---

### **گام 3: پیکربندی برای Android**

برای ارسال نوتیفیکیشن‌ها در اندروید، باید برخی تغییرات را در پروژه Android انجام دهید.

#### 3.1. **تنظیمات در `AndroidManifest.xml`**

1. به فایل `android/app/src/main/AndroidManifest.xml` بروید و این مجوزها را در بخش `<manifest>` اضافه کنید:

   ```xml
   <uses-permission android:name="android.permission.INTERNET"/>
   <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
   <uses-permission android:name="android.permission.VIBRATE"/>
   ```

2. در داخل تگ `<application>`, این سرویس‌ها و ریسیور را اضافه کنید:

   ```xml
   <service android:name="com.dieam.reactnativepushnotification.modules.PushNotificationService" android:permission="android.permission.BIND_JOB_SERVICE"/>
   <receiver android:name="com.dieam.reactnativepushnotification.modules.PushReceiver" android:exported="true"/>
   ```

#### 3.2. **اضافه کردن پیکربندی Firebase (اگر از Firebase استفاده می‌کنید)**

برای استفاده از نوتیفیکیشن‌های ریموت از Firebase، نیاز به فایل `google-services.json` دارید.

1. وارد **Firebase Console** شوید.
2. پروژه‌ی جدیدی بسازید یا به پروژه موجود بروید.
3. اپلیکیشن اندروید خود را به Firebase اضافه کنید و سپس فایل `google-services.json` را دانلود کرده و در مسیر `android/app/` قرار دهید.

#### 3.3. **پیکربندی در `build.gradle`**

در فایل `android/build.gradle`:

```gradle
classpath 'com.google.gms:google-services:4.3.3'  // در قسمت dependencies
```

در فایل `android/app/build.gradle`:

```gradle
apply plugin: 'com.google.gms.google-services'  // در انتهای فایل
```

#### 3.4. **راه‌اندازی Firebase**

اگر از Firebase برای نوتیفیکیشن‌های ریموت استفاده می‌کنید، باید افزونه Firebase را در پروژه خود اضافه کنید. این کار معمولاً شامل نصب کتابخانه `react-native-firebase` است:

```bash
npm install --save @react-native-firebase/app @react-native-firebase/messaging
```

و سپس طبق مستندات Firebase، تنظیمات لازم برای دریافت نوتیفیکیشن ریموت را انجام دهید.

---

### **گام 4: پیکربندی برای iOS**

#### 4.1. **تنظیمات در `AppDelegate.m`**

1. به فایل `ios/[YourProjectName]/AppDelegate.m` بروید.
2. در ابتدای فایل، این import‌ها را اضافه کنید:

   ```objc
   #import <RNNotifications.h>
   ```

3. سپس در `didFinishLaunchingWithOptions`, این خط را اضافه کنید:

   ```objc
   [[RNNotifications instance] didFinishLaunchingWithOptions:launchOptions];
   ```

#### 4.2. **پیکربندی `Podfile`**

1. به فایل `ios/Podfile` بروید و مطمئن شوید که Firebase و نوتیفیکیشن‌ها در آن پیکربندی شده‌اند.
2. پس از تغییرات، از پوشه `ios` دستور زیر را اجرا کنید تا Pods به‌روزرسانی شوند:

   ```bash
   cd ios
   pod install
   cd ..
   ```

---

### **گام 5: ارسال نوتیفیکیشن محلی**

حالا که پیکربندی‌ها را انجام داده‌اید، می‌توانید نوتیفیکیشن‌های محلی را ارسال کنید. در اینجا یک مثال ساده از ارسال نوتیفیکیشن محلی آورده شده است:

```javascript
import React, { useEffect } from 'react';
import { View, Button, StyleSheet } from 'react-native';
import { Notifications } from 'react-native-notifications';

const App = () => {

  useEffect(() => {
    // پیکربندی نوتیفیکیشن‌ها
    Notifications.registerRemoteNotifications();

    Notifications.events().registerNotificationReceivedForeground((notification, completion) => {
      console.log('Notification received in foreground: ', notification);
      completion({ alert: true, sound: true, badge: false });
    });

    Notifications.events().registerNotificationOpened((notification, completion) => {
      console.log('Notification opened: ', notification);
      completion();
    });

    Notifications.events().registerRemoteNotificationsRegistered((event) => {
      console.log('Remote Notifications Registered', event);
    });

    Notifications.events().registerNotificationReceivedBackground((notification, completion) => {
      console.log('Notification received in background: ', notification);
      completion();
    });
  }, []);

  // تابع ارسال نوتیفیکیشن محلی
  const sendNotification = () => {
    Notifications.postLocalNotification({
      title: 'عنوان نوتیفیکیشن',
      body: 'این یک نوتیفیکیشن محلی است.',
      extra: 'اطلاعات اضافی',
    });
  };

  return (
    <View style={styles.container}>
      <Button title="ارسال نوتیفیکیشن" onPress={sendNotification} />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
});

export default App;
```

### توضیحات کد:
- **پیکربندی نوتیفیکیشن‌ها**: در `useEffect`، ما ابتدا نوتیفیکیشن‌ها را پیکربندی می‌کنیم و به انواع رویدادهای مختلف نظیر دریافت نوتیفیکیشن‌ها در foreground و background گوش می‌دهیم.
- **ارسال نوتیفیکیشن محلی**: تابع `sendNotification` برای ارسال نوتیفیکیشن محلی استفاده می‌شود. شما می‌توانید عنوان، محتوا و اطلاعات اضافی مربوط به نوتیفیکیشن را تنظیم کنید.
- **دکمه**: یک دکمه با متن "ارسال نوتیفیکیشن" نمایش داده می‌شود. وقتی کاربر روی آن کلیک می‌کند، نوتیفیکیشن محلی ارسال می‌شود.

---

### **گام 6: تست نوتیفیکیشن‌ها**

برای تست نوتیفیکیشن‌ها:

1. از **دستگاه واقعی** استفاده کنید زیرا نوتیفیکیشن‌ها در شبیه‌سازها به درستی کار نمی‌کنند.
2. در **اندروید** و **iOS**، پس از اجرای پروژه، نوتیفیکیشن‌ها باید به درستی ارسال و دریافت شوند.

---

### **نتیجه‌گیری**

با انجام این مراحل، شما به سادگی می‌توانید نوتیفیکیشن‌های محلی و ریموت را در پروژه‌ی React Native خود با استفاده از `react-native-notifications` راه‌اندازی کنید. این کتابخانه به شما این امکان را می‌دهد که نوتیفیکیشن‌های ساده یا پیچیده را ارسال کرده و رویدادهای مختلف نوتیفیکیشن‌ها را مدیریت کنید.

اگر مشکلی داشتید یا سوالی برای شما پیش آمد، خوشحال می‌شوم که کمک کنم!
