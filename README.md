Android Text Manager
====================

Our goal is to make sending SMS and MMS easier on the Android OS.


Download
--------
```groovy
dependencies {
  compile 'com.xlythe:android-text-manager:0.0.2'
}
```

Usage
-----
First thing to do is grab an instance of TextManager
```java
TextManager manager = TextManager.getInstance(context);
```

### Reading Messages
To get a list of conversations aka "threads"
```java
List<Thread> threads = manager.getThreads().get();
```

You can also get a cursor of threads
```java
Thread.ThreadCursor cursor = manager.getThreadCursor();
```

With a thread you can get the latest message and get more info from there
```java
Text text = thread.getLatestMessage(context).get();
text.getThreadId()
text.getTimestamp();
text.getBody();
text.getAttachment();
text.sender();
text.getMembersExceptMe(context).get();
// and the list goes on...
```

### Sending Messages
To send a message:
  * use Text Builder to build your message
  * and send using TextManger
```java
manager.send(new Text.Builder()
                .message("HIII!!!!")
                .addRecipient(context, "1234567890")
                .attach(new ImageAttachment(uri))
                .attach(new VideoAttachment(uri))
                .build()
);
```

To reply to a thread of messages given a thread id (This handles group messaging):
  * use TextManager to grab the Thread
  * get the latest message in the thread using getLatestMessage
  * get all the members in the conversation minus yourself
```java
// There are a few ways to do this.
// This example uses callbacks, but you can get all the same data for the Builder from the methods above
Thread thread = manager.getThread(id).get()
thread.getLatestMessage(context).get(new Future.Callback<Text>() {
            @Override
            public void get(Text instance) {
                instance.getMembersExceptMe(context).get(new Future.Callback<Set<Contact>>() {
                    @Override
                    public void get(Set<Contact> instance) {
                        manager.send(new Text.Builder()
                                .message("HIII!!!!")
                                .addRecipients(instance)
                                .build());
                    }
                });
            }
        });
```

### Receiving and Storing Messages
Just extend our TextReceiver
```java
public class MessageReceiver extends TextReceiver {
    @Override
    public void onMessageReceived(Context context, Text text) {
    
    }
}
```
### Permissions
And lastly, but very import PERMISSIONS!
```xml
<uses-permission android:name="android.permission.SEND_SMS" />
<uses-permission android:name="android.permission.SEND_MMS" />
<uses-permission android:name="android.permission.RECEIVE_SMS" />
<uses-permission android:name="android.permission.RECEIVE_MMS" />
<uses-permission android:name="android.permission.READ_SMS" />
<uses-permission android:name="android.permission.WRITE_SMS" />
<uses-permission android:name="android.permission.READ_CONTACTS" />
<uses-permission android:name="android.permission.WRITE_CONTACTS" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_SETTINGS" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_PROFILE" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<!-- Thats a lot and I probably forgot some -->
<!-- You may not need all of these, depending on what you are doing -->
```

Limitations
-----------
* Re-downloading a failed MMS not yet implemented
* Dual sim support not yet added
* Demo app not yet finished


License
--------

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
