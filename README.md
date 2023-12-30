# react-native-workflow

Mastering the React Native Workflow: From the Initial Setup to Your First Profit

react-native workflow: from start to make first profit


## Init

```
npx react-native@latest init profit
```

### Assets

resource names must starts with lowercase letter a-z

resource names must contain only lowercase a-z, 0-9, or underscore

add ./react-native.config.js

```
module.exports = {
  project: {
    ios: {},
    android: {},
  },
  assets: ['./src/assets/', './src/assets/fonts'],
};
```

each asset run:

```
npx react-native-asset
```

### Splash sceen

https://www.npmjs.com/package/react-native-splash-screen

Android:

In your `android/settings.gradle` file, make the following additions:

```
include ':react-native-splash-screen'
project(':react-native-splash-screen').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-splash-screen/android')
```

In your `android/app/build.gradle` file, add the :react-native-splash-screen project as a compile-time dependency:
```
...
dependencies {
    ...
    implementation project(':react-native-splash-screen')
}
```

SKIP:
Update the `MainApplication.java` file to use react-native-splash-screen via the following changes:

// react-native-splash-screen >= 0.3.1
```
import org.devio.rn.splashscreen.SplashScreenReactPackage;
```

// react-native-splash-screen < 0.3.1
```
import com.cboy.rn.splashscreen.SplashScreenReactPackage;
```

in `MainActivity.java`

```
import android.os.Bundle;
// react-native-splash-screen >= 0.3.1
import org.devio.rn.splashscreen.SplashScreen; // here
// react-native-splash-screen < 0.3.1
import com.cboy.rn.splashscreen.SplashScreen; // here

public class MainActivity extends ReactActivity {
   @Override
    protected void onCreate(Bundle savedInstanceState) {
        SplashScreen.show(this);  // here
        super.onCreate(savedInstanceState);
    }
```

