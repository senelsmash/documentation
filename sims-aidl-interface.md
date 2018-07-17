# Documentation of SIMS AIDL interfaces v(1.0.2) #
Package name: _com.cootf.sims_

This documentation is about the interface for communication of CPE and SIMS application.  

## 1. Interfaces ##

### 1.1 CPE ###
1. Subscriber interface.
2. SubscriberCallback interface.
3. RegisterResponse Object
4. LinkUnlinkResponse Object

#### 1.1.1 Subscriber interface ####
Package name: _com.cootf.sims.cpe.ISubscriberInterface_

This interface allows IPC communication between client and server. It allows major 
prerequisite functions of SIMS that needed by client(CPE).

**Public methods**

| Return Type | Functions |
| --- | --- |
| void  | ```register(String uid, String mcwillNo)``` |
|| Subscriber register function to sims. |
| void  | ```link(String uid, String simId, int slotId, int slotType, boolean isHotSwap, int mcc, int mnc)``` |
|| Link a sim card. |
| void  | ```unlink(String uid, String simId)``` |
|| Un-link a sim card. |
| void  | ```onHosted(String simId,int mcc, int mnc, int simType)``` |
|| Check the sim card is link/ unlink. |
| void  | ```isLoggedIn()``` |
|| Flag to check user is registered or logged in. |
| void  | ```reset()``` |
|| Reset sims app connections and GCM token. |
| void  | ```registerCallback(SubscriberCallback cb)``` |
|| Register SubscriberCallback function. |
| void  | ```unregisterCallback(SubscriberCallback cb)``` |
|| Unregister SubscriberCallback function. |

**Parameters**

| Parameter | Description |
| --- | --- |
| ```uid```  | UID number(McWill) |
| ```mcwillNo```  | McWill number |
| ```simId```  | Subscriber id(Unique number of sim card id) |
| ```slotId```  | Sim slot id |
| ```slotType```  | Enumeration sims slot types 0 - GSM, 1 - SoftSIM, 2 - CSIM |
| ```isHotSwap```  | Flag to check sim can be change to another sim |
| ```mcc```  | MCC number of sim card |
| ```mnc```  | MNC number of sim card |

#### 1.1.2 SubscriberCallback interface ####
Package name: _com.cootf.sims.cpe.ISubscriberCallbackInterface_

This interface is a callbacks of ISubscriberInterface.

**Public methods**

| Return Type | Functions |
| --- | --- |
| void  | ```register(RegisterResponse response)``` |
| void  | ```link(LinkUnlinkResponse response)``` |
| void  | ```unlink(LinkUnlinkResponse response)``` |
| void  | ```onHosted(String iccId, boolean status)``` |
| void  | ```isRegister(Boolean status)``` |
| void  | ```reset()``` |

#### 1.1.3 RegisterResponse object ####
Package name: _com.cootf.sims.cpe.object.IRegisterResponse_

This interface is a response object of Register function in SubscriberCallback. This object need to be parcelable.

**Public methods**

| Data Type | Functions/ Variable |
| --- | --- |
| List<Link> | ```simLinks``` |
|| This is the sim card object that used to manage active/ in-active status and get status information regarding the sim. |
| int | ```status``` |
|| ```0``` = Success Response, ```1``` = Error response (Other error codes are defined below). |
| String  | ```message``` |
|| Response message from app api. |

#### 1.1.4 LinkUnlinkResponse object ####
Package name: _com.cootf.sims.cpe.object.ILinkUnlinkResponse_

This interface is a response object of link function and unlink function in SubscriberCallback. This object need to be parcelable.

**Public methods**

| Data Type | Functions/ Variable |
| --- | --- |
| List<Link> | ```simLinks``` |
|| This is the sim card object that used to manage active/ in-active status and get status information regarding the sim. |
| int | ```status``` |
|| ```0``` = Success Response, ```1``` = Error response (Other error codes are defined below). |
| String  | ```message``` |
|| Response message from app api. |

**Error Codes**

```SUBSCRIBER_NOT_FOUND = 1001```

```SUBSCRIBER_INACTIVE = 1002```

```SUBSCRIBER_CHANGED = 1009```

