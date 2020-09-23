---
title: Azure Active Directory 與 SAP SuccessFactors 整合參考
description: 深入瞭解 SAP SuccessFactors-HR 驅動的布建
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/20/2020
ms.author: chmutali
ms.openlocfilehash: 805cdc0713afd43502bb224cce60167adbc418ee
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969523"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Azure Active Directory 布建如何與 SAP SuccessFactors 整合 

[Azure Active Directory 使用者](../app-provisioning/user-provisioning.md) 布建服務與 [SAP SuccessFactors 員工中心](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 整合，以管理使用者的身分識別生命週期。 Azure Active Directory 提供三個預先建立的整合： 

* [SuccessFactors 至內部部署 Active Directory 使用者布建](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors 至 Azure Active Directory 使用者布建](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [SuccessFactors 回寫](../saas-apps/sap-successfactors-writeback-tutorial.md)

本文說明整合的運作方式，以及如何針對不同的 HR 案例自訂布建行為。 

## <a name="establishing-connectivity"></a>建立連線能力 
Azure AD 布建服務會使用基本驗證來連接到 Employee Central OData API 端點。 設定 SuccessFactors 布建應用程式時，請使用 [系統*管理員認證*] 區段中的 [*租使用者 url* ] 參數來設定[API 資料中心 URL](https://apps.support.sap.com/sap/support/knowledge/en/2215682)。 

若要進一步保護 Azure AD 布建服務和 SuccessFactors 之間的連線，您可以使用以下所述的步驟，在 SuccessFactors IP 允許清單中新增 Azure AD 的 IP 範圍：

1. 下載 Azure 公用雲端的[最新 IP 範圍](https://www.microsoft.com/download/details.aspx?id=56519) 
1. 開啟檔案，並搜尋標記 **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP 範圍](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. 複製 [元素] *addressPrefixes* 中列出的所有 IP 位址範圍，並使用範圍來建立您的 IP 位址限制清單。
1. 將 CIDR 值轉譯為 IP 範圍。  
1. 登入 SuccessFactors 系統管理員入口網站，將 IP 範圍新增至允許清單。 請參閱 SAP [支援附注 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200)。 您現在可以在此工具中 [輸入 IP 範圍](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) 。 

## <a name="supported-entities"></a>支援的實體
針對 SuccessFactors 中的每個使用者，Azure AD 布建服務都會抓取下列實體。 每個實體都會使用 OData API *$expand* query 參數展開。 請參閱下方的 [抓取 *規則* ] 資料行。 某些實體預設會展開，而某些實體只有在對應中有特定屬性時才會展開。 

| \# | SuccessFactors 實體                  | OData 節點     | 抓取規則 |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *根節點*                  | 一律           |
| 2  | PerPersonal                            | personalInfoNav              | 一律           |
| 3  | PerPhone                               | phoneNav                     | 一律           |
| 4  | PerEmail                               | emailNav                     | 一律           |
| 5  | EmpEmployment                          | employmentNav                | 一律           |
| 6  | User                                   | employmentNav/userNav        | 一律           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | 一律           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | 一律           |
| 9  | FOCompany                              | employmentNav/jobInfoNav/companyNav | 只有當 `company` 或 `companyId` 屬性已對應時 |
| 10 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | 只有當 `department` 或 `departmentId` 屬性已對應時 |
| 11 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | 只有當 `businessUnit` 或 `businessUnitId` 屬性已對應時 |
| 12 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | 只有當 `costCenter` 或 `costCenterId` 屬性已對應時 |
| 13 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | 只有當 `division` 或 `divisionId` 屬性已對應時 |
| 14 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | 只有當 `jobCode` 或 `jobCodeId` 屬性已對應時 |
| 15 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | 只有當 `payGrade` 屬性已對應時 |
| 16 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | 只有當 `location` 屬性已對應時 |
| 17 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | 如果對應包含下列其中一個屬性： `officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 18 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | 只有當 `eventReason` 屬性已對應時 |
| 19 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | 只有在 `assignmentType` 對應時 |
| 20 | EmploymentType 挑選清單                | employmentNav/jobInfoNav/employmentTypeNav | 只有在 `employmentType` 對應時 |
| 21 | EmployeeClass 挑選清單                 | employmentNav/jobInfoNav/employeeClassNav | 只有在 `employeeClass` 對應時 |
| 22 | EmplStatus 挑選清單                    | employmentNav/jobInfoNav/emplStatusNav | 只有在 `emplStatus` 對應時 |
| 23 | AssignmentType 挑選清單                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | 只有在 `assignmentType` 對應時 |

## <a name="how-full-sync-works"></a>完整同步處理的運作方式
根據屬性對應，在完整同步處理期間 Azure AD 布建服務會傳送下列 "GET" OData API 查詢，以提取所有作用中使用者的有效資料。 

> [!div class="mx-tdCol2BreakAll"]
>| 參數 | 描述 |
>| ----------|-------------|
>| OData API 主機 | 將 HTTPs 附加至 *租使用者 URL*。 範例： `https://api4.successfactors.com` |
>| OData API 端點 | `/odata/v2/PerPerson` |
>| OData $format 查詢參數 | `json` |
>| OData $filter 查詢參數 | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| OData $expand 查詢參數 | 此參數值取決於對應的屬性。 範例： `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| OData customPageSize 查詢參數 | `100` |

> [!NOTE]
> 在第一次初始完整同步處理期間，Azure AD 布建服務不會提取非使用中/已終止的背景工作資料。

針對每個 SuccessFactors 使用者，布建服務會在目標 (中尋找帳戶 Azure AD/內部部署 Active Directory) 使用對應中定義的相符屬性。 例如：如果 *personIdExternal* 對應至 [ *員工* ]，並設定為 [比對] 屬性，則布建服務會使用 *personIdExternal* 值來搜尋具有 [ *員工* ] 篩選準則的使用者。 如果找到相符的使用者，則會更新目標屬性。 如果找不到相符專案，則會在目標中建立新專案。 

若要驗證您的 OData API 端點針對特定所傳回的資料 `personIdExternal` ，請使用 `SuccessFactorsAPIEndpoint` 您的 api 資料中心伺服器 URL 更新下列 api 查詢中的，並使用 [Postman](https://www.postman.com/downloads/) 之類的工具來叫用查詢。 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>增量同步處理的運作方式

完整同步處理之後，Azure AD 布 `LastExecutionTimestamp` 建服務會維護並使用它來建立 delta 查詢，以供您抓取增量變更。 每個 SuccessFactors 實體中的時間戳記屬性（例如 `lastModifiedDateTime` 、 `startDate` 、 `endDate` 和 `latestTerminationDate` ）都會進行評估，以查看變更是否落在 `LastExecutionTimestamp` 和之間 `CurrentExecutionTime` 。 如果是，則專案變更會被視為有效並處理同步處理。 

## <a name="reading-attribute-data"></a>讀取屬性資料

當 Azure AD 布建服務查詢 SuccessFactors 時，它會抓取 JSON 結果集。 JSON 結果集會包含許多儲存在員工中部中的屬性。 根據預設，布建架構會設定為只抓取這些屬性的子集。 

若要取得其他屬性，請遵循下列步驟：
    
1. 流覽至**企業應用程式**  ->  **SuccessFactors 應用程式**布  ->  **Provisioning**  ->  建的 [**編輯**布  ->  **建屬性-對應] 頁面**。
1. 向下鍵，然後按一下 [ **顯示 advanced options**]。
1. 按一下 [ **SuccessFactors 的編輯屬性清單**]。 

   > [!NOTE] 
   > 如果 [SuccessFactors] 選項的 [ **編輯屬性清單** ] 未顯示在 Azure 入口網站中，請使用 URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* 存取頁面。 

1. 此視圖中的 [ **API 運算式** ] 欄會顯示連接器使用的 JSONPath 運算式。

   >[!div class="mx-imgBorder"] 
   >![API 運算式](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. 您可以編輯現有的 JSONPath 值，或使用有效的 JSONPath 運算式將新的屬性加入至架構。 

下一節提供編輯 JSONPath 值的常見案例清單。 

## <a name="handling-different-hr-scenarios"></a>處理不同的 HR 案例

JSONPath 是 JSON 的查詢語言，類似于 XML 的 XPath。 就像 XPath 一樣，JSONPath 可讓您從 JSON 承載中解壓縮和篩選資料。

藉由使用 JSONPath 轉換，您可以自訂 Azure AD 布建應用程式的行為，以抓取自訂屬性和處理案例，例如 rehire、背景工作轉換和全域指派。 

本節說明如何針對下列 HR 案例自訂布建應用程式： 
* [正在抓取其他屬性](#retrieving-additional-attributes)
* [正在抓取自訂屬性](#retrieving-custom-attributes)
* [處理背景工作轉換案例](#handling-worker-conversion-scenario)
* [處理 rehire 案例](#handling-rehire-scenario)
* [處理全域指派案例](#handling-global-assignment-scenario)
* [處理並行作業案例](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>正在抓取其他屬性

預設 Azure AD SuccessFactors 布建應用程式架構隨附 [90 個以上的預先定義屬性](sap-successfactors-attribute-reference.md)。 若要將更多 SuccessFactors 屬性新增至布建架構，請使用下列步驟： 

1. 使用下面的 OData 查詢，從員工中心取得有效測試使用者的資料。 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. 判斷與屬性相關聯的員工中心實體
   * 如果屬性是 *EmpEmployment* 實體的一部分，請在 [ *employmentNav* 節點] 底下尋找屬性。 
   * 如果屬性是 *使用者* 實體的一部分，請在 [employmentNav]/[ *userNav* 節點] 底下尋找屬性。
   * 如果屬性是 *EmpJob* 實體的一部分，請在 [employmentNav]/[ *jobInfoNav* ] 節點底下尋找屬性。 
1. 建立與屬性相關聯的 JSON 路徑，並將這個新屬性加入至 SuccessFactors 屬性清單。 
   * 範例1：假設您想要新增屬性 *okToRehire*，這是 *employmentNav* 實體的一部分，然後使用 JSONPath  `$.employmentNav.results[0].okToRehire`
   * 範例2：假設您想要新增屬於*userNav*實體一部分的屬性*時區*，然後使用 JSONPath`$.employmentNav.results[0].userNav.timeZone`
   * 範例3：假設您想要新增屬性 *flsaStatus*，這是 *jobInfoNav* 實體的一部分，然後使用 JSONPath `$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
1. 儲存結構描述。 
1. 重新開機布建。

### <a name="retrieving-custom-attributes"></a>正在抓取自訂屬性

根據預設，下列自訂屬性會在 Azure AD SuccessFactors 布建應用程式中預先定義： 
* *custom01-custom15* 自使用者 (userNav) 實體
* *customString1-customString15* 自 EmpEmployment (employmentNav) 實體，稱為 *empNavCustomString1-empNavCustomString15*
* *customString1-customString15* 自 EmpJobInfo (jobInfoNav) 實體，稱為 *empJobNavCustomString1-empNavJobCustomString15*

假設在您的員工中央實例中， *EmpJobInfo*中的*customString35*屬性會儲存位置描述。 您想要將此值傳送給 Active Directory 的 *physicalDeliveryOfficeName* 屬性。 若要設定此案例的屬性對應，請使用以下提供的步驟： 

1. 編輯 [SuccessFactors 屬性] 清單，加入名為 *empJobNavCustomString35*的新屬性。
1. 將這個屬性的 JSONPath API 運算式設為： `$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. 儲存並重載 Azure 入口網站中的對應變更。  
1. 在 [屬性對應] 分頁中，將 *empJobNavCustomString35* 對應至 *physicalDeliveryOfficeName*。
1. 儲存對應。

擴充此案例： 
* 如果您想要從*使用者*實體對應*custom35*屬性，請使用 JSONPath`$.employmentNav.results[0].userNav.custom35`
* 如果您想要從*EmpEmployment*實體對應*customString35*屬性，請使用 JSONPath`$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>處理背景工作轉換案例

背景工作轉換是指將現有全職員工轉換成承包商或承包商進行全時間的程式。 在此案例中，員工中心會將新的*EmpEmployment*實體與相同*Person*實體的新*使用者*實體一併加入。 在先前的*EmpEmployment*實體下嵌套的*使用者*實體會設定為 null。 若要處理此案例，以便在進行轉換時顯示新的雇用資料，您可以使用下列步驟大量更新布建應用程式架構：  

1. 開啟 SuccessFactors 布建應用程式的屬性對應 blade。 
1. 向下鍵，然後按一下 [ **顯示 advanced options**]。
1. 按一下連結，在 **這裡查看您的架構** 以開啟架構編輯器。 

   >![螢幕擷取畫面顯示開啟架構編輯器的 [在這裡審核您的架構] 連結。](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. 按一下 [ **下載** ] 連結，以在編輯之前儲存架構的複本。 

   >![螢幕擷取畫面顯示具有下載 select 的架構編輯器，以儲存架構的複本。](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. 在 [架構編輯器] 中，按 Ctrl-H 鍵以開啟 [尋找取代] 控制項。
1. 在 [尋找] 文字方塊中，複製並貼上值 `$.employmentNav.results[0]`
1. 在 [取代] 文字方塊中，複製並貼上值 `$.employmentNav.results[?(@.userNav != null)]` 。 請注意運算子周圍的空白字元 `!=` ，這對於成功處理 JSONPath 運算式而言很重要。 
   >![尋找-取代-轉換](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. 按一下 [取代全部] 選項以更新架構。 
1. 儲存結構描述。 
1. 上述進程會更新所有的 JSONPath 運算式，如下所示： 
   * 舊的 JSONPath： `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 新 JSONPath： `$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. 重新開機布建。 

### <a name="handling-rehire-scenario"></a>處理 rehire 案例

通常有兩個選項可處理重新雇用： 
* 選項1：在員工中部建立新的個人設定檔
* 選項2：在員工中部重複使用現有的人員設定檔

如果您的 HR 流程使用選項1，則不需要變更布建架構。 如果您的 HR 流程使用選項2，則員工中心會將新的*EmpEmployment*實體連同相同*Person*實體的新*使用者*實體一起新增。 與轉換案例不同的是，先前*EmpEmployment*實體中的*使用者*實體不會設定為 null。 

若要處理此 rehire 案例 (選項 2) ，以便針對 rehire 設定檔顯示最新的雇用資料，您可以使用下列步驟大量更新布建應用程式架構：  

1. 開啟 SuccessFactors 布建應用程式的屬性對應 blade。 
1. 向下鍵，然後按一下 [ **顯示 advanced options**]。
1. 按一下連結，在 **這裡查看您的架構** 以開啟架構編輯器。   
1. 按一下 [ **下載** ] 連結，以在編輯之前儲存架構的複本。   
1. 在 [架構編輯器] 中，按 Ctrl-H 鍵以開啟 [尋找取代] 控制項。
1. 在 [尋找] 文字方塊中，複製並貼上值 `$.employmentNav.results[0]`
1. 在 [取代] 文字方塊中，複製並貼上值 `$.employmentNav.results[-1:]` 。 這個 JSONPath 運算式會傳回最新的 *EmpEmployment* 記錄。   
1. 按一下 [取代全部] 選項以更新架構。 
1. 儲存結構描述。 
1. 上述進程會更新所有的 JSONPath 運算式，如下所示： 
   * 舊的 JSONPath： `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 新 JSONPath： `$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. 重新開機布建。 

此架構變更也支援背景工作轉換案例。 

### <a name="handling-global-assignment-scenario"></a>處理全域指派案例

當員工中心內的使用者針對全域指派進行處理時，SuccessFactors 會新增一個新的 *EmpEmployment* 實體，並將 *assignmentClass* 設定為「GA」。 它也會建立新的 *使用者* 實體。 因此，使用者現在有：
* 一個*EmpEmployment*  +  對應至 home 指派的 EmpEmployment*使用者*實體， *assignmentClass*設定為 "ST" 和 
* 另*EmpEmployment*一個  +  對應至全域指派的 EmpEmployment*使用者*實體， *assignmentClass*設定為「GA」

若要提取屬於標準指派和全域指派使用者設定檔的屬性，請使用下列步驟： 

1. 開啟 SuccessFactors 布建應用程式的屬性對應 blade。 
1. 向下鍵，然後按一下 [ **顯示 advanced options**]。
1. 按一下連結，在 **這裡查看您的架構** 以開啟架構編輯器。   
1. 按一下 [ **下載** ] 連結，以在編輯之前儲存架構的複本。   
1. 在 [架構編輯器] 中，按 Ctrl-H 鍵以開啟 [尋找取代] 控制項。
1. 在 [尋找] 文字方塊中，複製並貼上值 `$.employmentNav.results[0]`
1. 在 [取代] 文字方塊中，複製並貼上值 `$.employmentNav.results[?(@.assignmentClass == 'ST')]` 。 
1. 按一下 [取代全部] 選項以更新架構。 
1. 儲存結構描述。 
1. 上述進程會更新所有的 JSONPath 運算式，如下所示： 
   * 舊的 JSONPath： `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 新 JSONPath： `$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. 重載應用程式的屬性對應 blade。 
1. 向下鍵，然後按一下 [ **顯示 advanced options**]。
1. 按一下 [ **SuccessFactors 的編輯屬性清單**]。
1. 新增屬性以提取全域指派資料。 例如：如果您想要提取與全域指派設定檔相關聯的部門名稱，您可以將 JSONPath 運算式設定為的屬性 *globalAssignmentDepartment* `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` 。 
1. 您現在可以將兩個部門值流向 Active Directory 屬性，或選擇性地使用運算式對應來傳送值。 範例：下列運算式會將 AD *部門* 屬性的值設定為 *globalAssignmentDepartment* （如果有的話），否則它會將值設定為與標準指派相關聯的 *部門* 。 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. 儲存對應。 
1. 重新開機布建。 

### <a name="handling-concurrent-jobs-scenario"></a>處理並行作業案例

當員工中心內的使用者具有並行/多個作業時，有兩個 *EmpEmployment* 和 *使用者* 實體的 *assignmentClass* 設定為 "ST"。 若要提取屬於這兩個作業的屬性，請使用下列步驟： 

1. 開啟 SuccessFactors 布建應用程式的屬性對應 blade。 
1. 向下鍵，然後按一下 [ **顯示 advanced options**]。
1. 按一下 [ **SuccessFactors 的編輯屬性清單**]。
1. 假設您想要提取與作業1和作業2相關聯的部門。 預先定義的屬性 *部門* 已經為第一個工作提取部門的價值。 您可以定義名為 *secondJobDepartment* 的新屬性，並將 JSONPath 運算式設定為 `$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
1. 您現在可以將兩個部門值流向 Active Directory 屬性，或選擇性地使用運算式對應來傳送值。 
1. 儲存對應。 
1. 重新開機布建。 

## <a name="writeback-scenarios"></a>回寫案例

本節涵蓋不同的回寫案例。 它會根據 SuccessFactors 中設定電子郵件和電話號碼的方式，建議設定方法。

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>電話和電子郵件回寫的支援案例 

| \# | 案例需求 | 電子郵件主要 <br> 旗標值 | 商務電話 <br> 主要旗標值 | 行動電話 <br> 主要旗標值 | 商務電話 <br> 對應 | 行動電話 <br> 對應 |
|--|--|--|--|--|--|--|
| 1 | * 僅將企業電子郵件設定為主要。 <br> * 請勿設定電話號碼。 | true | true | false | \[未設定\] | \[未設定\] | 
| 2 | * 在 SuccessFactors 中，公司電子郵件和公司電話是主要的 <br> * 一律將 Azure AD 電話號碼傳送至公司電話，並傳送到行動電話。 | true | true | false | telephoneNumber | mobile | 
| 3 | * 在 SuccessFactors 中，公司電子郵件和行動電話是主要的 <br> * 一律將 Azure AD 電話號碼傳送至公司電話和行動電話傳送至手機 | true | false | true |  telephoneNumber | mobile | 
| 4 | * SuccessFactors 商務電子郵件是主要的 <br> * 在 Azure AD 中，檢查是否有公司電話號碼存在，如果存在，請檢查是否也有行動電話號碼，只有在沒有行動電話號碼時，才將公司電話號碼標示為主要。 | true | 使用運算式對應： `IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | 使用運算式對應： `IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | mobile | 
| 5 | * 在 SuccessFactors 的公司電子郵件和公司電話都是主要的。 <br> * 在 Azure AD 中，如果行動裝置可供使用，請將它設定為公司電話，否則請使用 telephoneNumber。 | true | true | false | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[未設定\] | 

* 如果在回寫屬性對應中沒有電話號碼的對應，則寫回中只會包含電子郵件。
* 在員工中心的新員工上線期間，可能無法使用公司電子郵件和電話號碼。 如果將公司電子郵件和公司電話設定為主要，則在上線期間，您可以在新員工建立期間設定公司電話和電子郵件的虛擬值，這最終會由回寫應用程式更新。
 
### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>不支援的電話和電子郵件回寫案例

* 在員工中心內，登入個人電子郵件和個人電話設定為主要。 回寫應用程式無法切換此設定，並將公司電子郵件和公司電話設定為主要。
* 在員工中心，公司電話設定為主要。 回寫應用程式無法變更此設定，並將行動電話設定為主要。
* 回寫應用程式無法讀取目前的主要旗標設定，並使用相同的值進行寫入操作。 一律會使用屬性對應中所設定的旗標值。 

## <a name="next-steps"></a>下一步

* [瞭解如何設定 SuccessFactors 以 Active Directory 布建](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [瞭解如何設定回寫至 SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [深入瞭解輸入布建的支援 SuccessFactors 屬性](sap-successfactors-attribute-reference.md)










