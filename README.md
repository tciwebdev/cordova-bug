# cordova-bug
This project is to show a bug in the Cordova Network Plugin in regards to the "Doze" mode in Android 6.0 and up.

Once the phone puts the app into "Doze" mode, the network plugin always shows the app as offline until it is killed and relaunched.
<https://issues.apache.org/jira/browse/CB-12325>

I normally build the app via Build.PhoneGap.com, and install the app on my Android emulator or physical device, and follow the steps from the JIRA bug above to replicate the issue.

Reproduction steps:
1. Install and launch app on Android Emulator.
2. Press the "Network Check" button. You should see an alert that says "we are online", plus another alert that states the Network State is 4G, WIFI, etc (based on the network state of the emulator)
3. From the CLI run the following commands

+ adb shell dumpsys deviceidle enable
+ adb shell monkey -p com.mytest.dozer -c android.intent.category.LAUNCHER 1 # launch it
+ sleep 10
+ adb shell input keyevent KEYCODE_HOME # send it to the background
+ sleep 2
+ adb shell svc data disable # disable mobile data or do this manually
+ sleep 5
+ adb shell dumpsys deviceidle force-idle # bring device in deep sleep
+ sleep 1
+ adb shell svc data enable # re-enable mobile data or do this manually
+ sleep 5
+ adb shell dumpsys deviceidle unforce # bring device back from deep sleep
+ sleep 1
+ adb shell monkey -p com.mytest.dozer -c android.intent.category.LAUNCHER 1 # bring app back

4. Press the "Network Check" button again. You'll get an alert about "Forcing into online mode", and press it again, and the network state will be reported as "noneNo". 

To have the network plugin actually report that the device is online, you have to essentially put the device/emulator in airplane mode and take it back out.

