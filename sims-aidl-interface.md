# Documentation of SIMS AIDL interfaces v(1.0.2) #
Package name: _com.cootf.sims_

This documentation is about the interface for communication with SIMS application.  

## 1. Interfaces ##

### 1.1 DIALLER ###
1. ISimsAppService interface.
2. ISimsAppCallback interface.
3. CostMatrixObject Object

Note: To retrieve CostMatrix you need to add a permission in your ```AndroidManifest.xml```.

```<uses-permission android:name="com.cootf.sims.matrix.provider.CostMatrixProvider" />```

#### 1.1.1 ISimsAppService interface ####
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

#### 1.1.2 ISimsAppCallback interface ####
Package name: _com.cootf.sims.costmatrix.ISimsAppCallback_

This interface is a callbacks of ISimsAppService.

**Public methods**

| Return Type | Functions |
| --- | --- |
| void  | ```onHosted(String iccId, boolean status)``` |
| void  | ```onOrderingCost(in List<CostMatrixObject> orderingCards)``` |

#### 1.1.3 CostMatrixObject object ####
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
| double | ```amount``` |
|| Available resource amount. |
| double | ```cost``` |
|| Cost per resource. |
| long | ```validTime``` |
|| Cost matrix valid time. |
| long | ```createdAt``` |
|| Data created time. |
| long | ```updatedAt``` |
|| Data updated time. |
| int | ```priceOrder``` |
|| The unique order of the total price about the transaction method. |


**AIDL Source Code**

```git clone git@bitbucket.org:techleadintl/sims-aidl-lib.git```
