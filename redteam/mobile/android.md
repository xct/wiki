# Android

## Mobile Android

### Setup

Windows VM with Genymotion \(make personal use account\) & Android studio

In Android Studio install command line tools under Tools -&gt; SDK Manager

```text
setx PATH "%PATH%;C:\Users\xct\AppData\Local\Android\Sdk\platform-tools"
C:\Users\xct\AppData\Local\Android\Sdk\platform-tools\adb.exe
```

### ADB

```bash
adb connect <ip>
adb devices
adb shell (get shell on device)
adb push/pull <filename> <targetpath>
adb install <name of app>
am start -a android.intent.action.VIEW http://google.com (start action via activity manager)
adb shell ps
adb logcat | grep -I <psid from above>
```

### Handling APKs

#### Unpacking

```bash
unzip -d
apktool d <apk>
dexdump to dump .dex files or 010 Editor with templates
```

#### Reverse

* bytecode-viewer

#### Repacking

```bash
apktool b folder -o name.apk
```

#### Signing

```bash
jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore my_release_key.keystore name.apk alias_name
```

### Proxy

#### Set Proxy

Androidwifi \(long press\), Modify -&gt; Proxy

#### Import Burp Certificate

```bash
openssl x509 -inform DER -in cacert.der -out cacert.pem
openssl x509 -inform PEM -subject_hash_old -in cacert.pem |head -1
mv cacert.pem <hash>.0
adb push path /sdcard/
adb remount
mv /sdcard/9a5ba575.0 /system/etc/security/cacerts
chmod 644 on that file
adb reboot
```

### ARM in Genymotion

* [https://pentester.land/tips-n-tricks/2018/10/19/installing-arm-android-apps-on-genymotion-devices.html](https://pentester.land/tips-n-tricks/2018/10/19/installing-arm-android-apps-on-genymotion-devices.html)

### Frida

```bash
adb push frida-server /data/local/tmp/frida-server
adb shell chmod 777 /data/local/tmp/frida-server
adb shell /data/local/tmp/frida-server &
```

#### Bypassing [SSL-Pinning](https://medium.com/@ved_wayal/hail-frida-the-universal-ssl-pinning-bypass-for-android-e9e1d733d29)

Use Genymotion & Setup Burp + Proxy as usual \(save burp ca-cert locally as well\).

```text
pip install Frida objection frida-tools
```

Download the appropriate Frida server [release ](https://github.com/frida/frida/releases/)\(probably x86\). Also download this script: pcipolloni/universal-android-ssl-pinning-bypass-with-frida and store it as fridascript.js, then:

```text
adb push frida-server /data/local/tmp
adb shell chmod 777 /data/local/tmp/frida-server
adb push cacert.der /data/local/tmp/cert-der.crt
adb push fridascript.js /data/local/tmp
adb shell /data/local/tmp/frida-server &
```

Run ps and note the app name you want to bypass it for:

```text
frida-ps -U
```

Finally:

```text
frida -U -f com.twitter.android --codeshare pcipolloni/universal-android-ssl-pinning-bypass-with-frida --no-pause
```

### Checklist

* unzip -d and apktool -d both \(different outputs at time\)
* check for /assets and /res/raw \(api keys, encryption keys\)
* sensitive files and external storage \(world readable  & writeable\)
* executables & log files on external storage
* look at manifest \(WRITE\_EXTERNAL\_STORAGE\)., grep for "getExternal"
* check for installed package "vnd.android.package-archive" \(they want to install something\)
* hidden directories \(.folder\)
* api keys saved as bytearray to obfuscate
* identify crypto & understand it
* webSettings.setJavaScriptEnabled\(True\); means we might be able to XSS
* interesting options: "setAllowContent", "setAllowFileAccess", "setAllowFileAccessFromFileURILS", "setAllowUniversalAccessFromFileURLs", "setJavaScriptEnabled", "setPluginState", "setSavePassword"
* overwriting ssl errors :facepalm:
* xss might allow to call Runtime.getRuntime\(\).exec\(\) \(CVE-2012-6636\) &lt;= Api17
* use Mitm Proxy \(mitm.it has the cert\)

