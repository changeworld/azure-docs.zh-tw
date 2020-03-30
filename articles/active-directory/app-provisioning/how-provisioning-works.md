---
title: 瞭解 Azure AD 預配的工作原理 ›微軟文檔
description: 瞭解 Azure AD 預配的工作原理
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 555fb39836054be05102f4c28167d72016805639
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481495"
---
# <a name="how-provisioning-works"></a>佈建運作方式

自動預配是指在使用者需要訪問的雲應用程式中創建使用者身份和角色。 除了建立使用者身分識別以外，自動佈建還包括隨著狀態或角色變更，維護和移除使用者身分識別。 在開始部署之前，可以查看本文以瞭解 Azure AD 預配的工作原理並獲取配置建議。 

**Azure AD 預配服務**通過連接到應用程式供應商提供的跨域標識管理系統 （SCIM） 2.0 使用者管理 API 終結點，將使用者預配到 SaaS 應用和其他系統。 此 SCIM 終結點允許 Azure AD 以程式設計方式創建、更新和刪除使用者。 對於選定的應用程式，預配服務還可以創建、更新和刪除其他與標識相關的物件，如組和角色。 用於在 Azure AD 和應用程式之間預配的通道使用 HTTPS TLS 加密進行加密。


![Azure AD 預配](./media/how-provisioning-works/provisioning0.PNG)
服務*圖 1：Azure AD 預配服務*

![出站使用者預配工作流](./media/how-provisioning-works/provisioning1.PNG)
*圖 2："出站"使用者預配工作流從 Azure AD 到流行的 SaaS 應用程式*

![入站使用者預配工作流](./media/how-provisioning-works/provisioning2.PNG)
*圖 3："入站"使用者預配工作流，從流行的人力資本管理 （HCM） 應用程式到 Azure 活動目錄和 Windows 伺服器活動目錄*

## <a name="provisioning-using-scim-20"></a>使用 SCIM 2.0 進行預配

