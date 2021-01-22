---
title: 教學課程 - 自訂 Azure Active Directory 屬性對應
description: 了解 Azure Active Directory 中 SaaS 應用程式有哪些屬性對應，以及如何修改屬性對應來應付業務需求。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: kenwith
ms.openlocfilehash: efdbec10c74a6b1892df13b8308538e61f42f679
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673496"
---
# <a name="tutorial---customize-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>教學課程 - 在 Azure Active Directory 中自訂 SaaS 應用程式的使用者佈建屬性對應

Microsoft Azure AD 支援將使用者佈建至 Salesforce、G Suite 等第三方 SaaS 應用程式。 如果您啟用了第三方 SaaS 應用程式的使用者佈建，Azure 入口網站將會透過屬性對應控制其屬性值。

在 Azure AD 使用者物件和每個 SaaS 應用程式的使用者物件之間，有一組預先設定的屬性和屬性對應。 有些應用程式除了使用者以外，還會管理其他類型的物件，例如群組。

您可以根據您的業務需求自訂預設的屬性對應。 因此，您可以變更或刪除現有的屬性對應，或建立新的屬性對應。

## <a name="editing-user-attribute-mappings"></a>編輯使用者屬性對應

請依照下列步驟存取使用者佈建的 **對應** 功能：

