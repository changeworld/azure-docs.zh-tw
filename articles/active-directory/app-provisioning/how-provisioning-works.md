---
title: 瞭解 Azure AD 布建如何運作 |Microsoft Docs
description: 瞭解 Azure AD 布建的運作方式
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
ms.openlocfilehash: 241d90981ed9ba54d253e6c22c00f9e5a9197863
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80884880"
---
# <a name="how-provisioning-works"></a>佈建運作方式

自動布建指的是在使用者需要存取的雲端應用程式中建立使用者身分識別和角色。 除了建立使用者身分識別以外，自動佈建還包括隨著狀態或角色變更，維護和移除使用者身分識別。 開始部署之前，您可以先參閱這篇文章，瞭解 Azure AD 布建的運作方式，並取得設定建議。 

Azure AD 布建**服務**會將使用者布建至 SaaS 應用程式和其他系統，方法是連接到系統，以進行應用程式廠商所提供的跨網域身分識別管理（SCIM）2.0 使用者管理 API 端點。 此 SCIM 端點可讓 Azure AD 以程式設計方式建立、更新和移除使用者。 對於選取的應用程式，布建服務也可以建立、更新和移除其他身分識別相關物件，例如群組和角色。 用來在 Azure AD 和應用程式之間布建的通道會使用 HTTPS TLS 加密進行加密。


![Azure AD 布建](./media/how-provisioning-works/provisioning0.PNG)
服務*圖1： Azure AD*布建服務

![輸出使用者布建](./media/how-provisioning-works/provisioning1.PNG)
工作流程*圖2：從 Azure AD 到熱門 SaaS 應用程式的「輸出」使用者布建工作流程*

![輸入使用者布建](./media/how-provisioning-works/provisioning2.PNG)
工作流程*圖3：從熱門人力資本管理（HCM）應用程式到 Azure Active Directory 和 Windows Server Active Directory 的「輸入」使用者布建工作流程*

## <a name="provisioning-using-scim-20"></a>使用 SCIM 2.0 進行布建

