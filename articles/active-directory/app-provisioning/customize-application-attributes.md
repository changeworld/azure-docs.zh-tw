---
title: 自訂 Azure AD 屬性映射 |微軟文件
description: 了解 Azure Active Directory 中 SaaS 應用程式有哪些屬性對應，以及如何修改屬性對應來應付業務需求。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfee19e9cfd1def71ebad82c2210ffc10146c896
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639751"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>為 Azure 活動目錄中的 SaaS 應用程式自訂使用者預配屬性映射

Microsoft Azure AD 支援使用者預配到第三方 SaaS 應用程式,如 Salesforce、G Suite 等。 如果為第三方 SaaS 應用程式啟用使用者預配,Azure 門戶將通過屬性映射控制其屬性值。

Azure AD 使用者物件和每個 SaaS 應用的使用者對象之間有一組預先配置的屬性和屬性映射。 某些應用與"使用者"一起管理其他類型的物件,如"組"。

您可以根據您的業務需求自訂預設的屬性對應。 因此,您可以更改或刪除現有的屬性映射,或創建新的屬性映射。

## <a name="editing-user-attribute-mappings"></a>編輯使用者屬性對應

依以下步驟存取使用者預配的**映射**功能:

1. 登入 Azure[的目錄門戶](https://aad.portal.azure.com)。
1. 從左邊窗格中選擇**企業應用程式**。 將顯示所有已配置應用的清單,包括從庫中添加的應用。
1. 選擇任何應用以載入其應用管理窗格,您可以在其中查看報表和管理應用設置。
1. 選擇 **「預配**」以管理所選應用的使用者帳戶預配設置。
1. 展開 對應 映**射**以查看和編輯在 Azure AD 和目標應用程式之間流淌的使用者屬性。 如果目標應用程式支援它,則此部分允許您選擇配置組和使用者帳戶的預配。

   ![使用地圖檢視及編輯使用者屬性](./media/customize-application-attributes/21.png)

1. 選擇**對應**開啟相關**屬性映射**螢幕。 SaaS 應用程式需要一些屬性映射才能正常運行。 若為必要的屬性，[刪除]**** 功能就無法使用。

   ![使用屬性對應設定應用屬性對應](./media/customize-application-attributes/22.png)

   在此螢幕截圖中,您可以看到 Salesforce 中託管物件的**使用者名**屬性與連結的 Azure 活動目錄物件的**用戶主體名稱**值一起填充。

1. 選擇現有**屬性映射**以打開 **「編輯屬性」** 螢幕。 在這裡,您可以編輯在 Azure AD 和目標應用程式之間流動的使用者屬性。

   ![使用編輯屬性編輯使用者屬性](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>了解屬性對應類型

透過屬性對應，您將可控制屬性在第三方 SaaS 應用程式中填入的方式。
支援四種不同的對應類型：

- **Directa:** el atributo de destino se rellena con el valor de un atributo del objeto vinculado en Azure AD.
- **常量**= 目標屬性使用您指定的特定字串填充。
- **運算式** - 目標屬性會根據類似指令碼的運算式結果填入。
  如需詳細資訊，請參閱[在 Azure Active Directory 中撰寫屬性對應的運算式](../app-provisioning/functions-for-customizing-application-data.md)。
- **Ninguno:** el atributo de destino se deja sin modificar. 但是,如果目標屬性為空,則使用指定的 Default 值填充它。

除了這四種基本類型外,自定義屬性映射還支援可選**默認值**賦值的概念。 如果 Azure AD 或目標物件上沒有值,則預設值賦值可確保使用值填充目標屬性。 最常見的設定是將其保留空白。

### <a name="understanding-attribute-mapping-properties"></a>了解屬性對應屬性

在上一節中,您已引入屬性映射類型屬性。
除了此屬性外,屬性對應支援以下屬性:

- **來源屬性** - 來源系統的使用者屬性 (例如：Azure Active Directory)。
- **目標屬性** – 目標系統中的使用者屬性 (例如：ServiceNow)。
- **預設值(如果為空)** - 如果來源屬性為空,則傳遞給目標系統的值。 僅當創建使用者時,才會預配此值。 更新現有使用者時不會預配"當為空時的預設值」。 例如,如果要向目標系統中的所有現有使用者預配特定的作業標題(噹該名稱在源系統中為空時),可以使用以下[運算](../app-provisioning/functions-for-customizing-application-data.md)式 :Switch(Is存在[作業標題])、"默認值"、"真實"、"作業標題]"。 請確保在源系統中為空時,將"默認值"替換為要預配的內容。 
- **匹配使用此屬性的物件**—是否應使用此映射來唯一標識源和目標系統之間的使用者。 它通常設置在 Azure AD 中的使用者主體名稱或郵件屬性上,該屬性通常映射到目標應用程式中的使用者名字段。
- **比對優先順序** – 您可以設定多個比對屬性。 當有多個時,按此欄位定義的順序計算它們。 只要找到相符項目，便不會評估進一步比對屬性。
- **套用此對應**
  - **始終**= 在使用者創建和更新操作上應用此映射。
  - **僅在創建期間**- 僅在使用者創建操作上應用此映射。

## <a name="matching-users-in-the-source-and-target--systems"></a>在源和目標系統中符合使用者
Azure AD 預配服務可以部署在"綠地"方案中(使用者不會在目標系統中退出)和「棕色欄位」方案(目標系統中使用者已經存在)。 為了支援這兩種情況,預配服務使用匹配屬性的概念。 匹配屬性允許您確定如何唯一標識源中的使用者並匹配目標中的使用者。 在規劃部署時,確定可用於唯一標識源和目標系統中的使用者的屬性。 需要注意的事項:

- **符合屬性應是唯一的:** 客戶通常使用用戶主體名稱、郵件或對象 ID 等屬性作為匹配屬性。
- **多屬性可用符合屬性:** 您可以定義在匹配使用者和計算使用者的順序時要計算的多個屬性(定義為 UI 中的匹配優先順序)。 例如,如果將三個屬性定義為匹配屬性,並且使用者在計算前兩個屬性后唯一匹配,則服務將不會計算第三個屬性。 該服務將按指定的順序計算匹配屬性,並在找到匹配項時停止評估。  
- **來源與目標中的值不必完全符合:** 目標中的值可以是源中值的一些簡單函數。 因此,可以在源中具有電子郵件地址屬性,在目標中具有使用者主體名稱,並通過電子郵件地址屬性的函數進行匹配,該函數將某些字元替換為一些常量值。  
- **不支援基於屬性組合的配配:** 大多數應用程式不支援基於兩個屬性的查詢。 因此,無法基於屬性的組合進行匹配。 可以一個接一個地計算單個屬性。
- **所有使用者必須至少具有一個符合屬性的值:** 如果定義一個匹配屬性,則所有用戶都必須在源系統中具有該屬性的值。 例如,如果將用戶主體名稱定義為匹配屬性,則所有使用者都必須具有使用者主體名稱。 如果定義多個匹配屬性(例如擴展屬性1 和郵件),則並非所有用戶必須具有相同的匹配屬性。 一個使用者可以有一個擴展屬性1,但不能郵寄,而另一個使用者可以有郵件,但沒有擴展屬性1。 
- **目標應用程式必須支援對符合屬性進行篩選:** 應用程式開發人員允許篩選其使用者或組 API 上的屬性子集。 對於庫中的應用程式,我們確保預設屬性映射適用於目標應用程式的 API 確實支援篩選的屬性。 更改目標應用程式的預設匹配屬性時,請檢查第三方 API 文件以確保可以篩選該屬性。  

## <a name="editing-group-attribute-mappings"></a>編輯群組屬性對應

選定的許多應用程式(如 ServiceNow、Box 和 G Suite)支援預配組物件和使用者物件的能力。 組物件可以包含組屬性,如顯示名稱和電子郵件別名以及組成員。

![範例顯示服務目前與預配群組和使用者物件](./media/customize-application-attributes/24.png)

通過選擇 **「映射**」下的組映射,並將 **「已啟用」** 設定為 **「屬性映射**」螢幕中所需的選項,可以選擇啟用或禁用組預配。

佈建為群組物件一部分的屬性，可依照與使用者物件相同的方式進行自訂，如前所述。 

> [!TIP]
> 佈建群組物件 (屬性和成員) [與將群組指派](../manage-apps/assign-user-or-group-access-portal.md)給應用程式是不同的概念。 您可以將群組指派給應用程式，但只能佈建群組中所包含的使用者物件。 要在指派中使用群組，並不需要佈建整個群組的物件。

## <a name="editing-the-list-of-supported-attributes"></a>編輯支援的屬性清單

系統會預先設定支援指定應用程式的使用者屬性。 大多數應用程式的使用者管理 API 不支援架構發現。 因此,Azure AD 預配服務無法透過調用應用程式來動態生成受支援的屬性清單。

但是,某些應用程式支援自定義屬性,Azure AD 預配服務可以讀取和寫入自定義屬性。 要在 Azure 門戶中輸入其定義,請在 **「屬性映射」** 螢幕底部選中「**顯示進階選項**」複選框,然後為應用選擇 **「編輯屬性清單**」 。。

支援屬性清單自訂的應用程式和系統包括：

- Salesforce
- ServiceNow
- Workday
- Azure 動作目錄 (支援[Microsoft 圖像化 REST API v1.0 參考](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)與自訂目錄擴展)
- 支援 [SCIM 2.0](https://tools.ietf.org/html/rfc7643) 的應用程式，定義於[核心結構描述](https://tools.ietf.org/html/rfc7643)中的屬性必須新增至此處

> [!NOTE]
> 我們建議，只有已自訂應用程式和系統的結構描述，並確知其自訂屬性如何定義的系統管理員，才可編輯支援的屬性清單。 有時，這會需要熟悉應用程式或系統所提供的 API 和開發人員工具。

編輯支援的屬性清單時，必須提供下列屬性：

- **名稱** - 屬性的系統名稱，如目標物件的結構描述所定義。
- **類型**- 屬性儲存的資料型態,如目標物件的架構中定義,可以是以下類型之一:
  - *二進位* - 屬性包含二進位資料。
  - *布林值* - 屬性包含 True 或 False 值。
  - *日期時間* - 屬性包含日期字串。
  - *整數* - 屬性包含整數。
  - *參考* - 屬性包含對目標應用程式中的另一個資料表所儲存的值進行參考的識別碼。
  - *字串* - 屬性包含文字字串。
- **主鑰匙?** - 屬性是否定義為目標物件架構中的主鍵欄位。
- **必填?** - 是否需要在目標應用程式或系統中填充該屬性。
- **多值?** - 屬性是否支援多個值。
- **大小寫完全相符嗎？** - 是否以區分大小寫的方式計算屬性值。
- **API 表示式**- 除非特定預配連接器的文件(如工作日)的文件指示不要使用。
- **引用物件屬性**- 如果它是參考類型屬性,則此功能表允許您在目標應用程式中選擇包含與該屬性關聯的值的表和屬性。 例如，如果您有名為 "Department" 的屬性，且其儲存值參考了個別 "Departments" 資料表中的物件，則您會選取 "Departments.Name"。 給定應用程式支援的引用表和主 ID 欄位已預先配置,目前無法使用 Azure 門戶進行編輯,但可以使用[Microsoft 圖形 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)進行編輯。

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>將自訂延伸屬性預先設定到符合 SCIM 的應用程式
SCIM RFC 定義了核心使用者和組架構,同時還允許擴展架構以滿足應用程式的需求。 要向 SCIM 應用程式新增自訂屬性,請執行:
   1. 登入 Azure[活動目錄門戶](https://aad.portal.azure.com),選擇**企業應用程式**,選擇應用程式,然後選擇**預配**。
   2. 在 **「映射」** 下,選擇要為其添加自定義屬性的物件(使用者或組)。
   3. 在頁面底部,選擇 **「顯示高級選項**」。
   4. 為**應用名稱選擇"編輯屬性清單**"。
   5. 在屬性清單的底部,在提供的欄位中輸入有關自定義屬性的資訊。 然後選擇 **「添加屬性**」。

對於 SCIM 應用程式,屬性名稱必須遵循下面範例中所示的模式。 「自訂副檔名稱」和「自訂屬性」可以根據應用程式的要求進行自訂,例如:  
 * urn:ietf:參數:scim:架構:擴展:自定義副檔名稱:2.0:使用者:自定義屬性 
 * urn:ietf:參數:scim:架構:擴展:2.0:自定義擴展名稱:自定義屬性  
 * urn:ietf:參數:scim:架構:擴展:自定義副檔名稱:2.0:使用者.自定義屬性名稱:值

這些說明僅適用於支援 SCIM 的應用程式。 服務現在和 Salesforce 等應用程式不使用 SCIM 與 Azure AD 整合,因此在添加自訂屬性時不需要此特定的命名空間。

自定義屬性不能是引用屬性或多值屬性。 自定義多值擴展屬性目前僅支援庫中的應用程式。  
 
**具有延伸屬性的使用者的範例表示形式:**

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
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
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


## <a name="provisioning-a-role-to-a-scim-app"></a>將角色預先用 SCIM 應用
使用以下步驟為應用程式預配使用者的角色。 請注意,以下說明特定於自定義 SCIM 應用程式。 對於庫應用程式(如 Salesforce 和服務Now),請使用預定義的角色映射。 下面的項目符號描述如何將 AppRole 工作屬性轉換為應用程式期望的格式。

- 將 Azure AD 中的 AppRoleAssignment 映射到應用程式中的角色需要使用[運算式](../app-provisioning/functions-for-customizing-application-data.md)轉換屬性。 應用RoleAssignment屬性**不應直接映射到**角色屬性,而不使用運算式來分析角色詳細資訊。 

- **SingleAppRoleAssignment** 
  - **何時使用:** 使用 SingleAppRole 分配表達式為使用者預配單個角色並指定主要角色。 
  - **如何設定:** 使用上述步驟導航到屬性映射頁,並使用 SingleAppRole 分配運算到角色屬性。 有三個角色屬性可供選擇:(角色[主要 eq"True")顯示、角色[主 eq"True"類型和角色[主 eq"True"\值)。 您可以選擇在映射中包括任何或所有角色屬性。 如果要包含多個映射,只需添加新映射並將其作為目標屬性。  
  
  ![新增單應用程式角色分配](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **要考慮的事項**
    - 確保未將多個角色分配給使用者。 我們不能保證將預配哪個角色。
    
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
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **應用程式角色分配複雜** 
  - **何時使用:** 使用 AppRole 工作複雜表達式為使用者預配多個角色。 
  - **如何設定:** 編輯此支援的屬性清單,以包括角色的新屬性: 
  
    ![新增角色](./media/customize-application-attributes/add-roles.png)<br>

    然後使用 AppRole 工作複雜表示式映射到自定義角色屬性,如下圖所示:

    ![新增應用程式角色分配複雜](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **要考慮的事項**
    - 所有角色都將預配為主角色 = false。
    - POST 包含角色類型。 PATCH 請求不包含類型。 我們正在努力在 POST 和 PATCH 請求中發送類型。
    
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

  


## <a name="provisioning-a-multi-value-attribute"></a>預先多值屬性
某些屬性(如電話數位和電子郵件)是多值屬性,您可能需要在其中指定不同類型的電話號碼或電子郵件。 對多值屬性使用下面的運算式。 它允許您指定屬性類型並將其映射到該值的相應 Azure AD 使用者屬性。 

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

如果需要重新開始並將現有映射重置回其預設狀態,可以選擇「**還原預設映射**」複選框並保存配置。 這樣做會設置所有映射和範圍篩選器,就像應用程式剛剛從應用程式庫中添加到 Azure AD 租戶一樣。

選擇此選項將有效地強制在預配服務運行時重新同步所有使用者。

> [!IMPORTANT]
> 在調用此選項之前,我們強烈建議將**預配狀態**設置為 **"關閉**"。

## <a name="what-you-should-know"></a>您應該知道的事情

- Microsoft Azure AD 提供有效率的同步處理程序實作。 在初始化環境中，同步處理期間只會處理需要更新的物件。
- 更新屬性對應會影響同步處理期間的效能。 更新屬性對應組態需要重新評估所有受控物件。
- 建議的最佳做法是至少將屬性映射的連續更改數保持在最小。
- 現在不支援將要預配到應用的照片屬性添加,因為您無法指定同步照片的格式。 您可以在[使用者語音](https://feedback.azure.com/forums/169401-azure-active-directory)上要求該功能
- 屬性 IsSoftDeleted 通常是應用程式的預設映射的一部分。 IsSoftdelete 在四種情況之一中可能為 true(使用者由於未從應用程式分配而不在範圍範圍內,使用者由於不符合範圍範圍篩選器而退出範圍,使用者已在 Azure AD 中軟刪除,或者屬性帳戶啟用設置為 false 使用者)。 
- Azure AD 預配服務不支援預先定義的空值

## <a name="next-steps"></a>後續步驟

- [自動化 SaaS 應用程式使用者佈建/解除佈建](user-provisioning.md)
- [撰寫屬性對應的運算式](../app-provisioning/functions-for-customizing-application-data.md)
- [適用於使用者佈建的範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](use-scim-to-provision-users-and-groups.md)
- [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)