1. 登入 [Azure Active Directory 入口網站](https://aad.portal.azure.com)。
1. 在左側窗格中，選取 [企業應用程式]。 此時會顯示所有已設定的應用程式清單，包括已從資源庫新增的應用程式。
1. 選取任何應用程式以載入其 [應用程式管理] 窗格，您可以在其中檢視報表及管理應用程式設定。
1. 選取 [佈建]，以管理所選應用程式的使用者帳戶佈建設定。
1. 展開 [對應] 以檢視和編輯在 Azure AD 與目標應用程式之間流動的使用者屬性。 如果目標應用程式可支援，則此區段可讓您選擇性地設定群組和使用者帳戶的佈建。

   ![使用對應檢視和編輯使用者屬性](./media/customize-application-attributes/21.png)

1. 選取 [對應] 設定，以開啟相關的 [屬性對應] 畫面。 SaaS 應用程式必須要有某些屬性對應才能正確運作。 若為必要的屬性，[刪除] 功能就無法使用。

   ![使用屬性對應來設定應用程式的屬性對應](./media/customize-application-attributes/22.png)

   在此螢幕擷取畫面中，您可以看到 Salesforce 中受控物件的 **Username** 屬性填入了連結的 Azure Active Directory 物件的 **userPrincipalName** 值。

1. 選取現有的 [屬性對應]，以開啟 [編輯屬性] 畫面。 在此處，您可以編輯在 Azure AD 與目標應用程式之間流動的使用者屬性。

   ![使用編輯屬性來編輯使用者屬性](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>了解屬性對應類型

透過屬性對應，您將可控制屬性在第三方 SaaS 應用程式中填入的方式。
支援四種不同的對應類型：

- **直接** - 目標屬性會填入 Azure AD 中連結物件的屬性值。
- **常數** - 目標屬性會填入您所指定的特定字串。
- **運算式** - 目標屬性會根據類似指令碼的運算式結果填入。
  如需詳細資訊，請參閱[在 Azure Active Directory 中撰寫屬性對應的運算式](../app-provisioning/functions-for-customizing-application-data.md)。
- **無** - 目標屬性保留未修改。 不過，如果目標屬性是空的，就會填入您所指定的預設值。

除了這四個基本類型以外，自訂屬性對應還支援選擇性的 **預設** 值指派的概念。 預設值指派可確保當 Azure AD 中和目標物件都沒有值時，目標屬性會填入某個值。 最常見的設定是將其保留空白。

### <a name="understanding-attribute-mapping-properties"></a>了解屬性對應屬性

在上一節中，我們已為您介紹屬性對應類型屬性。
除了這個屬性之外，屬性對應也支援下列屬性：

- **來源屬性** - 來源系統中的使用者屬性 (例如：Azure Active Directory)。
- **目標屬性** - 目標系統中的使用者屬性 (例如：ServiceNow)。
- **Null 時的預設值 (選擇性)** - 來源屬性為 Null 時將傳至目標系統的值。 只有在建立使用者時，才會佈建此值。 更新現有的使用者時不會佈建「Null 時的預設值」。 例如，如果您想要在目標系統中以特定職稱 (若此值在來源系統中為 Null) 佈建所有現有的使用者，您可以使用下列[運算式](../app-provisioning/functions-for-customizing-application-data.md)：Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). 請務必將「預設值」取代為您要在來源系統中的值為 Null 時佈建的值。 
- **使用此屬性比對物件** – 是否應將此對應用來唯一識別來源與目標系統之間的使用者。 這通常會設定於 Azure AD 中的 userPrincipalName 或 mail 屬性上，而該屬性通常會對應至目標應用程式中的使用者名稱欄位。
- **比對優先順序** – 您可以設定多個比對屬性。 具有多個屬性時，系統會以此欄位定義的順序進行評估。 只要找到相符項目，便不會評估進一步比對屬性。 雖然您可以視需要設定不限數量的比對屬性，但請考量您作為比對屬性的屬性是否確實是唯一的，且必須是比對屬性。 一般而言，客戶的設定中會有 1 或 2 個比對屬性。 
- **套用此對應**
  - **一律** – 將此對應套用於使用者建立和更新動作。
  - **僅限建立期間** - 僅將此對應套用於使用者建立動作。

## <a name="matching-users-in-the-source-and-target--systems"></a>比對來源和目標系統中的使用者
Azure AD 佈建服務可以部署在 "greenfield" 案例中 (使用者不存在於目標系統中) 和 "brownfield" 案例中 (使用者已存在於目標系統中)。 為了支援這兩種案例，佈建服務會使用比對屬性的概念。 比對屬性可讓您決定如何唯一識別來源中的使用者，並比對目標中的使用者。 在規劃部署的過程中，請找出可用來對來源和目標系統中的使用者進行唯一識別的屬性。 注意事項：

- **比對屬性必須是唯一的：** 客戶常會使用 userPrincipalName、mail 或物件識別碼等屬性作為比對屬性。
- **可將多個屬性作為比對屬性：** 您可以定義多個要在比對使用者時評估的屬性，及其評估順序 (定義為 UI 中的比對優先順序)。 例如，如果您將三個屬性定義為比對屬性，且在評估前兩個屬性之後即找出唯一相符的使用者，服務就不會評估第三個屬性。 服務會依照指定的順序評估比對屬性，並在找到相符項目時停止評估。  
- **來源和目標中的值不一定要完全相符：** 目標中的值可以是來源中之值的某種簡單函式。 因此，若在來源中具有 emailAddress 屬性，在目標中具有 userPrincipalName，將可使用 emailAddress 屬性的函式 (以某個常數值取代部分字元) 來進行比對。  
- **不支援根據屬性的組合進行比對：** 大部分的應用程式不支援以兩個屬性為基礎的查詢。 因此，無法根據屬性的組合進行比對。 您可以逐一評估單一屬性。
- **所有使用者都必須至少有一個比對屬性的值：** 如果您定義了一個比對屬性，則所有使用者在來源系統中都必須有該屬性的值。 例如，如果您將 userPrincipalName 定義為比對屬性，則所有使用者都必須具有 userPrincipalName。 如果您定義了多個比對屬性 (例如 extensionAttribute1 和 mail)，則並非所有使用者都必須具有相同的比對屬性。 某個使用者具有 extensionAttribute1，但沒有 mail，而另一個使用者具有 mail，但沒有 extensionAttribute1，是有可能的。 
- **目標應用程式必須支援比對屬性的篩選：** 應用程式開發人員可以在其使用者或群組 API 上篩選屬性子集。 對於資源庫中的應用程式，我們確保預設屬性對應是用於目標應用程式的 API 可篩選的屬性。 變更目標應用程式的預設比對屬性時，請查看第三方 API 文件，以確定該屬性是可篩選的。  

## <a name="editing-group-attribute-mappings"></a>編輯群組屬性對應

特定應用程式 (例如 ServiceNow、Box 和 G Suite) 支援佈建群組物件和使用者物件的能力。 除了群組成員以外，群組物件也可包含群組屬性，例如顯示名稱和電子郵件別名。

![此範例顯示已佈建群組和使用者物件的 ServiceNow](./media/customize-application-attributes/24.png)

佈建群組可以選擇性地啟用或停用，方法是在 [對應] 下選取群組對應，然後在 [屬性對應] 畫面中將您所需的選項設定為 [啟用]。

佈建為群組物件一部分的屬性，可依照與使用者物件相同的方式進行自訂，如前所述。 

> [!TIP]
> 佈建群組物件 (屬性和成員) [與將群組指派](../manage-apps/assign-user-or-group-access-portal.md)給應用程式是不同的概念。 您可以將群組指派給應用程式，但只能佈建群組中所包含的使用者物件。 要在指派中使用群組，並不需要佈建整個群組的物件。

## <a name="editing-the-list-of-supported-attributes"></a>編輯支援的屬性清單

系統會預先設定支援指定應用程式的使用者屬性。 大部分應用程式的使用者管理 API 不支援結構描述探索。 因此，Azure AD 佈建服務無法藉由呼叫應用程式動態產生支援的屬性清單。

不過，有些應用程式支援自訂屬性，且 Azure AD 佈建服務可以讀取和寫入自訂屬性。 若要在 Azure 入口網站中輸入其定義，請選取 [屬性對應] 畫面底部的 [顯示進階選項] 核取方塊，然後選取 [編輯應用程式的屬性清單]。

支援屬性清單自訂的應用程式和系統包括：

- Salesforce
- ServiceNow
- Workday 至 Active Directory / Workday 至 Azure Active Directory
- SuccessFactors 至 Active Directory / SuccessFactors 至 Azure Active Directory
- Azure Active Directory (支援 [Azure AD Graph API 預設屬性](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity)和自訂目錄擴充功能)
- 支援 [SCIM 2.0](https://tools.ietf.org/html/rfc7643) 的應用程式
- Azure Active Directory 若回寫至 Workday 或 SuccessFactors，支援為支援的屬性 (XPATH 和 JSONPath) 更新相關的中繼資料，但不支援新增 Workday 或 SuccessFactors 屬性，不過，預設架構描述中所包含的屬性除外


> [!NOTE]
> 我們建議，只有已自訂應用程式和系統的結構描述，並確知其自訂屬性如何定義的系統管理員，才可編輯支援的屬性清單。 有時，這會需要熟悉應用程式或系統所提供的 API 和開發人員工具。 預設會鎖定支援屬性清單的編輯功能，但客戶可以瀏覽至下列 URL 來啟用此功能： https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true 。 接著，您可以瀏覽至您的應用程式來檢視屬性清單，如[上方](#editing-the-list-of-supported-attributes)所述。 

編輯支援的屬性清單時，必須提供下列屬性：

- **名稱** - 屬性的系統名稱，如目標物件的結構描述所定義。
- **類型** - 屬性儲存的資料類型 (如目標物件的結構描述所定義)，可以是下列其中一種類型：
  - *二進位* - 屬性包含二進位資料。
  - *布林值* - 屬性包含 True 或 False 值。
  - *日期時間* - 屬性包含日期字串。
  - *整數* - 屬性包含整數。
  - *參考* - 屬性包含對目標應用程式中的另一個資料表所儲存的值進行參考的識別碼。
  - *字串* - 屬性包含文字字串。
- **是主索引鍵嗎？** - 屬性是否定義為目標物件結構描述中的主索引鍵欄位。
- **必要？** - 屬性是否必須填入目標應用程式或系統中。
- **是多重值嗎？** - 屬性是否支援多個值。
- **大小寫完全相符嗎？** - 是否以區分大小寫的方式評估屬性值。
- **API 運算式** - 不使用，除非特定佈建連接器 (例如 Workday) 的文件另有指示。
- **參考的物件屬性** - 如果這是參考類型屬性，則此功能表可讓您在目標應用程式中選取包含屬性相關值的資料表和屬性。 例如，如果您有名為 "Department" 的屬性，且其儲存值參考了個別 "Departments" 資料表中的物件，則您會選取 "Departments.Name"。 支援指定應用程式的參考資料表和主要識別碼欄位都是預先設定的，且目前無法使用 Azure 入口網站加以編輯，但可使用 [Microsoft Graph API](/graph/api/resources/synchronization-configure-with-custom-target-attributes) 來編輯。

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>將自訂延伸模組屬性佈建至符合 SCIM 規範的應用程式
SCIM RFC 會定義核心使用者和群組結構描述，同時允許結構描述的延伸，以符合您應用程式的需求。 若要將自訂屬性新增至 SCIM 應用程式：
   1. 登入 [Azure Active Directory 入口網站](https://aad.portal.azure.com)，選取 [企業應用程式]，選取您的應用程式，然後選取 [佈建]。
   2. 在 [對應] 底下，選取要新增自訂屬性的物件 (使用者或群組)。
   3. 在頁面底部，選取 [顯示進階選項]。
   4. 選取 [編輯 AppName 的屬性清單]。
   5. 在屬性清單的底部，將自訂屬性的相關資訊輸入到提供的欄位中。 然後，選取 [新增屬性]。

SCIM 應用程式的屬性名稱必須遵循下列範例顯示的模式。 可以根據應用程式的需求自訂 "CustomExtensionName" 和 "CustomAttribute"，例如：urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:CustomAttribute 

這些指示僅適用於已啟用 SCIM 的應用程式。 ServiceNow 和 Salesforce 等應用程式不會與使用 SCIM 的 Azure AD 整合，因此在新增自訂屬性時不需要此特定命名空間。

自訂屬性不可以是參考屬性、多重值或複雜類型屬性。 目前只有資源庫中的應用程式可支援自訂多重值和複雜類型延伸模組屬性。  
 
**具有延伸模組屬性之使用者的範例表示法：**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>將角色佈建至 SCIM 應用程式
使用下列步驟，將使用者的角色佈建至您的應用程式。 請注意，下列描述僅適用於自訂 SCIM 應用程式。 針對資源庫應用程式 (例如 Salesforce 和 ServiceNow)，請使用預先定義的角色對應。 下列項目符號說明如何將 AppRoleAssignments 屬性轉換為您的應用程式預期的格式。

- 若要將 Azure AD 中的 appRoleAssignment 對應至應用程式中的角色，您必須使用[運算式](../app-provisioning/functions-for-customizing-application-data.md)來轉換屬性。 AppRoleAssignment 屬性 **不應直接對應** 至角色屬性，而未使用運算式來剖析角色詳細資料。 

- **SingleAppRoleAssignment** 
  - **使用時機：** 使用 SingleAppRoleAssignment 運算式為使用者佈建單一角色，並指定主要角色。 
  - **如何設定：** 使用上述步驟導覽至 [屬性對應] 頁面，並使用 SingleAppRoleAssignment 運算式對應至角色屬性。 有三個角色屬性可供選擇：(roles[primary eq "True"].display、roles[primary eq "True].type 和 roles[primary eq "True"].value)。 您可以選擇在對應中包含任何或所有角色屬性。 如果您想要包含多個屬性，只要新增對應，並將其納入為目標屬性即可。  
  
  ![新增 SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **考量事項**
    - 確定未指派多個角色給使用者。 我們無法保證會佈建哪個角色。
    
  - **範例要求 (POST)** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
  - **範例輸出 (PATCH)** 
    
   ```
   "Operations": [
   {
   "op": "Add",
   "path": "roles",
   "value": [
   {
   "value": "{\"id\":\"06b07648-ecfe-589f-9d2f-6325724a46ee\",\"value\":\"25\",\"displayName\":\"Role1234\"}"
   }
   ]
   ```  
PATCH 和 POST 中的要求格式不同。 若要確保 POST 和 PATCH 以相同格式傳送，您可以使用[這裡](./application-provisioning-config-problem-scim-compatibility.md#flags-to-alter-the-scim-behavior)所述的功能旗標。 

- **AppRoleAssignmentsComplex** 
  - **使用時機：** 使用 AppRoleAssignmentsComplex 運算式為使用者佈建多個角色。 
  - **如何設定：** 依照先前的說明編輯支援的屬性清單，以納入角色的新屬性： 
  
    ![新增角色](./media/customize-application-attributes/add-roles.png)<br>

    然後，使用 AppRoleAssignmentsComplex 運算式對應至自訂角色屬性，如下圖所示：

    ![新增 AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **考量事項**
    - 所有角色都會以 primary = false 的設定佈建。
    - POST 包含角色類型。 PATCH 要求不包含類型。 我們正設法在 POST 和 PATCH 要求中都傳送類型。
    
  - **範例輸出** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>佈建多重值屬性
某些屬性 (例如 phoneNumbers 和 emails) 是多重值屬性，您可能需要指定不同類型的電話號碼或電子郵件。 請使用下列運算式來處理多重值屬性。 這可讓您指定屬性類型，並將其對應至對應 Azure AD 使用者屬性的值。 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>還原預設屬性和屬性對應

如果您要重新開始，並將現有的對應重設為其預設狀態，您可以選取 [還原預設對應] 核取方塊，並儲存設定。 這麼做會設定所有的對應和範圍篩選器，如同剛從應用程式庫將應用程式新增至您的 Azure AD 租用戶一樣。

選取此選項，可在佈建服務執行期間有效地強制重新同步處理所有的使用者。

> [!IMPORTANT]
> 強烈建議先將 [佈建狀態] 設為 [關閉]，再叫用此選項。

## <a name="what-you-should-know"></a>您應該知道的事情

- Microsoft Azure AD 提供有效率的同步處理程序實作。 在初始化環境中，同步處理期間只會處理需要更新的物件。
- 更新屬性對應會影響同步處理期間的效能。 更新屬性對應組態需要重新評估所有受控物件。
- 建議您最好不要經常變更屬性對應。
- 目前不支援新增要佈建至應用程式的相片屬性，因為您無法指定同步處理相片的格式。 您可以在 [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory) 上要求此功能
- IsSoftDeleted 屬性通常是應用程式預設對應的一部分。 IsSoftdeleted 在四種情況下可設為 true (使用者因已從應用程式取消指派而超出範圍、使用者因不符合範圍篩選器而超出範圍，使用者已在 Azure AD 中遭到虛刪除，或使用者的 AccountEnabled 屬性設定為 false)。 不建議您從屬性對應中移除 IsSoftDeleted 屬性。
- Azure AD 佈建服務不支援佈建 Null 值。
- 其主索引鍵 (通常是 "ID") 不應包含在屬性對應中作為目標屬性。 
- 角色屬性通常需要使用運算式來對應，而不是直接對應。 如需角色對應的詳細資訊，請參閱上一節。 
- 儘管您可以從對應中停用群組，但不支援停用使用者。 

## <a name="next-steps"></a>後續步驟

- [自動化 SaaS 應用程式使用者佈建/解除佈建](user-provisioning.md)
- [撰寫屬性對應的運算式](functions-for-customizing-application-data.md)
- [適用於使用者佈建的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
- [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](use-scim-to-provision-users-and-groups.md)
- [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)