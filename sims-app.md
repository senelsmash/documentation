# SIMS Documentation #

## What is this repository for? ##

* Sims Android application.
* IDE: Android Studio 3.0.1
* App Name: Sims
* Package Name: com.cootf.sims

This is a background application that enables to manage the user's secondary sim card. The main functions of the sims are,

    1 User subscription
    2 GCM integration for Remote wakeup
    3 Query mobile devices (USSD/ SMS query)
    4 USSD conversation support
    5 Read the usage of SMS/ Data/ Voice
    6 Sync CDR location
    7 Reduce CDR bite size (binary conversion)
    8 Sync CDR with server
    9 Link/Unlink sim
    10 Repeated update cost matrix
    11 Pick server URL by country
    11 Sim card pushing mechanism
    12 AIDL remote service for csim
    13 AIDL remote service for cpe
    14 Download sim card

## How do I get set up? ##

### Summary of set up ###

* Android Studio v3.0.1
* Gradle v3.0.1
    
### Configuration ###

Clone sims project

``git clone git@bitbucket.org:techleadintl/sims-app.git``

Clone sub-modules

```

   [submodule "sims-android-common"]
           path = sims-android-common
           url = git@bitbucket.org:techleadintl/sims-android-common.git
   [submodule "socket-io"]
           path = socket-io
           url = https://github.com/CharithaRatnayake/socket-io.git
   [submodule "sims-aidl-lib"]
           path = sims-aidl-lib
           url = git@bitbucket.org:techleadintl/sims-aidl-lib.git
           
```

### Dependencies ###


```

implementation 'com.android.support.constraint:constraint-layout:1.1.0'
implementation fileTree(include: ['*.jar'], dir: 'libs')
implementation 'junit:junit:4.12'
implementation 'org.hamcrest:hamcrest-library:1.3'
implementation 'com.android.support.test:runner:1.0.1'
implementation 'com.android.support.test:rules:1.0.1'
implementation('com.squareup.retrofit2:retrofit:2.3.0') {
    // exclude Retrofit’s OkHttp peer-dependency module and define your own module import
    exclude module: 'okhttp'
}
implementation 'com.google.code.gson:gson:2.8.0'
implementation 'com.squareup.okhttp3:okhttp:3.8.0'
implementation 'com.squareup.okhttp3:logging-interceptor:3.5.0'
implementation 'com.squareup.retrofit2:converter-gson:2.1.0'
implementation 'com.squareup.retrofit2:adapter-rxjava:2.1.0'
implementation 'com.android.support:cardview-v7:26.0.1'
implementation 'com.android.support:appcompat-v7:26.0.1'
implementation 'com.android.support:support-v4:26.0.1'
implementation 'com.android.support:design:26.0.1'
implementation 'com.android.support:support-vector-drawable:26.0.1'
implementation 'com.daimajia.easing:library:2.0@aar'
implementation 'com.daimajia.androidanimations:library:2.3@aar'
implementation 'org.greenrobot:eventbus:3.1.1'
implementation 'com.wang.avi:library:2.1.3'
implementation 'com.dualcores.swagpoints:swagpoints:1.0.1'
implementation 'com.evernote:android-job:1.2.1'
implementation 'de.mindpipe.android:android-logging-log4j:1.0.3'
implementation 'log4j:log4j:1.2.17'
implementation 'com.google.android.gms:play-services-gcm:11.0.4'
implementation 'com.googlecode.libphonenumber:libphonenumber:8.9.2'
implementation 'com.googlecode.libphonenumber:geocoder:2.91'
implementation 'com.googlecode.libphonenumber:prefixmapper:2.91'
implementation files('libs/carrier-1.83-SNAPSHOT.jar')
implementation project(':simsAndroidCommon')
implementation (project(':socketIo')){
    exclude group: 'org.json', module: 'json'
}
implementation project(':sims-aidl-lib')

```

## Config live apk ##

jks Password: ^VJDr,A2&vzbP&;2
Alias: sims

**SIMS version**

| Version Code | Version Name | Release note |
| --- | --- | --- |
|4| ```0.2.3```  | Link multiple GSM sim cards |
|5| ```0.2.5```  | SIMS moved to ```system/app``` |
|5| ```0.2.6```  | New UID/ McWill number request method |
|6| ```0.2.7```  | SIMS moved to ```system/priv-app``` folder and sign by system folder |

**SIMS app api interface documentation**

This documentation is about the api interface of SIMS app and sims-notify-service.