Azure AD 布建服務會使用[SCIM 2.0 通訊協定](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)來進行自動布建。 服務會連接到應用程式的 SCIM 端點，並使用 SCIM 使用者物件架構和 REST Api 來自動布建和取消布建使用者和群組。 SCIM 為基礎的布建連接器是針對 Azure AD 資源庫中的大部分應用程式所提供。 建立 Azure AD 的應用程式時，開發人員可以使用 SCIM 2.0 使用者管理 API 來建立整合 Azure AD 以提供布建的 SCIM 端點。 如需詳細資訊，請參閱[建立 SCIM 端點和設定使用者布建](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

若要為目前不存在的應用程式要求自動 Azure AD 布建連接器，請填寫[Azure Active Directory 的應用程式要求](https://aka.ms/aadapprequest)。

## <a name="authorization"></a>授權

Azure AD 需要認證，才能連接到應用程式的使用者管理 API。 當您設定應用程式的自動使用者布建時，您必須輸入有效的認證。 您可以藉由參考應用程式教學課程來尋找應用程式的認證類型和需求。 在 Azure 入口網站中，您可以藉由讓 Azure AD 嘗試使用提供的認證來連線到應用程式的布建應用程式，藉以測試認證。

如果應用程式也設定了 SAML 型單一登入，Azure AD 的內部，每個應用程式的儲存體限制為1024個位元組。 此限制包括與應用程式的單一實例相關聯的所有憑證、秘密權杖、認證和相關設定資料（在 Azure AD 中也稱為服務主體記錄）。 設定 SAML 型單一登入時，用來簽署 SAML 權杖的憑證通常會耗用超過50% 的空間。 您在使用者布建設定期間輸入的任何其他專案（秘密權杖、Uri、通知電子郵件地址、使用者名稱和密碼）可能會超過儲存空間限制。 如需詳細資訊，請參閱在設定使用者布建[時，儲存系統管理員認證時發生問題](../manage-apps/application-provisioning-config-problem-storage-limit.md)。

## <a name="mapping-attributes"></a>對應屬性

當您啟用協力廠商 SaaS 應用程式的使用者布建時，Azure 入口網站會透過屬性對應來控制其屬性值。 對應會決定在布建或更新使用者帳戶時，Azure AD 與目標應用程式之間流動的使用者屬性。

Azure AD 使用者物件和每個 SaaS 應用程式的使用者物件之間，有一組預先設定的屬性和屬性對應。 有些應用程式會管理其他類型的物件以及使用者，例如群組。

設定布建時，請務必檢查並設定屬性對應，以及定義哪些使用者（或群組）屬性會從 Azure AD 流向應用程式的工作流程。 檢查並設定比對屬性（**使用此屬性**比對物件），以用來唯一識別並比對兩個系統之間的使用者/群組。

您可以根據您的業務需求自訂預設的屬性對應。 因此，您可以變更或刪除現有的屬性對應，或建立新的屬性對應。 如需詳細資訊，請參閱[自訂 SaaS 應用程式的使用者布建屬性對應](../manage-apps/customize-application-attributes.md)。

當您設定佈建到 SaaS 應用程式時，您可以指定的其中一種屬性對應類型是運算式對應。 針對這些對應，您必須撰寫類似腳本的運算式，讓您將使用者的資料轉換成 SaaS 應用程式更容易接受的格式。 如需詳細資訊，請參閱[撰寫屬性對應的運算式](functions-for-customizing-application-data.md)。

## <a name="scoping"></a>範圍 
### <a name="assignment-based-scoping"></a>以指派為基礎的範圍

針對從 Azure AD 到 SaaS 應用程式的輸出布建，依賴[使用者或群組指派](../manage-apps/assign-user-or-group-access-portal.md)是判斷哪些使用者在布建範圍中最常見的方式。 因為使用者指派也用於啟用單一登入，所以相同的方法也可用來管理存取與布建。 以指派為基礎的範圍不適用於輸入布建案例，例如 Workday 和 Successfactors。

* **部門.** 有了 Azure AD Premium 的授權方案，您就可以使用群組來指派 SaaS 應用程式的存取權。 然後，當布建範圍設定為 [**僅同步指派的使用者和群組**] 時，Azure AD 布建服務會根據是否為指派給應用程式之群組的成員，來布建或取消布建使用者。 除非應用程式支援群組物件，否則不會布建群組物件本身。 請確定指派給應用程式的群組將屬性 "SecurityEnabled" 設定為 "False"。

* **動態群組。** Azure AD 的使用者布建服務可以讀取和布建[動態群組](../users-groups-roles/groups-create-rule.md)中的使用者。 請記住下列注意事項和建議：

  * 動態群組可能會影響從 Azure AD 到 SaaS 應用程式的端對端布建效能。

  * 在 SaaS 應用程式中布建或取消布建動態群組使用者的速度，取決於動態群組可評估成員資格變更的速度。 如需如何檢查動態群組之處理狀態的詳細資訊，請參閱[檢查成員資格規則的處理狀態](../users-groups-roles/groups-create-rule.md)。

  * 當使用者失去動態群組的成員資格時，它會被視為取消布建事件。 建立動態群組的規則時，請考慮這種情況。

* **嵌套的群組。** Azure AD 的使用者布建服務無法讀取或布建嵌套群組中的使用者。 此服務只能讀取和布建已明確指派群組之直屬成員的使用者。 「以群組為基礎的應用程式指派」的限制也會影響單一登入（請參閱[使用群組來管理 SaaS 應用程式的存取權](../users-groups-roles/groups-saasapps.md)）。 相反地，請直接在包含需要布建之使用者的群組[中，以](define-conditional-rules-for-provisioning-user-accounts.md)其他方式指派或範圍。

### <a name="attribute-based-scoping"></a>以屬性為基礎的範圍 

您可以使用範圍篩選器來定義以屬性為基礎的規則，以決定要將哪些使用者布建到應用程式。 這個方法通常用於從 HCM 應用程式到 Azure AD 和 Active Directory 的輸入布建。 系統會針對每個 Azure AD 使用者佈建連接器，將範圍篩選器設定為屬性對應的一部分。 如需設定以屬性為基礎之範圍篩選器的詳細資訊，請參閱[使用範圍篩選器的屬性型應用程式布建](define-conditional-rules-for-provisioning-user-accounts.md)。

### <a name="b2b-guest-users"></a>B2B （來賓）使用者

您可以使用 Azure AD 的使用者布建服務，在 Azure AD SaaS 應用程式中布建 B2B （或來賓）使用者。 不過，若要讓 B2B 使用者使用 Azure AD 登入 SaaS 應用程式，SaaS 應用程式必須以特定方式設定其 SAML 型單一登入功能。 若想進一步了解如何設定 SaaS 應用程式以支援 B2B 使用者的登入，請參閱[設定適用於 B2B 共同作業的 SaaS 應用程式](../b2b/configure-saas-apps.md)。

請注意，來賓使用者的 userPrincipalName 通常會儲存為「別名 # EXT #@domain.com」。 當您的屬性對應中包含 userPrincipalName 做為來源屬性時，會從 userPrincipalName 中移除 #EXT #。 如果您需要 #EXT #，請將 userPrincipalName 取代為來源屬性的 originalUserPrincipalName。 

## <a name="provisioning-cycles-initial-and-incremental"></a>布建週期：初始和增量

當 Azure AD 為來源系統時，布建服務會使用 [[使用差異查詢] 來追蹤 Microsoft Graph 資料中的變更](https://docs.microsoft.com/graph/delta-query-overview)，以監視使用者和群組。 布建服務會對來源系統和目標系統執行初始迴圈，後面接著週期性增量迴圈。

### <a name="initial-cycle"></a>初始迴圈

當布建服務啟動時，第一個迴圈將會：

1. 從來源系統查詢所有使用者和群組，其中會擷取[屬性對應](customize-application-attributes.md)中定義的所有屬性。

2. 使用任何已設定的[指派](../manage-apps/assign-user-or-group-access-portal.md)或[屬性型範圍設定篩選](define-conditional-rules-for-provisioning-user-accounts.md)來篩選所傳回的使用者和群組。

3. 當使用者被指派或在布建範圍內時，服務會使用指定的比對[屬性](customize-application-attributes.md#understanding-attribute-mapping-properties)來查詢目標系統中是否有相符的使用者。 範例：如果來源系統中的 userPrincipal 名稱是比對屬性並與目標系統中的 userName 對應，則佈建服務會查詢目標系統中是否有與來源系統中 userPrincipal 名稱值相符的 userName。

4. 如果在目標系統中找不到相符的使用者，則會使用從來源系統傳回的屬性來建立。 建立使用者帳戶之後，布建服務會偵測並快取新使用者的目標系統識別碼。 這個識別碼是用來執行該使用者所有未來的作業。

5. 如果找到相符的使用者，則會使用來源系統所提供的屬性來更新它。 使用者帳戶符合後，布建服務會偵測並快取新使用者的目標系統識別碼。 這個識別碼是用來執行該使用者所有未來的作業。

6. 如果屬性對應包含 "reference" 屬性，服務會在目標系統上進行其他更新，以建立及連結參考的物件。 例如，使用者可能在目標系統中有 "Manager" 屬性，而此屬性連結至在目標系統中建立的另一個使用者。

7. 在初始週期結束時保存浮水印，以提供後續累加迴圈的起點。

某些應用程式（例如 ServiceNow、G Suite 和 Box）不僅支援布建使用者，還能提供群組和其成員。 在這些情況下，如果已在對應中啟用群組布[建，布](customize-application-attributes.md)建服務會同步處理使用者和群組，然後在稍後同步處理群組成員資格。

### <a name="incremental-cycles"></a>增量迴圈

在初始迴圈之後，所有其他週期將會：

1. 查詢來源系統中是否有任何自上次儲存浮水印之後更新的使用者和群組。

2. 使用任何已設定的[指派](../manage-apps/assign-user-or-group-access-portal.md)或[屬性型範圍設定篩選](define-conditional-rules-for-provisioning-user-accounts.md)來篩選所傳回的使用者和群組。

3. 當使用者被指派或在布建範圍內時，服務會使用指定的比對[屬性](customize-application-attributes.md#understanding-attribute-mapping-properties)來查詢目標系統中是否有相符的使用者。

4. 如果在目標系統中找不到相符的使用者，則會使用從來源系統傳回的屬性來建立。 建立使用者帳戶之後，布建服務會偵測並快取新使用者的目標系統識別碼。 這個識別碼是用來執行該使用者所有未來的作業。

5. 如果找到相符的使用者，則會使用來源系統所提供的屬性來更新它。 如果它是與新指派的帳戶，則布建服務會偵測並快取新使用者的目標系統識別碼。 這個識別碼是用來執行該使用者所有未來的作業。

6. 如果屬性對應包含 "reference" 屬性，服務會在目標系統上進行其他更新，以建立及連結參考的物件。 例如，使用者可能在目標系統中有 "Manager" 屬性，而此屬性連結至在目標系統中建立的另一個使用者。

7. 如果您先前在布建範圍中的使用者已從範圍中移除，包括未指派，則服務會透過更新來停用目標系統中的使用者。

8. 如果將先前在佈建範圍中的使用者停用或在來源系統中虛刪除，則服務會透過更新，在目標系統中停用該使用者。

9. 如果將先前在佈建範圍中的使用者在來源系統中實刪除，則服務會在目標系統中刪除該使用者。 在 Azure AD 中，使用者會在虛刪除之後的30天內被實刪除。

10. 在累加迴圈結束時保存新的浮水印，以提供稍後增量迴圈的起點。

> [!NOTE]
> 您可以使用 [對應[] 區段中的 [](customize-application-attributes.md) **目標物件動作**] 核取方塊，選擇性地停用**建立**、**更新**或**刪除**作業。 在更新期間停用使用者的邏輯也是透過來自 "accountEnabled" 這類欄位的屬性對應來控制。

布建服務會依[每個應用程式特定的教學](../saas-apps/tutorial-list.md)課程中所定義的間隔，無限期地繼續執行回送增量迴圈。 增量迴圈會繼續進行，直到發生下列其中一個事件為止：

- 使用 Azure 入口網站或使用適當的 Microsoft Graph API 命令，手動停止服務。
- 使用 Azure 入口網站中的 [**清除狀態] 和 [重新開機**] 選項，或使用適當的 Microsoft Graph API 命令來觸發新的初始迴圈。 此動作會清除所有儲存的浮水印，並再次評估所有來源物件。
- 因為屬性對應或範圍篩選準則變更，所以會觸發新的初始迴圈。 此動作也會清除所有儲存的浮水印，並再次評估所有來源物件。
- 布建程式會進入隔離狀態（如下所示），因為這是較高的錯誤率，而且會持續隔離超過四周。 在此事件中，將會自動停用服務。

### <a name="errors-and-retries"></a>錯誤和重試

如果目標系統中的錯誤導致無法在目標系統中新增、更新或刪除個別使用者，則會在下一個同步處理週期中重試作業。 如果使用者持續發生失敗，則會開始降低重試頻率，逐漸調整回每天僅嘗試一次。 若要解決此錯誤，系統管理員必須檢查布建[記錄](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)以判斷根本原因，並採取適當的動作。 常見的錯誤可能包括：

- 來源系統中未填入目標系統中所需的使用者屬性
- 在來源系統中有一個屬性值的使用者在目標系統中有唯一的條件約束，而且相同的值會出現在另一個使用者記錄中

藉由調整來源系統中受影響使用者的屬性值，或藉由調整屬性對應，讓它們不會造成衝突，來解決這些失敗。

### <a name="quarantine"></a>隔離

如果對目標系統進行的大部分或所有呼叫一直因為錯誤而失敗（例如，系統管理員認證無效），布建作業會進入「隔離」狀態。 如果在 Azure 入口網站中設定了電子郵件通知，則會在布建[摘要報告](../manage-apps/check-status-user-account-provisioning.md)中指出此狀態，以及透過電子郵件傳送。

在隔離時，增量迴圈的頻率會逐漸縮減為每天一次。

當所有違規的錯誤都固定並啟動下一個同步處理週期之後，布建作業就會結束隔離。 如果佈建作業處於隔離狀態超過四週，系統就會將它停用。 在這裡深入瞭解[隔離狀態。](../manage-apps/application-provisioning-quarantine-status.md)

### <a name="how-long-provisioning-takes"></a>佈建時間長短

效能取決於您的布建作業是執行初始布建週期還是增量迴圈。 如需布建花費時間和如何監視布建服務狀態的詳細資訊，請參閱[檢查使用者](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)布建的狀態。

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>如何判斷是否已正確布建使用者

使用者布建服務所執行的所有作業都會記錄在 Azure AD 布建[記錄（預覽）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)中。 這些記錄包含對來源和目標系統進行的所有讀取和寫入作業，以及在每一項作業期間讀取或寫入的使用者資料。 如需有關如何讀取 Azure 入口網站中布建記錄的詳細資訊，請參閱布建[報告指南](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="de-provisioning"></a>解除布建

當使用者不再需要存取時，Azure AD 布建服務會將來源和目標系統保持同步，方法是解除布建帳戶。 

當應用程式 suupports 虛刪除（具有 active = false 的 update 要求）且發生下列任何事件時，Azure AD 布建服務會將應用程式中的使用者虛刪除：

* 使用者帳戶會在中刪除 Azure AD
*   使用者已從應用程式取消指派
*   使用者不再符合範圍篩選器，因此超出範圍
    * 根據預設，Azure AD 布建服務會虛刪除或停用超出範圍的使用者。 如果您想要覆寫此預設行為，您可以設定旗標來[略過超出範圍的刪除](../app-provisioning/skip-out-of-scope-deletions.md)。
*   AccountEnabled 屬性設定為 False

如果發生上述四個事件的其中一個，而目標應用程式不支援虛刪除，則布建服務會傳送刪除要求，以從應用程式永久刪除使用者。 

在 Azure AD 中刪除使用者後的30天后，就會從租使用者中永久刪除它們。 此時，布建服務會傳送刪除要求，以永久刪除應用程式中的使用者。 在30天的時間範圍內，您可以隨時[手動刪除使用者](../fundamentals/active-directory-users-restore.md)，這會將刪除要求傳送至應用程式。

如果您在屬性對應中看到屬性 IsSoftDeleted，則會使用它來判斷使用者的狀態，以及是否要傳送具有 active = false 的更新要求，以虛刪除使用者。 

## <a name="next-steps"></a>後續步驟

[規劃自動使用者佈建部署](../app-provisioning/plan-auto-user-provisioning.md)

[設定資源庫應用程式的佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)

[建立 SCIM 端點，並在建立您自己的應用程式時設定布建](../app-provisioning/use-scim-to-provision-users-and-groups.md)

針對設定和布建[使用者至應用程式的問題進行疑難排解](../manage-apps/application-provisioning-config-problem.md)。
