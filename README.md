## HOW TO BUILD

```
tp

tp_group_add kvm
tp_apt_i openjdk-8-jdk
SUITEDIR=$(cchs "echo git@github.com:stupidsing/suite.git" @git-clone "@git-cd pwd")

#tp_android_avdmanager create avd -n polly -k 'system-images;android-27;default;x86_64'
#tp_android_avdmanager delete avd -n polly
#tp_android_emulator -avd polly

#keytool -genkey -v -keystore lines_5-mobileapps.keystore -alias lines_5-mobileapps -keyalg RSA -keysize 2048 -validity 10000

tp_cordova create lines_5
cd ~/lines_5/
tp_cordova platform add android

#ANDROID_SDK_ROOT=$(tp_android_sdk_tools) JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 PATH=${JAVA_HOME}/bin:${PATH} tp_cordova run android

(cd ~/lines_5/ &&
cp ${SUITEDIR}/src/main/html/{five-in-a-row-cordova.html,*.js} www/ &&
ANDROID_SDK_ROOT=$(tp_android_sdk_tools) JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 PATH=${JAVA_HOME}/bin:${PATH} tp_cordova build &&
$(tp_android_sdk_tools)/platform-tools/adb uninstall io.cordova.lines_5 &&
$(tp_android_sdk_tools)/platform-tools/adb install -r platforms/android/app/build/outputs/apk/debug/app-debug.apk &&
scp platforms/android/app/build/outputs/apk/debug/app-debug.apk pointless.online:/var/www/html/storey/ &&
echo DONE)

(cd ~/lines_5/ &&
ANDROID_SDK_ROOT=$(tp_android_sdk_tools) JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 PATH=${JAVA_HOME}/bin:${PATH} tp_cordova build --release &&
jarsigner -digestalg SHA1 -keystore lines_5-mobileapps.keystore -sigalg SHA1withRSA -verbose platforms/android/app/build/outputs/apk/release/app-release-unsigned.apk lines_5-mobileapps &&
$(tp_android_sdk_tools)/build-tools/29.0.3/zipalign -v 4 platforms/android/app/build/outputs/apk/release/app-release-unsigned.apk platforms/android/app/build/outputs/apk/release/app-release.apk &&
$(tp_android_sdk_tools)/platform-tools/adb uninstall io.cordova.lines_5 &&
$(tp_android_sdk_tools)/platform-tools/adb install -r platforms/android/app/build/outputs/apk/release/app-release.apk &&
echo DONE)

(cd ~/lines_5/platforms/android/ &&
ANDROID_HOME=$(tp_android_sdk_tools) ./gradlew :app:bundle &&
tp_android_bundletool build-apks --bundle=app/build/outputs/bundle/debug/app.aab --output=lines_5.apks &&
$(tp_android_sdk_tools)/platform-tools/adb uninstall io.cordova.lines_5 &&
ANDROID_HOME=$(tp_android_sdk_tools) tp_android_bundletool install-apks --apks=lines_5.apks &&
echo DONE)
```