```UNKNOWN_SERVER_ERROR = 1010```

```UNREGISTER_USER_NOT_FOUND = 400```

```USER_EXIST = 1005```

```MCC_NULL = 1003```

```UID_NOT_FOUND = 2000```

#### 1.1.4.1 Link object ####
Package name: _PackageName.object.Link_

**Public methods**

| Data Type | Functions/ Variable |
| --- | --- |
| boolean | ```isDefault``` |
|| If the isDefault is set to ```true``` : then all query and top-ups are run through this sim card. At a time only one sim can be set as default. |
| boolean | ```isActive``` |
|| Link status of the sim card. If the isDefault is ```true``` : all query and top-ups are run through this sim card. |
| String | ```uid``` |
|| UID number(McWill). |
| String | ```simId``` |
|| Sim slot id. |
| int | ```slotType``` |
|| Enumeration sims slot types 0 - GSM, 1 - SoftSIM, 2 - CSIM. |
| boolean | ```isHotSwap``` |
|| Flag to check sim can be change to another sim. |
| int | ```mcc``` |
|| MCC number of sim card. |
| int | ```mnc``` |
|| MNC number of sim card. |

### 1.2 DIALLER ###
1. ISimsAppService interface.
2. ISimsAppCallback interface.
3. CostMatrixObject Object

Note: To retrieve CostMatrix you need to add a permission in your ```AndroidManifest.xml```.

```<uses-permission android:name="com.cootf.sims.matrix.provider.CostMatrixProvider" />```

#### 1.2.1 ISimsAppService interface ####
Package name: _com.cootf.sims.costmatrix.ISimsAppService_

This interface allows IPC communication between client and server. It allows to communicate sims hosted status and the available cost matrix plan.

**Public methods**

| Return Type | Functions |
| --- | --- |
| void  | ```isHosted(String iccId, int mnc, int mcc, String simType)``` |
|| Hosted staus of the app |
| void  | ```getOrderingCost(int type, String destination)``` |
|| Current Cost Martix of user's Voice/ Data/ SMS plans sorted by the cost according to ascending order. |
| void  | ```registerCallback(ISimsAppCallback cb)``` |
|| Register ISimsAppCallback function. |
| void  | ```unregisterCallback(ISimsAppCallback cb)``` |
|| Unregister ISimsAppCallback function. |

**Parameters**

| Parameter | Description |
| --- | --- |
| ```iccId```  | Subscriber id(Unique number of sim card id) |
| ```slotType```  | Enumeration sims slot types 0 - GSM, 1 - SoftSIM, 2 - CSIM |
| ```mcc```  | MCC number of sim card |
| ```mnc```  | MNC number of sim card |
| ```type```  | VOICE = 0, DATA = 1, SMS = 2 |
| ```destination```  | If the type is 0 (voice) destination number is mandatory |

#### 1.2.2 ISimsAppCallback interface ####
Package name: _com.cootf.sims.costmatrix.ISimsAppCallback_

This interface is a callbacks of ISimsAppService.

**Public methods**

| Return Type | Functions |
| --- | --- |
| void  | ```onHosted(String iccId, boolean status)``` |
| void  | ```onOrderingCost(in List<CostMatrixObject> orderingCards)``` |

#### 1.2.3 CostMatrixObject object ####
Package name: _com.cootf.sims.costmatrix.object.CostMatrixObject_

This interface is a response object of CostMatrix function in ISimsAppCallback. This object need to be parcelable.

**Public methods**

| Data Type | Functions/ Variable. |
| --- | --- |
| String | ```methodName``` |
|| Transaction method (Eg. MCWILL, SoftSim, GSM, CT/McWill, CT/SoftSim, CT/GSM, CT/WIFI). |
| String | ```typeName``` |
|| Transaction type. (Eg. CALL/ DATA/ SMS). |
| String | ```operatorName``` |
|| Name of the operator. |
| String | ```amount``` |
|| Available resource amount. |
| String | ```cost``` |
|| Cost per resource. |
| String | ```validTime``` |
|| Cost matrix valid time. |
| String | ```createdAt``` |
|| Data created time. |
| String | ```updatedAt``` |
|| Data updated time. |
