# Termux e Sketchware java compile-check (no Sketchware run needed)
- `android.jar` API 34: https://raw.githubusercontent.com/Sable/android-platforms/master/android-34/android.jar
- okhttp 3.12.12 + okio 1.15.0 Maven Central jars
- `javac -source 1.8 -target 1.8 -cp android-34.jar:okhttp.jar:okio.jar -d out files/java/*.java` → full syntax/type check
- Class name clashes: `DeviceAdminReceiver` er instead use `MyDeviceAdminReceiver` (conflict with android.app.admin.DeviceAdminReceiver)
- Re-sign pipeline: `remote-build.sh` (auto-picks newest .unsigned.apk, signs devzeron_2026.jks, verifies manifest/signature)