Dependencies: socket.io[https://socket.io/]
Protocol: websocket, polling
Path: /sims-notify/socket.io
SSL: enabled

| # | End points |
| --- | --- |
| 1 |```connect```|
| 2 |```connected```|
| 3 |```register```|
| 4 |```disconnect```|
| 5 |```reconnect_attempt```|
| 6 |```connect_error```|
| 7 |```transport```|
| 8 |```onError```|
| 9 |```new message```|
| 10 |```dbPush```|
| 11 |```isLink```|

#### 1. Connect ####
- Function in SIMS socket manager.
- Function is used to connect to socket and establish a connection with the server.
- Used to establish/Re-establish a connection.

```    public void connect(){
        LogUtils.d(mContext, TAG, "Socket : " + mSocket.id());
        if(mSocket != null && !mSocket.connected()){
            LogUtils.d(mContext, TAG, "Try to connect socket.");
            mSocket.connect();
            //authentication with token
        }else{
            LogUtils.d(mContext, TAG, "Socket is connected.");
        }
    }
```

| Return Type | Functions |
| --- | --- |
| void  | ```connect()``` |
|| Device connecting function to SIMS. |

##### Usage #####
- Function is called when user register/login to SIMS.
- Function is called in wake-up using GCM Token.
- Function is used when reseting the connection.

#### 2. connected ####
- Listener in SIMS socket manager.
- After a user successfully connect to SIMS system/server ```onConnect``` listener will return **link** , **unlink** , **simStatus** and recent **cost matrix details** .

```
private Emitter.Listener onConnected = new Emitter.Listener() {
        @Override
        public void call(Object... args) {
            LogUtils.i(mContext, TAG, "onConnected");
            LoginResponseEvent event =  new Gson().fromJson(args[0].toString(), LoginResponseEvent.class);
            if(event == null)return;
            LogUtils.i(mContext, TAG, "onConnected response: " + event.toString());
            ModemManager.getInstance().setOperators(mContext, event.getLinks());
            EventBus.getDefault().post(event);
            if(mListener != null)mListener.onConnect(args);
            SocketConnection.sendConnect();
        }
    };
```
| Return Type | Functions |
| --- | --- |
| ```LoginResponseEvent``` | ```onConnected``` |

##### LoginResponseEvent #####
**Parameters**

| Parameter | Description |
| --- | --- |
| ```List<Link> links``` | List of Sims currently registerd to SIMS system under the same UID |

  
##### Link #####
**Parameters**

|Data type| Parameter | Description |
| --- | --- | --- |
| ```Boolean``` | ```isLink```  | is the selected sim linked or unlinked from system |
| ``` long``` | ```lupTimeStamp```  | Last updated timestamp |
| ``` long``` | ```registeredDate```  | sim regustered date |
| ``` boolean``` | ```isDefault```  | is selected sim default sim |
| ``` boolean``` | ```isActive```  | is selected sim currently active |
| ``` String``` | ```simId```  | Sim ID of the sim |
| ``` int``` | ```slotId```  | Slot ID of the sim |
| ``` int``` | ```slotType```  | Slot Type of the sim |
| ``` boolean``` | ```hotSwap```  | is sim able to hot swap |
| ``` int```| ```mnc```  | MNC of sim |
| ``` int```| ```mcc```  | MCC of sim |
| ``` String```| ```uid```  | UID of sim |

#### 3. register ####
- Function in SIMS socket manager that enables to register a device (UID) to the SIMS system.
- Used first time the app is opened to register a device under a UID.

![Activity diagram - register and login](https://bytebucket.org/techleadintl/sims-app/raw/9030dac7f74ea4ddb7b6c1f849da38705f9289a3/resources/Register%20-%20ad.png?token=f32ed98e0346c88077e99fc31fe7848727bd3c3d)

```    public void register(Telephony telephony){
        LogUtils.d(mContext, TAG, "register emit: " + telephony.toString());
        mSocket.emit(AppConstants.Socket.KEY_REGISTER, SocketEmitter.getJsonObj(telephony), new Ack() {
            @Override
            public void call(Object... args) {
                if(args[0] == null)return;
                RegisterResponseEvent event =  new Gson().fromJson(args[0].toString(), RegisterResponseEvent.class);
                ModemManager.getInstance().setOperators(mContext, event.getLinks());
                if(event == null)return;
                LogUtils.i(mContext, TAG, "register response: " + event.toString());
                EventBus.getDefault().post(event);
            }
        });
    }
```

| Return Type | Functions |
| --- | --- |
| void  | ```register(Telephony telephony)``` |
|| Device registering function to SIMS. |

##### Telephony #####
**Parameters**

|Data type| Parameter | Description |
| --- | --- | --- |
| String | ```deviceID```  | returns IMEI for GSM|
| String | ```lineOneNumber```  | the number of the SIM |
| String | ```simSerialNumber```  | serial number of the sim |
| String | ```deviceSoftwareVersion```  | Returns the software version number for the device, for example, the IMEI/SV for GSM phones. |
| String | ```networkCountryIso```  | Returns the ISO country code equivalent of the MCC (Mobile Country Code) of the current registered operator, or nearby cell information if not registered. |
| String | ```networkOperator```  | Returns the numeric name (MCC+MNC) of current registered operator. |
| String | ```simCountryIso```  | Returns the ISO country code equivalent for the SIM provider's country code. |
| String | ```networkOperatorName```  | Returns the alphabetic name of current registered operator. |
|| ```simOperator```  | Returns the MCC+MNC (mobile country code + mobile network code) of the provider of the SIM. 5 or 6 decimal digits.|
| String | ```simOperatorName```  | Returns the Service Provider Name (SPN). |
| String | ```voiceMailAlphaTag```  | Retrieves the alphabetic identifier associated with the voice mail number. |
| String | ```voiceMailNumber```  | Returns the voice mail number. Return null if it is unavailable. |
| String | ```imei```  | McWill UID |
| String | ```deviceImei```  | Returns the IMEI (International Mobile Equipment Identity). Return null if IMEI is not available. |
| String | ```primaryPhoneNumber```  | McWill phone number |
| String | ```mcc```  | mcc number |
| String | ```mnc```  | mnc number |
| String | ```subscriberId```  | Returns the unique subscriber ID, for example, the IMSI for a GSM phone. Return null if it is unavailable. |
| String | ```deviceToken```  | device token |
| String | ```networkConnectionType```  | Network connected medium |
| String | ```versionName```  | SIMS app version name |
| int | ```versionCode```  | SIMS app version code|
| String | ```password```  | generated password |


###### onRegister Listener ######
```
private Emitter.Listener onRegister = new Emitter.Listener() {
        @Override
        public void call(Object... args) {
            if(args[0] == null)return;
            LoginResponseEvent event =  new Gson().fromJson(args[0].toString(), LoginResponseEvent.class);

            if(event == null)return;
            ModemManager.getInstance().setOperators(mContext, event.getLinks());
            LogUtils.i(mContext, TAG, "subscribe response: " + event.toString());
            EventBus.getDefault().post(event);
        }
    };
```
Same as onConnect returns a list of SIMS if available **(LoginResponseEvent)**.

#### 4. disconnect ####
- Function in SIMS socket manager.
- Function is used to disconnect from socket.

```
    public void disconnect(){
        if(mSocket != null && mSocket.connected()){
            {
                LogUtils.d(mContext, TAG, "Socket: socket shutdown gracefully");
                mSocket.disconnect();
            }
        }
    }
```

| Return Type | Functions |
| --- | --- |
| void  | ```disconnect()``` |
|| Device disconnecting function to SIMS. |

** onDisconnect listener **
```
    private Emitter.Listener onDisconnect = new Emitter.Listener() {
        @Override
        public void call(Object... args) {
            LogUtils.e(mContext, TAG, "onDisconnect: "
                    + new Gson().toJson(args));
            if (mListener != null) mListener.onDisconnect(args);

            SocketConnection.sendDisconnect();
        }
    };
```

#### 5. reconnect_attempt ####
- used to reconnect to the system automatically
- it is a socketIO function

** onReconnect listener **
```
    private Emitter.Listener mOnReconnect = new Emitter.Listener() {
        @Override
        public void call(Object... args) {
            LogUtils.i(mContext, TAG, "mOnReconnect");
        }
    };
```

#### 6. connect_error ####
- if connection failed due to any reason it will be caught in onConnectError which listen for a possible connection error

In onConnectError listener if there is a connection error it will disconnect the current connection and close the socket.

Common errors : ** Request Register ** , ** Request Login ** , ** message not found ** and ** Unknown server error**

#### 7. transport ####
- under transport call data such as ** token, data, uid and matrixVersion ** will be communicated to the server.
- data is basically the telephony object.
- matrixVersion is used to find if there are any available updates for cost matrix.

```
private Emitter.Listener onTransport = new Emitter.Listener() {
        @Override
        public void call(Object... args) {
            Transport transport = (Transport)args[0];
            transport.on(Transport.EVENT_REQUEST_HEADERS, new Emitter.Listener() {
                @Override
                public void call(Object... args) {
                    @SuppressWarnings("unchecked")
                    Map<String, List<String>> headers = (Map<String, List<String>>)args[0];
                    // modify request headers
                    Telephony telephony = Helper.getDeviceDetails(mContext);
                    headers.put("token", Arrays.asList(Helper.md5("com.cootf.sims")));
                    headers.put("data", Arrays.asList(new Gson().toJson(telephony)));
                    headers.put("uid", Arrays.asList(Helper.getUID(mContext)));
                    headers.put("matrixVersion", Arrays.asList(String.valueOf(SessionManager.getInstance(mContext).getMatrixKey())));
//                    LogUtils.i(mContext, TAG, "onTransport: " + headers.toString());
                }
            });
            transport.on(Transport.EVENT_RESPONSE_HEADERS, new Emitter.Listener() {
                @Override
                public void call(Object... args) {
                    @SuppressWarnings("unchecked")
                    Map<String, List<String>> headers = (Map<String, List<String>>)args[0];
                }
            });
        }
    };
```
#### 8. new message ####

onNewMessage listens for any new messages from the server, these messages are queries sent from the server
example: check sim balance, self num etc.

![Activity diagram - wake up device](https://bytebucket.org/techleadintl/sims-app/raw/9030dac7f74ea4ddb7b6c1f849da38705f9289a3/resources/wake%20up%20-%20ad.png?token=9fb61928b18b480429fee285d70f516cfbdecb0c)

![Activity diagram - send message](https://bytebucket.org/techleadintl/sims-app/raw/9030dac7f74ea4ddb7b6c1f849da38705f9289a3/resources/send%20message%20-%20ad.png?token=830c31b834d4e1286b66461478e71f158e13f182)

```
 private Emitter.Listener onNewMessage = new Emitter.Listener() {
        @Override
        public void call(Object... args) {
            LogUtils.i(mContext, TAG, "onNewMessage");
            //Init callback function to send the acknowledgment
            setAck((Ack) args[args.length - 1]);
            mSyncTimeout.cancel();
            mSyncTimeout.start();
            ComposerNotification notification = new Gson().fromJson(args[0].toString(),
                    ComposerNotification.class);

            for(Command command : notification.commands){
                if(command.getResponseType() == Command.ResponseType.ECHO){
                    SocketConnection.sendEcho();

                    LogUtils.i(mContext, TAG, command.toString());
                    Response response = new Response();
                    response.imei = Helper.getUID(mContext);
                    response.status = "ECHO";
                    response.processId = notification.getProcessId();
                    if(mSocket != null && getAck() != null)
                        getAck().call(AppConstants.Socket.KEY_ECHO, new Gson().toJson(response));
                }
            }

            EventBus.getDefault().post(notification);
        }
    };
```

**ComposerNotification Parameters**

|Data type| Parameter | Description |
| -- | --- | --- |
| long  | ```processId```  | query process id |
| long | ```sbcProcessId```  | query sbs id |
| Command | ```List<Command> commands```  | list of commands |

**Command Parameters**

|Data type| Parameter | Description |
| -- | --- | --- |
| long | ```groupId```  | query process id |
| long | ```sbsTransactionId```  | Sbs transaction id |
| String | ```message```  | message |
| String | ```serviceNumbers[]```  | array of service numbers |
| String | ```title```  | title |
| String | ```action```  | action |
| String | ```conversationId```  | ussd conversation ID |
| String | ```type```  | message type |
| long| ```version```  | version |
| String | ```time```  | time of message |
| CostMatrix | ```List<CostMatrix> costMatrixList```  | costmatrix list |
| enum | ```responseType```  | Type of response types [SMS_BALANCE, USSD, RELOAD, ECHO, ALERT, MATRIX]|

**Cost Matrix Parameters**

| Data type | Parameter | Description |
| --- | --- | --- |
| long | ```matrixID```  | Matrix id |
| double | ```cost```  | Matrix cost |
| double | ```amount```  | Available resource amount.  |
| long | ```validTime```  | valid time of resource |
| long | ```matrixMethodID```  | method ID |
| long | ```matrixTypeID```  | matrix type id |
| long | ```matrixOperatorID```  | matrix operator id |
| long | ```createdAt```  | created date resource |
| long | ```updatedAt```  | updated date resource |
