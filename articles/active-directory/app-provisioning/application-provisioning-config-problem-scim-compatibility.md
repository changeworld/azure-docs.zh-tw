---
title: 系統用於跨網域身分識別管理的已知問題 (SCIM) 2.0 通訊協定合規性-Azure AD
description: 如何解決將支援 SCIM 2.0 且不在資源庫的應用程式新增至 Azure AD 時所面臨的常見通訊協定相容性問題
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/05/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 7f400d6959a40361ea3beff8bd21c2fa9ef2996a
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052625"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Azure AD 使用者佈建服務 SCIM 2.0 通訊協定相容性的已知問題和解決方法

Azure Active Directory (Azure AD) 會利用 [System for Cross-Domain Identity Management (SCIM) 2.0 通訊協定規格](https://tools.ietf.org/html/draft-ietf-scim-api-19)中定義的介面，自動佈建使用者和群組到 Web 服務前端的任何應用程式或系統。 

[使用 System for Cross-Domain Identity Management (SCIM) 自動將使用者和群組從 Azure Active Directory 佈建到應用程式](use-scim-to-provision-users-and-groups.md)中描述 Azure AD 對 SCIM 2.0 通訊協定的支援，並列出它所實作通訊協定的特定部分，以自動將使用者和群組從 Azure AD 佈建到支援 SCIM 2.0 的應用程式。

本文描述 Azure AD 使用者佈建服務在遵循 SCIM 2.0 通訊協定方面的目前和過去問題，並說明如何解決這些問題。

## <a name="understanding-the-provisioning-job"></a>瞭解布建作業
布建服務會使用作業的概念，對應用程式進行操作。 JobID 可在 [進度](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)列中找到。 所有新的布建應用程式都是使用開頭為 "scim" 的 jobID 來建立。 Scim 作業代表服務的目前狀態。 較舊的作業具有識別碼 "customappsso"。 這項作業代表服務在2018中的狀態。 

如果您使用資源庫中的應用程式，此作業通常會包含應用程式的名稱 (例如，縮放雪花、dataBricks 等 ) 。 使用資源庫應用程式時，您可以略過此檔。 這主要適用于具有 jobID SCIM 或 customAppSSO 的非資源庫應用程式。

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 相容性問題和狀態
在下表中，任何標示為固定的專案，都表示可以在 SCIM 作業中找到適當的行為。 我們已努力確保我們所做變更的回溯相容性。 但是，我們不建議您執行舊的行為。 建議您針對任何新的執行使用新的行為，並更新現有的實作為。

> [!NOTE]
> 針對2018中所做的變更，您可以還原回 customappsso 行為。 針對自2018之後所做的變更，您可以使用這些 Url 來還原為較舊的行為。 我們已設法確保我們所做變更的回溯相容性，讓您可以還原回舊的 jobID 或使用旗標。 不過，如先前所述，我們不建議您執行舊的行為。 建議您針對任何新的執行使用新的行為，並更新現有的實作為。

| **SCIM 2.0 相容性問題** |  **固定？** | **修正日期**  |  **回溯相容性** |
|---|---|---|
| Azure AD 要求應用程式的 SCIM 端點 URL 根目錄中必須有 "/scim"  | 是  |  2018 年 12 月 18 日 | 降級至 customappSSO |
| 延伸模組屬性在屬性名稱前面使用點 "." 標記法，而不是冒號 ":" 標記法 |  是  | 2018 年 12 月 18 日  | 降級至 customappSSO |
| 多重值屬性的修補程式要求包含無效的路徑篩選語法 | 是  |  2018 年 12 月 18 日  | 降級至 customappSSO |
| 群組建立要求包含無效的結構描述 URI | 是  |  2018 年 12 月 18 日  |  降級至 customappSSO |
| 更新修補程式列為以確保合規性 (例如 active as boolean 和適當的群組成員資格移除)  | 否 | TBD| 使用預覽旗標 |

## <a name="flags-to-alter-the-scim-behavior"></a>改變 SCIM 行為的旗標
在您應用程式的租使用者 URL 中使用下方的旗標，以變更預設的 SCIM 用戶端行為。

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="SCIM 旗標以供稍後的行為。":::

* 您可以使用下列 URL 來更新修補程式列為，並確保 SCIM 合規性 (例如 active as boolean，以及適當的群組成員資格移除) 。 目前只有在使用旗標時才能使用此行為，但會在接下來的幾個月內變成預設行為。 請注意，此預覽旗標目前不適用於隨選布建。 
  * **符合 SCIM 規範的 URL () ：** AzureAdScimPatch062020
  * **SCIM RFC 參考：** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **行為：**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"16b083c0-f1e8-4544-b6ee-27a28dc98761\"]"
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "members",
            "value": [
                {
                    "value": "10263a6910a84ef9a581dd9b8dcc0eae"
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "someone@contoso.com"
        },
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].primary",
            "value": true
        },
        {
            "op": "replace",
            "value": {
                "active": false,
                "userName": "someone"
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department",
            "value": "Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **降級 URL：** 一旦新的 SCIM 相容行為變成非資源庫應用程式的預設值，您就可以使用下列 URL 來回複為舊的不符合 SCIM 規範行為： AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>從較舊的 customappsso 作業升級至 SCIM 作業
遵循下列步驟將會刪除現有的 customappsso 作業，並建立新的 scim 作業。 
 
1. 登入 Azure 入口網站 https://portal.azure.com。
2. 在 Azure 入口網站的 [Azure Active Directory] > [企業應用程式]**** 區段中，尋找並選取您現有的 SCIM 應用程式。
3. 在您現有 SCIM 應用程式的 [屬性]**** 區段中，複製 [物件識別碼]****。
4. 在新的網頁瀏覽器視窗中，前往 https://developer.microsoft.com/graph/graph-explorer 並以新增應用程式所在的 Azure AD 租用戶系統管理員身分登入。
5. 在 [Graph 總管] 中，執行下列命令以尋找您佈建作業的識別碼。 將 "[object-id]" 取代為您從第三個步驟複製的服務主體識別碼 (物件識別碼)。
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![取得作業](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "取得作業") 


6. 在結果中，複製開頭為 "customappsso" 或 "scim" 的完整「識別碼」字串。
7. 執行下列命令以擷取屬性對應設定，以便進行備份。 使用上述相同的 [object-id]，並將 [job-id] 取代為您從上一個步驟複製的佈建作業識別碼。
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![取得架構](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "取得架構") 

8. 複製上一個步驟中的 JSON 輸出，並將其儲存至文字檔。 JSON 包含您新增至舊應用程式的任何自訂屬性對應，而且大約需要上千行的 JSON。
9. 執行下列命令以刪除佈建作業：
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. 執行下列命令以建立具有最新服務修正的新佈建作業。

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. 在上一個步驟的結果中，複製開頭為 "scim" 的完整「識別碼」字串。 （選擇性）執行下列命令以重新套用舊的屬性對應，並將 [new-job-id] 取代為您所複製的新作業識別碼，然後輸入步驟 #7 的 JSON 輸出作為要求主體。

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. 返回第一個網頁瀏覽器視窗，然後針對您的應用程式選取 [佈建]**** 索引標籤。
13. 驗證您的設定，然後啟動佈建作業。 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>不建議將 SCIM 作業降級為 customappsso 作業 () 
 我們可讓您降級為舊的行為，但不建議您這麼做，因為 customappsso 無法從我們所做的某些更新獲益，而且可能不會永遠受到支援。 

1. 登入 Azure 入口網站 https://portal.azure.com。
2. 在 Azure 入口網站的 [Azure Active Directory] > [企業應用程式] > [建立應用程式]**** 區段中，建立**不在資源庫內**的新應用程式。
3. 在新自訂應用程式的 [屬性]**** 區段中，複製 [物件識別碼]****。
4. 在新的網頁瀏覽器視窗中，前往 https://developer.microsoft.com/graph/graph-explorer 並以新增應用程式所在的 Azure AD 租用戶系統管理員身分登入。
5. 在 [Graph 總管] 中，執行下列命令以初始化應用程式的佈建設定。
   將 "[object-id]" 取代為您從第三個步驟複製的服務主體識別碼 (物件識別碼)。

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. 返回第一個網頁瀏覽器視窗，然後針對您的應用程式選取 [佈建]**** 索引標籤。
7. 如往常般完成使用者佈建設定。


## <a name="next-steps"></a>接下來的步驟
[深入了解對於 SaaS 應用程式的佈建和取消佈建](user-provisioning.md)
