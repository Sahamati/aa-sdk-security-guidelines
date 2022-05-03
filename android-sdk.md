---
description: Guidelines for creating a secure Android based SDK
scope: Consent approval, rejection and revocation.
---

# Android SDK

## AA information security guidelines
1. ### User Information protection
   The below list of information should not be accessible to the FIU app.
   - User's account discovery information
   - Number of discovered/undiscovered accounts  
2. ### Request traceability
   All requests coming from the AA SDK should be traceable to the FIU and the user.
3. ### Authentication
    The FIU should be able to prove their identity to the AA before starting the consent flow. Either via OAuth or TOTP ([Time-based OTP](https://en.wikipedia.org/wiki/Time-based_one-time_password)) based authentication.
4. ### Authorized access to consents  
   The SDK should be allowed to access only consents that are created by the FIU using the SDK.

## General Guidelines

### 1. Run the SDK out of the process to ensure SDK does not share memory space with the FIU app
**Type**: Optional  
To achieve this, the SDK should clearly define the activities that can be invoked by the FIU app, for example, an activity to capture login information and another activity to capture the debit card details. The FIU app should then include these activities in its manifest file under a separate process as shown in the snippet below:

```
<application
   ...
   <activity android:name=".MainActivity">
   ...   	
   </activity>
   <activity
       android:name="com.aa.sdk.Login"
       android:process=":aasdk"
   />
</application>
```

Notes:&#x20;

1. For inter-activity (process) communication, “Intent” should be used to pass data to the SDK activity by the FIU app.&#x20;
2. FIU app can start the activity by invoking the “startActivity()” method when the activity is not expected to return any data to the calling app.&#x20;
3. FIU app can start the activity by invoking the “startActivityForResult()” method when the activity is expected to return any data to the calling app.

### **2. The SDK should use a custom/virtual keyboard and should not rely on system keyboards to capture sensitive information such as PIN **
**Type**: Optional  
*Uf the information being entered is not sensitive. Example: OTP is temporary.*

The custom keyboard can be a number only or a regular keyboard based on what information needs to be captured. This will ensure any keylogging attacks can be successfully thwarted. We see similar methods being used in banking apps like SBI YONO and UPI apps like GPay and BHIM to capture login information and **UPI **PIN.

### 3. The activities exposed by the SDK should disable capturing of screenshots taken either manually or programmatically.

**Type**: Mandatory  
*This is to protect sensitive information to be captured by any outside process. Especially parts which show personal info and bank account information*

To prevent screen capturing, android provides enough security to block either screenshot or recording for that particular activity by setting the “[FLAG\_SECURE](https://developer.android.com/reference/android/view/WindowManager.LayoutParams#FLAG\_SECURE)” attribute in the activity window. The FLAG\_SECURE attribute will also exclude the activity view from google assistant, read more about it [here](https://developer.android.com/training/articles/assistant#excluding\_views)**.**

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    getWindow().setFlags(WindowManager.LayoutParams.FLAG_SECURE,
            WindowManager.LayoutParams.FLAG_SECURE);
    setContentView(R.layout.activity_main);
}
```

### 4. Make activities exposed by the SDK private so that they can’t be launched by any other application other than the FIU app that embeds it.
**Type**: Mandatory

### 5. Make sure your release artifact doesn’t contain any logs that output sensitive information.
**Type**: Mandatory

### 6. Prevent reverse engineering of the SDK code.
**Type**: Optional  
*Unless your SDK has proprietary code, one need not "hide" the code. This helps in discovery of vulnerabilities and quicker resolution.*

Hackers can try to reverse engineer the **SDK** code to find any vulnerabilities in the code or to modify its functionality or search for sensitive data hardcoded in the code.

One way to raise the bar manyfold is by obfuscating the code of SDK. Find more details about it [here](https://developer.android.com/studio/build/shrink-code).

Another option is using [NDK](https://developer.android.com/studio/projects/install-ndk) one can write sensitive portions of the code in C/C++ and compile it natively into .so files, which are much less likely to be decompiled than APKs. Reverse engineering would require the attacker to be proficient in processor architecture, assembler language, JNI Conventions, and Compiler ABI giving even a proficient attacker a few sleepless nights.

For obfuscating any string resources or an asset file, use a library like [StringCare](https://github.com/StringCare/AndroidLibrary/wiki/What-is-StringCare).

### 7. While storing sensitive information in variables in code like the PIN entered by the user, do not use String data type (when coding in java) instead, use a char/byte array and clear the array once it's not required anymore.
**Type**: Mandatory

### 10. Do not broadcast sensitive information using implicit intent.
**Type**: Mandatory  
1. Use explicit intent to restrict receivers(If implicit intent is used, launch app chooser)
2. You can also safely restrict the broadcast to a single application by using `Intent.setPackage()`
3. Use LocalBroadcast Manager instead of `Context.SendBroadcast` so that you don’t have to worry about leaking private data.

### 11. Store data safely
**Type**: Mandatory  
1. Store private data within internal storage. By default, files that you create on internal storage are accessible only to your app. Android implements this protection, and it's sufficient for most applications.
2. Store only non-sensitive data in cache files
3. Use SharedPreferences in private mode(MODE\_PRIVATE)
4. Don't store sensitive information using external storage.
5. Don't store sensitive information using external storage. Files created on external storage, such as SD cards, are globally readable and writable. Because external storage can be removed by the user and also modified by any application, don't store sensitive information using external storage.

## Best practices

### 1. Keep resources private because all public resources are accessible outside your library.
Make sure your public resources names are also unique to prevent resource merge conflicts.

### 2.How to ensure AA SDK is running out of the process

An android application can have multiple activities and each activity can potentially run in a different process. Assume a**n** FIU app has 3 activities `fiu-01`, `fiu-02` and `fiu-03` and an AA SDK having two activities `aa-01` and `aa-02`. What needs to be checked is that "aa-xx" activities have an exclusive process of their own. Following APIs should be helpful to check this programmatically in the AA SDK.

1. Get all activities for an application and the name of the process that hosts that activity
2. Get all processes (PID and process-name) for an application
3. Get current process id.

Using information from 1 and 2, we can build a dictionary of \[pid to List] . Using information provided by 3, we can find the process id for the current aa activity. Using this PID of aa activity, we can look up the dictionary to find the list of activity-names running in that process and ensure that non-aa activities are not running in this process.

```
public class AA_Activity extends AppCompatActivity {
    // ...
    public static void printAllRunningActivities(Context context) {
        try {
            PackageInfo pi = context.getPackageManager().getPackageInfo(
                    context.getPackageName(), PackageManager.GET_ACTIVITIES);
            ArrayList<ActivityInfo> activities = new ArrayList<>(Arrays.asList(pi.activities));
            for(ActivityInfo a : activities){
                System.out.println("activity name" + a.name);
                System.out.println("activity process name" + a.processName);
            }

        } catch (PackageManager.NameNotFoundException e) {
            // handle errors
        }
    }
}
```

Refer to the [API Doc](https://developer.android.com/reference/android/content/pm/PackageManager#getPackageInfo\(java.lang.String,%20int\)).

```
public static void printAllAppProcesses(){
    ActivityManager am = (ActivityManager) getSystemService(ACTIVITY_SERVICE);
    List<ActivityManager.RunningAppProcessInfo> processes =  am.getRunningAppProcesses();
    for (ActivityManager.RunningAppProcessInfo process : processes) {
        System.out.println("Process id" + process.pid);
        System.out.println("Process name " + process.processName);
    }
}
```

Refer to [API Doc](https://developer.android.com/reference/android/app/ActivityManager#getRunningAppProcesses\(\)).

```
public static void printCurrentProcessId(){
    System.out.println("Current Process id"+ android.os.Process.myPid());
}
```

Refer to the [API Doc](https://developer.android.com/reference/android/os/Process#myPid\(\)).

### Further Reads

1. Android process and application lifecycle: [https://developer.android.com/guide/components/activities/process-lifecycle](https://developer.android.com/guide/components/activities/process-lifecycle)
2. Android activity lifecycle: [https://developer.android.com/reference/android/app/Activity](https://developer.android.com/reference/android/app/Activity)

## **References**

1. ****[**https://developer.android.com/topic/security/best-practices**](https://developer.android.com/topic/security/best-practices)****
2. ****[**https://www.jssec.org/dl/android\_securecoding\_en.pdf**](https://www.jssec.org/dl/android\_securecoding\_en.pdf)****
3. ****[**https://wiki.sei.cmu.edu/confluence/display/android/Android+Secure+Coding+Standard**](https://wiki.sei.cmu.edu/confluence/display/android/Android+Secure+Coding+Standard)****
