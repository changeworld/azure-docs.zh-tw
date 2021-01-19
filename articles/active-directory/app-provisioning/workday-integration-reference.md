---
title: Azure Active Directory 與 Workday 整合參考
description: 技術深入探討 Workday-HR 驅動的布建
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/18/2021
ms.author: chmutali
ms.openlocfilehash: 251e1d4249373ec52afb3d7edaa2325c992b66f1
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570154"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Azure Active Directory 布建與 Workday 的整合方式

[Azure Active Directory 的使用者](../app-provisioning/user-provisioning.md) 布建服務會與 [Workday HCM](https://www.workday.com) 整合，以管理使用者的身分識別生命週期。 Azure Active Directory 提供三個預先建立的整合： 

* [Workday 到內部部署 Active Directory 使用者布建](../saas-apps/workday-inbound-tutorial.md)
* [Workday 到 Azure Active Directory 使用者布建](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday Writeback](../saas-apps/workday-writeback-tutorial.md)

本文說明整合的運作方式，以及如何針對不同的 HR 案例自訂布建行為。 

## <a name="establishing-connectivity"></a>建立連線能力 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>限制 Azure AD 端點的 Workday API 存取
Azure AD 布建服務會使用基本驗證來連接到 Workday Web 服務 API 端點。  

若要進一步保護 Azure AD 布建服務與 Workday 之間的連線能力，您可以限制存取，讓指定的整合系統使用者只能從允許的 Azure AD IP 範圍存取 Workday Api。 請與您的 Workday 系統管理員合作，在您的 Workday 租使用者中完成下列設定。 

1. 下載 Azure 公用雲端的 [最新 IP 範圍](https://www.microsoft.com/download/details.aspx?id=56519) 。 
1. 開啟檔案，並搜尋標記 **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP 範圍](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. 複製 [元素] *addressPrefixes* 中所列的所有 IP 位址範圍，並使用範圍來建立您的 IP 位址清單。
1. 登入 Workday 系統管理員入口網站。 
1. 存取「 **維護 IP 範圍** 」工作，為 Azure 資料中心建立新的 ip 範圍。 使用 CIDR 標記法) 作為逗點分隔清單，以指定 IP 範圍 (。  
1. 存取 [ **管理驗證原則** ] 工作以建立新的驗證原則。 在 [驗證原則] 中，使用 **驗證** 允許清單來指定 Azure AD ip 範圍，以及允許從這個 ip 範圍存取的安全性群組。 儲存變更。 
1. 存取「 **啟用所有擱置中的驗證原則變更** 」工作以確認變更。

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>使用受限制的安全性群組限制存取 Workday 中的背景工作資料

[設定 workday 整合系統使用者](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday)的預設步驟會授與存取權，以抓取 Workday 租使用者中的所有使用者。 在某些整合案例中，您可能會想要限制存取權，如此一來，只有特定監督組織的使用者會由 Get_Workers API 呼叫傳回，然後由 Workday Azure AD 連接器處理。 

您可以使用 Workday 系統管理員和設定受限制的整合系統安全性群組，來履行這項需求。 如需如何完成此操作的詳細資訊，請參閱 [這篇 workday](https://community.workday.com/forums/customer-questions/620393) 的 (*workday 的登入認證，以存取此文章*) 

使用受限制的 ISSG (Integration System 安全性群組) 來限制存取的這項策略，在下列案例中特別有用： 
* **階段式推出案例**：您有大型 Workday 租使用者，並計畫執行 Workday 的階段式推出，以 Azure AD 自動化布建。 在此案例中，您不會使用 Azure AD 範圍篩選器來排除不在目前階段範圍內的使用者，因此建議您設定限制的 ISSG，讓 Azure AD 只能看見範圍內的背景工作。
* **多個布建作業案例**：您有大型 Workday 租使用者及多個 AD 網域，各支援不同的業務單位/部門/公司。 為了支援此拓撲，您想要執行多個 Workday 來 Azure AD 布建工作，讓每個工作布建一組特定的背景工作。 在此案例中，您不需要使用 Azure AD 範圍篩選器來排除背景工作資料，我們建議您設定受限制的 ISSG，讓 Azure AD 只能看見相關的背景工作資料。 

### <a name="workday-test-connection-query"></a>Workday 測試連接查詢

若要測試 Workday 的連線性，Azure AD 傳送下列 *Get_Workers* Workday Web 服務要求。 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>完整同步處理的運作方式

Workday 驅動布建內容中的 **完整同步** 處理是指從 Workday 提取所有身分識別，以及決定要將哪些布建規則套用至每個背景工作物件的程式。 當您第一次開啟布建時，以及當您從 Azure 入口網站或使用 Graph Api *重新開機* 布建時，就會進行完整同步處理。 

Azure AD 傳送下列 *Get_Workers* Workday Web 服務要求來取得背景工作資料。 此查詢會查詢 Workday 交易記錄中，所有生效日期的背景工作專案，從對應到完整同步執行的時間開始。 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
*Response_Group* 節點用來指定要從 Workday 提取哪些背景工作屬性。 如需 *Response_Group* 節點中每個旗標的說明，請參閱 WORKDAY [Get_Workers API 檔](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)。 

*Response_Group* 節點中指定的特定旗標值會根據 Workday Azure AD 布建應用程式中所設定的屬性來計算。 請參閱 *支援的實體* 上的一節，以取得用來設定旗標值的準則。 

上述查詢的 Workday *Get_Workers* 回應包含背景工作記錄和頁面計數的數目。

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
若要取出結果集的下一頁，下一個 *Get_Workers* 查詢會將頁碼指定為 *Response_Filter* 中的參數。

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Azure AD 布建服務會處理每個頁面，並在完整同步處理期間逐一查看所有有效的背景工作。針對從 Workday 匯入的每個背景工作專案：
* 套用 [XPATH 運算式](workday-attribute-reference.md) 以從 Workday 取出屬性值。
* 套用屬性對應和比對規則， 
* 服務會決定要在目標 (Azure AD/AD) 中執行的作業。 

處理完成後，它會將與「完整同步處理」開始相關聯的時間戳記儲存為浮水印。 此水位線可作為增量同步處理週期的起點。 

## <a name="how-incremental-sync-works"></a>增量同步處理的運作方式

完整同步處理之後，Azure AD 布 `LastExecutionTimestamp` 建服務會維護並使用它來建立 delta 查詢，以抓取增量變更。 增量同步處理期間，Azure AD 會將下列類型的查詢傳送到 Workday： 

* [手動更新的查詢](#query-for-manual-updates)
* [查詢生效日期更新和終止](#query-for-effective-dated-updates-and-terminations)
* [查詢未來的員工](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>手動更新的查詢

下列 *Get_Workers* 要求查詢上次執行和目前執行時間之間發生的手動更新。 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>查詢生效日期更新和終止

下列 *Get_Workers* 要求查詢上次執行和目前執行時間之間發生的生效日期更新。 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>查詢未來的員工

如果上述任何一項查詢傳回未來的雇用日期，則會使用下列 *Get_Workers* 要求來提取新員工的相關資訊。 新雇用的 *WID* 屬性是用來執行查閱，並將生效日期設定為雇用日期和時間。 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="retrieving-worker-data-attributes"></a>正在抓取背景工作資料屬性

*Get_Workers* API 可能會傳回與背景工作關聯的不同資料集。 視布建架構中所設定的 [XPATH API 運算式](workday-attribute-reference.md) 而定，Azure AD 布建服務會決定要從 Workday 取出哪些資料集。 因此， *Response_Group* 旗標會在 *Get_Workers* 要求中設定。 

下表提供有關對應設定的指引，以用來抓取特定的資料集。 

| \# | Workday 實體                       | 預設包含 | 要在對應中指定以提取非預設實體的 XPATH 模式             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | 個人資料                        | Yes                 | wd： Worker \_ data/wd：個人 \_ 資料                                             |
| 2  | 員工資料                      | Yes                 | wd： Worker \_ data/wd：雇用 \_ 資料                                           |
| 3  | 其他工作資料                  | Yes                 | wd： worker \_ data/wd：雇用 \_ 資料/wd：背景工作 \_ 作業 \_ 資料 \[ @wd:Primary \_ 工作 = 0\]|
| 4  | 組織資料                    | Yes                 | wd： Worker \_ data/wd：組織 \_ 資料                                         |
| 5  | 管理鏈資料                | Yes                 | wd： Worker \_ data/wd：管理 \_ 鏈 \_ 資料                                    |
| 6  | 監督組織             | Yes                 | 監督                                                                 |
| 7  | 公司                              | Yes                 | 公司                                                                     |
| 8  | 業務單位                        | No                  | 「營業 \_ 單位」                                                              |
| 9  | 業務單位階層              | No                  | 「業務 \_ 單位階層」 \_                                                   |
| 10 | 公司階層                    | No                  | 「公司 \_ 階層」                                                          |
| 11 | Cost Center                          | 否                  | 「成本 \_ 中心」                                                                |
| 12 | 成本中心階層                | No                  | 「成本 \_ 中心階層」 \_                                                     |
| 13 | 基金                                 | No                  | 募捐                                                                        |
| 14 | 資金階層                       | No                  | 「資金 \_ 階層」                                                             |
| 15 | 禮物                                 | No                  | 禮物                                                                        |
| 16 | 禮物階層                       | No                  | 「禮物 \_ 階層」                                                             |
| 17 | 授與                                | No                  | 准許                                                                       |
| 18 | 授與階層                      | No                  | 「授與階層」 \_                                                            |
| 19 | 商務網站階層              | No                  | 「商務 \_ 網站階層」 \_                                                   |
| 20 | 矩陣組織                  | No                  | 矩陣                                                                      |
| 21 | 付費群組                            | No                  | 「付費 \_ 群組」                                                                  |
| 22 | 程式                             | No                  | 程式                                                                    |
| 23 | 程式階層                    | No                  | 「程式 \_ 階層」                                                          |
| 24 | 區域                               | No                  | 「區域 \_ 階層」                                                           |
| 25 | 位置階層                   | No                  | 「位置 \_ 階層」                                                         |
| 26 | 帳戶布建資料            | No                  | wd： Worker \_ data/wd：帳戶布建 \_ \_ 資料                                |
| 27 | 背景檢查資料                | No                  | wd： Worker \_ Data/wd：背景 \_ 檢查 \_ 資料                                    |
| 28 | 權益資格資料             | No                  | wd： Worker \_ Data/wd：權益 \_ 資格 \_ 資料                                 |
| 29 | 權益註冊資料              | No                  | wd： Worker \_ data/wd：權益 \_ 註冊 \_ 資料                                  |
| 30 | 事業資料                          | No                  | wd： Worker \_ data/wd：事業 \_ 資料                                               |
| 31 | 補償資料                    | No                  | wd： Worker \_ Data/wd：補償 \_ 資料                                         |
| 32 | 臨時的工作者稅務授權單位資料 | No                  | wd： Worker \_ Data/wd：臨時 \_ 工作的 \_ 稅務 \_ 機關 \_ 表單 \_ 類型 \_ 資料       |
| 33 | 開發專案資料                | No                  | wd： Worker \_ data/wd：開發 \_ 專案 \_ 資料                                    |
| 34 | 員工合約資料              | No                  | wd： Worker \_ data/wd： Employee \_ 合約 \_ 資料                                  |
| 35 | 員工評論資料                 | No                  | wd： Worker \_ Data/wd：員工 \_ 評論 \_ 資料                                     |
| 36 | 意見反應已收到資料               | No                  | wd： Worker \_ Data/wd：意見反應 \_ 收到的 \_ 資料                                   |
| 37 | 背景工作的目標資料                     | No                  | wd： Worker \_ Data/wd：工作者 \_ 目標 \_ 資料                                         |
| 38 | 相片資料                           | No                  | wd： Worker \_ data/wd：相片 \_ 資料                                                |
| 39 | 限定資料                   | No                  | wd： Worker \_ data/wd：限定性 \_ 資料                                        |
| 40 | 相關人員資料                 | No                  | wd： Worker \_ data/wd：相關 \_ 人員 \_ 資料                                     |
| 41 | 角色資料                            | No                  | wd： Worker \_ data/wd：角色 \_ 資料                                                 |
| 42 | 技能資料                           | No                  | wd： Worker \_ Data/wd：技能 \_ 資料                                                |
| 43 | 連續的設定檔資料              | No                  | wd： Worker \_ data/wd：連續的 \_ 設定檔 \_ 資料                                  |
| 44 | 人才評量資料               | No                  | wd： Worker \_ Data/wd：人才 \_ 評量 \_ 資料                                   |
| 45 | 使用者帳戶資料                    | No                  | wd： Worker \_ data/wd：使用者 \_ 帳戶 \_ 資料                                        |
| 46 | 背景工作檔資料                 | No                  | wd： Worker \_ Data/wd： worker \_ Document \_ 資料                                     |

以下是一些範例，說明如何擴充 Workday 整合以符合特定需求。 

**範例 1**

假設您想要從 Workday 取出下列資料集，並在您的布建規則中使用這些資料集：

* 成本中心
* 成本中心階層
* 付費群組

預設不會包含上述的資料集。 若要取得這些資料集：
1. 登入 Azure 入口網站，並開啟您的 Workday 至 AD/Azure AD 使用者布建應用程式。 
1. 在 [布建] 分頁中，編輯對應，並從 [advanced] 區段開啟 Workday 屬性清單。 
1. 新增下列屬性定義，並將其標示為「必要」。 這些屬性不會對應至 AD 或 Azure AD 中的任何屬性。 它們只是做為連接器的信號，以取得成本中心、成本中心階層和付費群組資訊。 

     > [!div class="mx-tdCol2BreakAll"]
     >| 屬性名稱 | XPATH API 運算式 |
     >|---|---|
     >| CostCenterHierarchyFlag  |  wd： Worker/wd： Worker_Data/wd： Organization_Data/wd： Worker_Organization_Data [wd： Organization_Data/wd： Organization_Type_Reference/wd： ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER_HIERARCHY ']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  wd： Worker/wd： Worker_Data/wd： Organization_Data/wd： Worker_Organization_Data [wd： Organization_Data/wd： Organization_Type_Reference/wd： ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER ']/wd： Organization_Data/wd： Organization_Code/text ( # A1 |
     >| PayGroupFlag  |  wd： Worker/wd： Worker_Data/wd： Organization_Data/wd： Worker_Organization_Data [wd： Organization_Data/wd： Organization_Type_Reference/wd： ID [ @wd:type = ' Organization_Type_ID '] = ' PAY_GROUP ']/wd： Organization_Data/wd： Organization_Reference_ID/text ( # A1 |

1. 一旦「成本中心」和「付費群組」資料集在 *Get_Workers* 回應中可供使用，您就可以使用下列 XPATH 值來取出成本中心名稱、成本中心代碼和付費群組。 

     > [!div class="mx-tdCol2BreakAll"]
     >| 屬性名稱 | XPATH API 運算式 |
     >|---|---|
     >| CostCenterName  | wd： Worker/wd： Worker_Data/wd： Organization_Data/wd： Worker_Organization_Data/wd： Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' Cost Center ']/wd： Organization_Name/text ( # A1 |
     >| CostCenterCode | wd： Worker/wd： Worker_Data/wd： Organization_Data/wd： Worker_Organization_Data/wd： Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' Cost Center ']/wd： Organization_Code/text ( # A1 |
     >| PayGroup | wd： Worker/wd： Worker_Data/wd： Organization_Data/wd： Worker_Organization_Data/wd： Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' 付費群組 ']/wd： Organization_Name/text ( # A1 |

**範例 2**

假設您想要取得與使用者相關聯的認證。 這項資訊可作為 *限定性資料* 集的一部分。 若要取得此資料集做為 *Get_Workers* 回應的一部分，請使用下列 XPATH： 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**範例 3**

假設您想要取出指派給背景工作角色的布建 *群組* 。 這項資訊可作為 *帳戶提供資料* 集的一部分。 若要取得此資料集做為 *Get_Workers* 回應的一部分，請使用下列 XPATH： 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="next-steps"></a>後續步驟

* [瞭解如何將 Workday 設定為 Active Directory 布建](../saas-apps/workday-inbound-tutorial.md)
* [瞭解如何設定回寫至 Workday](../saas-apps/workday-writeback-tutorial.md)
* [深入了解輸入佈建支援的 Workday 屬性](workday-attribute-reference.md)