Create a file called launch_screen.xml in app/src/main/res/layout (create the layout-folder if it doesn't exist). The contents of the file should be the following:

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">
    <ImageView android:layout_width="match_parent" android:layout_height="match_parent" android:src="@drawable/launch_screen" android:scaleType="centerCrop" />
</RelativeLayout>



### Hide statusbar

StatusBar.setHidden(true);


### Screen Orientation

Add to `AndroidManifest.xml`

```
android:screenOrientation="portrait" 
```

example:
```
<activity
        android:name=".MainActivity"
        ...
        android:screenOrientation="portrait">
```

### icons

You can find more than 20.000 icons for your app on [Icons for react native](http://ico.simpleness.org/packs) website.

`yarn add react-native-svg react-native-ico-material-design`

```
import Icon from 'react-native-ico-material-design';

<Icon name="home-button" />
```


### Animations

```
import * as Animatable from 'react-native-animatable';
...
<Animatable.View
  animation="tada"
  easing="ease-in"
  iterationCount={3}
  iterationDelay={1500}
  duration={1000}
  delay={1000}
  style={styles.homeCenter}>
  <View style={styles.homeCircle} />

  <Text style={styles.homeButtonText}>{t('Start')}</Text>

  <Animatable.View
    animation="fadeInUpBig"
    easing="ease-out"
    delay={500}>
    <Icon
      name="backhand-index-pointing-up"
      width={windowWidth * 0.7}
      height={windowWidth * 0.7}
      style={styles.homeHand}
    />
  </Animatable.View>
</Animatable.View>
```

### Bottom and drawer menu

yarn add react-native-safe-area-context react-native-screens react-native-reanimated react-native-gesture-handler @react-navigation/native @react-navigation/native-stack @react-navigation/drawer @react-navigation/bottom-tabs react-native-svg react-native-ico-material-design

update `babel.config.js`

```
module.exports = {
  ...
  plugins: ['react-native-reanimated/plugin'],
};
```

`App.tsx` example:

```
import * as React from 'react';
import {Text, ScrollView, TouchableOpacity, StyleSheet, View, Button} from 'react-native';
import Icon from 'react-native-ico-material-design';
import {NavigationContainer, useRoute} from '@react-navigation/native';
import {createNativeStackNavigator} from '@react-navigation/native-stack';
import {createDrawerNavigator} from '@react-navigation/drawer';
import {createBottomTabNavigator} from '@react-navigation/bottom-tabs';

const Drawer = createDrawerNavigator();
const Stack = createNativeStackNavigator();
const Tab = createBottomTabNavigator();

const pageComponent =
  (name: string) =>
  ({navigation}) =>
    (
      <View style={styles.container}>
        <Text>{name}</Text>
        <Button
          title="Go to Home"
          onPress={() => navigation.navigate('Home')}
        />
        <Button
          title="Go back"
          onPress={() => navigation.canGoBack() && navigation.goBack()}
        />
      </View>
    );

const pages = {
  Home: {
    component: pageComponent('Home Page'),
    icon: 'home-button',
    isBottomButton: true,
  },
  Puzzle: {
    component: pageComponent('Puzzle Page'),
    icon: 'puzzle-piece-plugin',
    isBottomButton: true,
  },
  Config: {
    component: pageComponent('Config Page'),
    icon: 'settings-cogwheel-button',
    isBottomButton: true,
  },
  About: {
    component: pageComponent('About Page'),
    icon: 'home-button',
  },
};

const MyTabs = () => (
  <Tab.Navigator
    backBehavior="history"
    screenOptions={({route}) => ({
      tabBarIcon: ({ color, size }) => <Icon name={pages[route.name].icon || ''} color={color} size={size}/>,
      headerShown: false,
      tabBarActiveTintColor: 'red',
      tabBarInactiveTintColor: 'gray',
    })}>
    {Object.keys(pages).map((key, i) => (
      <Tab.Screen
        key={i}
        name={key}
        component={pages[key].component}
        options={pages[key].isBottomButton ? {} : {tabBarButton: () => <></>}}
      />
    ))}
  </Tab.Navigator>
);

const StackNavigator = () => (
  <Stack.Navigator screenOptions={{headerShown: false}}>
    <Stack.Screen name="MyTabs" component={MyTabs} />
  </Stack.Navigator>
);

const DrawerMenu = ({navigation}) => (
  <ScrollView>
    {Object.keys(pages).map((key, i) => (
      <TouchableOpacity style={styles.drawer} key={i} onPress={() => navigation.navigate(key)}>
        <Icon name={pages[key].icon || ''} color="grey"/>
        <Text style={styles.drawerText}>{key}</Text>
      </TouchableOpacity>
    ))}
  </ScrollView>
);

const App = () => (
  <NavigationContainer>
    <Drawer.Navigator drawerContent={DrawerMenu}>
      <Drawer.Screen name="App Name" component={StackNavigator} />
    </Drawer.Navigator>
  </NavigationContainer>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  drawer: {
    flexDirection: 'row',
    padding: 10,
    borderColor: '#ccc',
    borderRightWidth: 1,
    borderBottomWidth: 1,
  },
  drawerText: {
    paddingLeft: 10,
  },
});

export default App;
```



### Press again to exit

```
import { BackHandler, ToastAndroid } from 'react-native';
...
  const [isStarted, setIsStarted] = useState(false);
  const [backPressedCount, setBackPressedCount] = useState(0);

  useFocusEffect(
    useCallback(() => {
      const sub = BackHandler.addEventListener(
        'hardwareBackPress',
        () => {
        setBackPressedCount(pre => {
            if (isStarted) {
              setIsStarted(false);
              return pre = 0;
            } else if (pre === 0) {
              ToastAndroid.show(t('Press again to exit'), 1000);
              setTimeout(() => setBackPressedCount(0), 1000);
            }
            return pre + 1;
          });
          return true;
        },
      );
      return sub.remove;
    }, [isStarted]),
  );

  useEffect(() => {
    if (backPressedCount === 2) {
      BackHandler.exitApp();
    }
  }, [backPressedCount]);
```



### Sounds

In your android/settings.gradle file, make the following additions:

```
include ':react-native-sound'
project(':react-native-sound').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-sound/android')
```

android/app/build.gradle

```
dependencies {
  ...
  compile project(':react-native-sound')
}
```

in MainApplication.java

```
import com.zmxv.RNSound.RNSoundPackage;
```


### Ico pictires

Update in `android/app/src/main/res/mipmap-...`


### App name

Update in `android/app/src/main/res/values/strings.xml`


## Google Play

generate key
```
cd android/app
sudo keytool -genkey -v -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

enter <KESTORE PASSWORD>

add to android/gradle.properties

```
MYAPP_UPLOAD_STORE_FILE=my-upload-key.keystore
MYAPP_UPLOAD_KEY_ALIAS=my-key-alias
MYAPP_UPLOAD_STORE_PASSWORD=<KESTORE PASSWORD>
MYAPP_UPLOAD_KEY_PASSWORD=<KESTORE PASSWORD>
```

add to android/app/build.gradle

```
    signingConfigs {
        ...
        release {
            if (project.hasProperty('MYAPP_UPLOAD_STORE_FILE')) {
                storeFile file(MYAPP_UPLOAD_STORE_FILE)
                storePassword MYAPP_UPLOAD_STORE_PASSWORD
                keyAlias MYAPP_UPLOAD_KEY_ALIAS
                keyPassword MYAPP_UPLOAD_KEY_PASSWORD
            }
        }
    }
    buildTypes {
        ...
        release {
            ...
            signingConfig signingConfigs.release
            ...
        }
    }
```

### Each new version

up version in android/app/build.gradle

```
versionCode 1
versionName "1.0"
```

## build

```
npx react-native build-android --mode=release
```

### adv

Implement after application was approved on google play.

`yarn add react-native-google-mobile-ads`

https://docs.page/invertase/react-native-google-mobile-ads

add app.json

```
  "react-native-google-mobile-ads": {
    "android_app_id": "ca-app-pub-9242...~1662120393",
    "ios_app_id": "ca-app-pub-9242...~1662120393"
  }
```

in Screen:

```
import mobileAds from 'react-native-google-mobile-ads';
import { AppOpenAd, InterstitialAd, RewardedAd, BannerAd, TestIds } from 'react-native-google-mobile-ads';


  mobileAds()
    .initialize()
    .then(adapterStatuses => {
      // console.log(adapterStatuses);
      // Initialization complete!
    }).catch(console.error);

  const {width: windowWidth, height: windowHeight} = Dimensions.get('window');

...
  <View style={{flex: 1, alignItems: 'center', justifyContent: 'flex-end'}}>
    <BannerAd
      // unitId={TestIds.BANNER}
      unitId="ca-app-pub-9242.../4492297860"
      size={windowWidth >= 468 ? '468x60' : '320x50'}
      // requestOptions={{requestNonPersonalizedAdsOnly: true}}
      // onAdLoaded={() => {
      //   console.log('Advert loaded');
      // }}
      // onAdFailedToLoad={error => {
      //   console.error('Advert failed to load: ', error);
      // }}
    />
  </View> 
```