Azure AD 預配服務使用[SCIM 2.0 協定](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)進行自動預配。 該服務連接到應用程式的 SCIM 終結點，並使用 SCIM 使用者物件架構和 REST API 自動預配和取消預配使用者和組。 Azure AD 庫中的大多數應用程式都提供了基於 SCIM 的預配連接器。 為 Azure AD 構建應用時，開發人員可以使用 SCIM 2.0 使用者管理 API 構建集成 Azure AD 進行預配的 SCIM 終結點。 有關詳細資訊，請參閱[生成 SCIM 終結點並配置使用者預配](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

要為當前沒有應用的應用請求自動 Azure AD 預配連接器，請填寫[Azure 活動目錄應用程式請求](https://aka.ms/aadapprequest)。

## <a name="authorization"></a>授權

Azure AD 需要憑據才能連接到應用程式的使用者管理 API。 在為應用程式佈建自動使用者預配時，需要輸入有效的憑據。 通過引用應用教程，可以找到應用程式的憑據類型和要求。 在 Azure 門戶中，您可以通過讓 Azure AD 嘗試使用提供的憑據連接到應用的預配應用來測試憑據。

如果為應用程式也配置了基於 SAML 的單登錄，則 Azure AD 的內部每個應用程式存儲限制為 1024 位元組。 此限制包括與應用程式的單個實例（也稱為 Azure AD 中的服務主體記錄）關聯的所有證書、金鑰權杖、憑據和相關配置資料。 配置基於 SAML 的單登錄時，用於對 SAML 權杖進行簽名的證書通常會佔用超過 50% 的空間。 您在使用者預配設置期間輸入的任何其他專案（秘密權杖、URI、通知電子郵件地址、使用者名和密碼）都可能超過存儲限制。 有關詳細資訊，請參閱[在配置使用者預配時保存管理員憑據時出現問題](../manage-apps/application-provisioning-config-problem-storage-limit.md)。

## <a name="mapping-attributes"></a>映射屬性

為協力廠商 SaaS 應用程式啟用使用者預配時，Azure 門戶將通過屬性對應控制其屬性值。 映射確定在預配或更新使用者帳戶時在 Azure AD 和目標應用程式之間流動的使用者屬性。

Azure AD 使用者物件和每個 SaaS 應用的使用者物件之間有一組預先配置的屬性和屬性對應。 某些應用與"使用者"一起管理其他類型的物件，如"組"。

設置預配時，請務必查看和配置屬性對應和工作流，這些映射和工作流定義哪些使用者（或組）屬性從 Azure AD 流向應用程式。 查看並配置匹配屬性 （**使用此屬性匹配物件**），該屬性用於在兩個系統之間唯一標識和匹配使用者/組。

您可以根據您的業務需求自訂預設的屬性對應。 因此，您可以更改或刪除現有的屬性對應，或創建新的屬性對應。 有關詳細資訊，請參閱[為 SaaS 應用程式自訂使用者預配屬性對應](../manage-apps/customize-application-attributes.md)。

當您設定佈建到 SaaS 應用程式時，您可以指定的其中一種屬性對應類型是運算式對應。 對於這些映射，必須編寫類似于腳本的運算式，該運算式允許您將使用者的資料轉換為 SaaS 應用程式更可接受的格式。 有關詳細資訊，請參閱[為屬性對應編寫運算式](functions-for-customizing-application-data.md)。

## <a name="scoping"></a>範圍 
### <a name="assignment-based-scoping"></a>基於分配的範圍

對於從 Azure AD 到 SaaS 應用程式的出站預配，依賴[使用者或組分配](../manage-apps/assign-user-or-group-access-portal.md)是確定哪些使用者處於預配範圍內的最常見方法。 由於使用者分配也用於啟用單一登入，因此相同的方法可用於管理訪問和預配。 基於分配的範圍範圍不適用於入站預配方案，如工作日和成功因素。

* **組。** 使用 Azure AD 高級許可證計畫，可以使用組分配對 SaaS 應用程式的存取權限。 然後，當預配範圍設置為**僅同步分配的使用者和組**時，Azure AD 預配服務將根據使用者是否為分配給應用程式的組的成員預配或取消預配。 除非應用程式支援組物件，否則不會預配組物件本身。

* **動態組。** Azure AD 使用者預配服務可以在[動態組中](../users-groups-roles/groups-create-rule.md)讀取和預配使用者。 請記住以下注意事項和建議：

  * 動態組可能會影響從 Azure AD 到 SaaS 應用程式的端到端預配的性能。

  * 動態組中的使用者在 SaaS 應用程式中的預配或取消預配的速度取決於動態組評估成員身份更改的速度。 有關如何檢查動態組的處理狀態的資訊，請參閱[檢查成員資格規則的處理狀態](../users-groups-roles/groups-create-rule.md)。

  * 當使用者失去動態組中的成員身份時，它被視為取消預配事件。 在為動態組創建規則時，請考慮此方案。

* **嵌套組。** Azure AD 使用者預配服務無法讀取或預配嵌套組中的使用者。 該服務只能讀取和預配顯式分配的組的直接成員的使用者。 "基於組的應用程式分配"的這種限制還會影響單一登入（請參閱[使用組來管理對 SaaS 應用程式的訪問](../users-groups-roles/groups-saasapps.md)）。 相反，直接在包含需要預配的使用者的組中分配或以其他方式分配[作用域](define-conditional-rules-for-provisioning-user-accounts.md)。

### <a name="attribute-based-scoping"></a>基於屬性的範圍 

可以使用範圍篩選器定義基於屬性的規則，這些規則確定哪些使用者預配到應用程式。 此方法通常用於從 HCM 應用程式到 Azure AD 和活動目錄的入站預配。 系統會針對每個 Azure AD 使用者佈建連接器，將範圍篩選器設定為屬性對應的一部分。 有關配置基於屬性的範圍篩選器的詳細資訊，請參閱[使用範圍篩選器的基於屬性的應用程式預配](define-conditional-rules-for-provisioning-user-accounts.md)。

### <a name="b2b-guest-users"></a>B2B（來賓）使用者

可以使用 Azure AD 使用者預配服務將 Azure AD 中的 B2B（或來賓）使用者預配到 SaaS 應用程式。 但是，對於 B2B 使用者使用 Azure AD 登錄到 SaaS 應用程式，SaaS 應用程式必須以特定方式配置其基於 SAML 的單登錄功能。 若想進一步了解如何設定 SaaS 應用程式以支援 B2B 使用者的登入，請參閱[設定適用於 B2B 共同作業的 SaaS 應用程式](../b2b/configure-saas-apps.md)。

請注意，來賓使用者的使用者主要名稱通常存儲為"別名_EXT"。"。@domain.com 當使用者主體名稱作為源屬性包含在屬性對應中時，#EXT* 將從使用者主要名稱中剝離。 如果需要存在#EXT*，請將使用者主體名稱替換為原始使用者主體名稱作為源屬性。 

## <a name="provisioning-cycles-initial-and-incremental"></a>預配週期：初始和增量

當 Azure AD 是源系統時，預配服務使用[使用增量查詢來跟蹤 Microsoft 圖形資料中的更改](https://docs.microsoft.com/graph/delta-query-overview)來監視使用者和組。 預配服務針對源系統和目標系統運行初始週期，然後是週期性增量週期。

### <a name="initial-cycle"></a>初始週期

啟動預配服務時，第一個迴圈將：

1. 從來源系統查詢所有使用者和群組，其中會擷取[屬性對應](customize-application-attributes.md)中定義的所有屬性。

2. 使用任何已設定的[指派](../manage-apps/assign-user-or-group-access-portal.md)或[屬性型範圍設定篩選](define-conditional-rules-for-provisioning-user-accounts.md)來篩選所傳回的使用者和群組。

3. 分配使用者或處於預配範圍時，服務將使用指定的[匹配屬性](customize-application-attributes.md#understanding-attribute-mapping-properties)查詢匹配使用者的目標系統。 範例：如果來源系統中的 userPrincipal 名稱是比對屬性並與目標系統中的 userName 對應，則佈建服務會查詢目標系統中是否有與來源系統中 userPrincipal 名稱值相符的 userName。

4. 如果在目標系統中找不到匹配的使用者，則使用從源系統返回的屬性創建匹配使用者。 創建使用者帳戶後，預配服務將檢測並緩存新使用者的目標系統的 ID。 此 ID 用於運行該使用者的所有將來操作。

5. 如果找到匹配的使用者，則使用源系統提供的屬性進行更新。 匹配使用者帳戶後，預配服務將檢測並緩存新使用者的目標系統的 ID。 此 ID 用於運行該使用者的所有將來操作。

6. 如果屬性對應包含"引用"屬性，則服務在目標系統上執行其他更新以創建和連結引用的物件。 例如，使用者可能在目標系統中有 "Manager" 屬性，而此屬性連結至在目標系統中建立的另一個使用者。

7. 在初始週期結束時保留浮水印，為以後的增量週期提供起點。

某些應用程式（如 ServiceNow、G Suite 和 Box）不僅支援預配使用者，還支援預配組及其成員。 在這些情況下，如果在[映射](customize-application-attributes.md)中啟用了組預配，則預配服務將同步使用者和組，然後同步組成員身份。

### <a name="incremental-cycles"></a>增量週期

初始週期後，所有其他週期將：

1. 查詢來源系統中是否有任何自上次儲存浮水印之後更新的使用者和群組。

2. 使用任何已設定的[指派](../manage-apps/assign-user-or-group-access-portal.md)或[屬性型範圍設定篩選](define-conditional-rules-for-provisioning-user-accounts.md)來篩選所傳回的使用者和群組。

3. 分配使用者或處於預配範圍時，服務將使用指定的[匹配屬性](customize-application-attributes.md#understanding-attribute-mapping-properties)查詢匹配使用者的目標系統。

4. 如果在目標系統中找不到匹配的使用者，則使用從源系統返回的屬性創建匹配使用者。 創建使用者帳戶後，預配服務將檢測並緩存新使用者的目標系統的 ID。 此 ID 用於運行該使用者的所有將來操作。

5. 如果找到匹配的使用者，則使用源系統提供的屬性進行更新。 如果是匹配的新分配的帳戶，則預配服務將檢測並緩存新使用者的目標系統的 ID。 此 ID 用於運行該使用者的所有將來操作。

6. 如果屬性對應包含"引用"屬性，則服務在目標系統上執行其他更新以創建和連結引用的物件。 例如，使用者可能在目標系統中有 "Manager" 屬性，而此屬性連結至在目標系統中建立的另一個使用者。

7. 如果以前處於預配範圍的使用者從作用域中刪除（包括未分配），則服務將通過更新禁用目標系統中的使用者。

8. 如果將先前在佈建範圍中的使用者停用或在來源系統中虛刪除，則服務會透過更新，在目標系統中停用該使用者。

9. 如果將先前在佈建範圍中的使用者在來源系統中實刪除，則服務會在目標系統中刪除該使用者。 在 Azure AD 中，使用者在虛刪除 30 天后將實刪除。

10. 在增量週期結束時保留一個新的浮水印，為以後的增量週期提供起點。

> [!NOTE]
> 通過使用["映射](customize-application-attributes.md)"部分中**的目標物件操作**核取方塊，可以選擇禁用 **"創建**、**更新****"和"刪除**"操作。 在更新期間停用使用者的邏輯也是透過來自 "accountEnabled" 這類欄位的屬性對應來控制。

預配服務繼續無限期地連續運行增量週期，其間隔為[特定于每個應用程式的教程](../saas-apps/tutorial-list.md)中。 增量迴圈將繼續，直到發生以下事件之一：

- 使用 Azure 門戶或使用相應的 Microsoft 圖形 API 命令手動停止該服務。
- 使用 Azure 門戶中的 **"清除"狀態和重新開機**選項或使用相應的 Microsoft 圖形 API 命令觸發新的初始週期。 此操作清除任何存儲的浮水印，並會導致再次評估所有源物件。
- 由於屬性對應或範圍篩選器的更改，將觸發新的初始週期。 此操作還會清除任何存儲的浮水印，並會導致再次評估所有源物件。
- 由於錯誤率高，預配過程進入隔離區（見下文），並在隔離中停留超過四周。 在此事件中，將會自動停用服務。

### <a name="errors-and-retries"></a>錯誤和重試

如果目標系統中的錯誤阻止在目標系統中添加、更新或刪除單個使用者，則在下一個同步週期中重試該操作。 如果使用者持續發生失敗，則會開始降低重試頻率，逐漸調整回每天僅嘗試一次。 要解決故障，管理員必須檢查[預配日誌](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)以確定根本原因並採取相應的操作。 常見的錯誤可能包括：

- 來源系統中未填入目標系統中所需的使用者屬性
- 源系統中具有屬性值的使用者，該屬性值在目標系統中存在唯一約束，並且其他使用者記錄中存在相同的值

通過調整源系統中受影響使用者的屬性值或調整屬性對應以使其不會導致衝突來解決這些失敗。

### <a name="quarantine"></a>隔離

如果針對目標系統進行的大多數或所有調用始終由於錯誤（例如不正確管理員憑據）而失敗，則預配作業進入"隔離"狀態。 如果在 Azure 門戶中配置了電子郵件通知，則此狀態在[預配摘要報表](../manage-apps/check-status-user-account-provisioning.md)中指示，並通過電子郵件指示。

在隔離時，增量迴圈的頻率逐漸降低為每天一次。

修復所有違規錯誤並啟動下一個同步週期後，預配作業將退出隔離區。 如果佈建作業處於隔離狀態超過四週，系統就會將它停用。 在此處瞭解有關隔離狀態的更多[資訊](../manage-apps/application-provisioning-quarantine-status.md)。

### <a name="how-long-provisioning-takes"></a>佈建時間長短

性能取決於預配作業是運行初始預配週期還是增量週期。 有關預配需要多長時間以及如何監視預配服務的狀態的詳細資訊，請參閱[檢查使用者預配的狀態](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)。

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>如何判斷使用者是否配置正確

使用者預配服務運行的所有操作都記錄在 Azure AD[預配日誌（預覽）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)中。 日誌包括對源和目標系統進行的所有讀取和寫入操作，以及在每個操作期間讀取或寫入的使用者資料。 有關如何讀取 Azure 門戶中的預配日誌的資訊，請參閱[預配報告指南](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="de-provisioning"></a>去預配

Azure AD 預配服務通過在使用者不應再具有存取權限時取消預配帳戶來使源和目標系統保持同步。 

當應用程式支援虛刪除（使用活動 = false 更新要求）和發生以下任一事件時，Azure AD 預配服務將虛刪除應用程式中的使用者：

* 使用者帳戶在 Azure AD 中被刪除
*   使用者從應用程式取消分配
*   使用者不再滿足範圍篩選器並超出範圍
    * 預設情況下，Azure AD 預配服務虛刪除或禁用超出範圍的使用者。 如果要重寫此預設行為，可以設置一個標誌以[跳過範圍外刪除](../app-provisioning/skip-out-of-scope-deletions.md)。
*   啟用帳戶的屬性設置為"False"

如果發生上述四個事件之一，並且目標應用程式不支援虛刪除，則預配服務將發送 DELETE 請求以永久從應用中刪除使用者。 

在 Azure AD 中刪除使用者 30 天后，他們將從租戶中永久刪除。 此時，預配服務將發送 DELETE 請求以永久刪除應用程式中的使用者。 在 30 天視窗期間的任何時間，您可以[手動永久刪除使用者](../fundamentals/active-directory-users-restore.md)，該使用者會向應用程式發送刪除請求。

如果在屬性對應中看到屬性 IsSoftDelete，則用於確定使用者的狀態以及是否發送具有活動 = false 的更新要求以虛刪除使用者。 

## <a name="next-steps"></a>後續步驟

[規劃自動使用者佈建部署](../app-provisioning/plan-auto-user-provisioning.md)

[設定資源庫應用程式的佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)

[在創建自己的應用時構建 SCIM 終結點並配置預配](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[在將使用者配置和預配到應用程式時排除問題](../manage-apps/application-provisioning-config-problem.md)。
