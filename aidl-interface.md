# Documentation of SIMS AIDL interfaces v(1.0.3) #
Package name: _com.cootf.sims_

This documentation is about the interface for communication of CPE and SIMS application.  

## 1. Interfaces ##

### 1.2.1 ISimsAppService interface ###
Package name: _com.cootf.sims.costmatrix.ISimsAppService_

This interface allows IPC communication between client and server. It allows to communicate sims hosted status and the available cost matrix plan.

**Public methods**

| Return Type | Functions |
| --- | --- |
| void  | ```isHosted(String iccId, int mnc, int mcc, String simType)``` |
|| Hosted staus of the app |
| void  | ```getOrderingCost(int type, String destination)``` |
|| Current Cost Martix of user's Voice/ Data/ SMS plans sorted by the cost according to ascending order. |
| void  | ```getQueryBalance()``` |
|| Includes balance of McWill account and balance of physical card. |
| void  | ```getTrafficFlow(long startDate, long endDate)``` |
|| Includes Traffic flow of Mcwill、phyical card and cloud card. |
| void  | ```getHostedCards(String uid)``` |
|| Hosted card list with uid. |
| void  | ```getSimInfoList()``` |
|| Async call for request query sim list. |
| void  | ```registerCallback(ISimsAppCallback cb)``` |
|| Register ISimsAppCallback function. |
| void  | ```unregisterCallback(ISimsAppCallback cb)``` |
|| Unregister ISimsAppCallback function. |
