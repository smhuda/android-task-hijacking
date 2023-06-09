# Android Task Hijacking - Proof of Concept

# Android Task Hijacking

### Background

There are four different Launch Modes:

1. standard
2. singleTop
3. singleTask
4. singleInstance

For the attack described here, we are mostly concerned with the “**singleTask**” mode.

One of the possibility with “**singleTask**” activity is it allows other activities to be part of its task. It’s always at the root of its task, but other activities (necessarily “standard” and “singleTop” activities) can be launched into that task.

**Task affinity** is an attribute that is defined in each `<activity>` tag in the `AndroidManifest.xml` file. It describes which Task an Activity prefers to join.\
By default, every activity has the same affinity as the **package** name.

### Grep for singletask to check if Vulnerability exists

<pre>
<code>
apktool d com.example.app
cd com.example.app
grep -r singleTask .                                                                                  
</code>
</pre>

If we find the activity whose launchMode is set to **singleTask** then we can hijack the task as it is vulnerable.

### Attack and POC

We need to create a malicious application to exploit this vulnerability

You can import the below POC in your Android Studio projects and replace the package name with you desired Victim Application package name in AndroidManifest.xml as follows:

```
android:taskAffinity="com.example. VICTIMAPPPackage"

```

<figure><img src="/screenshots/Screenshot 2023-03-15 at 12.33.55.png" alt=""><figcaption></figcaption></figure>

Now save and run the project, Android Studio will install and run the application on the Android device physially connected to your machine.

<figure><img src="/screenshots/Screenshot 2023-03-15 at 12.41.41.png" alt=""><figcaption></figcaption></figure>

If you prefer an APK you can follow the step in the screenshots below to build an APK to install on another device or emulator:

<figure><img src="/screenshots/Screenshot 2023-03-15 at 12.41.55.png" alt=""><figcaption></figcaption></figure>

* Now, when the user opens the attacker’s app. it immediately minimises the task.
* It will not be shown in the **recent apps** as well.
* After that, when the user opens the victim app and presses the back button, instead of being taken to home screen. he is taken to the attacker’s application.

Thanks to the **taskAffinity** mentioned by the attackers app which is set to the victims app.

Task hijacking is also known as **StrandHogg** vulnerability**.**

### **Remediation**

* Set the launchMode to **singleInstance** which will prevent other activities from becoming a part of it’s task.
* A custom **onBackPressed()** function can also be added, to override the default behaviour.
* Setting `taskAffinity=""` can be a quick fix for this issue.

# Credits for reference material
- <https://github.com/az0mb13/Task_Hijacking_Strandhogg>
- <https://docs.fluidattacks.com/criteria/vulnerabilities/347/#non-compliant-code>

