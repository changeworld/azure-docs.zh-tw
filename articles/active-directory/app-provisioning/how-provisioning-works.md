---
title: 了解 Azure AD 佈建的運作方式 | Microsoft Docs
description: 了解 Azure AD 佈建的運作方式
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 05/20/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperfq2
ms.openlocfilehash: c9d8bf42d8856ffcf7bb0247172f6c0fd49600e0
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424653"
---
# <a name="how-provisioning-works"></a>佈建運作方式

自動佈建是指在使用者需要存取的雲端應用程式中建立使用者身分識別和角色。 除了建立使用者身分識別以外，自動佈建還包括隨著狀態或角色變更，維護和移除使用者身分識別。 開始部署之前，您可以先參閱本文，以了解 Azure AD 佈建的運作方式並取得設定建議。 

**Azure AD 佈建服務**會藉由連線至應用程式廠商所提供的跨網域身分識別管理系統 (SCIM) 2.0 使用者管理 API 端點，將使用者佈建至 SaaS 應用程式和其他系統。 這個 SCIM 端點可讓 Azure AD 以程式設計方式建立、更新和移除使用者。 針對所選應用程式，佈建服務也可以建立、更新和移除其他與身分識別相關的物件，例如群組和角色。 Azure AD 與應用程式之間的佈建所使用的通道，會使用 HTTPS TLS 1.2 加密進行加密。


![Azure AD 佈建服務](./media/how-provisioning-works/provisioning0.PNG)
*圖 1：Azure AD 佈建服務*

![輸出使用者佈建工作流程](./media/how-provisioning-works/provisioning1.PNG)
*圖 2：從 Azure AD 至熱門 SaaS 應用程式的「輸出」使用者佈建工作流程*

![輸入使用者佈建工作流程](./media/how-provisioning-works/provisioning2.PNG)
*圖 3：從熱門人力資本管理 (HCM) 應用程式至 Azure Active Directory 和 Windows Server Active Directory 的「輸入」使用者佈建工作流程*

## <a name="provisioning-using-scim-20"></a>使用 SCIM 2.0 進行佈建

Azure AD 佈建服務會使用 [SCIM 2.0 通訊協定](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)進行自動佈建。 服務會連線至應用程式的 SCIM 端點，並使用 SCIM 使用者物件結構描述和 REST API 自動進行使用者和群組的佈建和取消佈建。 以 SCIM 為基礎的佈建連接器可供 Azure AD 資源庫中大部分的應用程式使用。 建置 Azure AD 的應用程式時，開發人員可使用 SCIM 2.0 使用者管理 API 建立整合了 Azure AD 的 SCIM 端點，以進行佈建。 如需詳細資訊，請參閱[建置 SCIM 端點和設定使用者佈建](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

若要為目前沒有自動 Azure AD 佈建連接器的應用程式要求一個連接器，請填寫 [Azure Active Directory 應用程式要求](https://aka.ms/aadapprequest)。

## <a name="authorization"></a>授權

Azure AD 連線至應用程式的使用者管理 API 時所需的認證。 在設定應用程式的自動使用者佈建時，您必須輸入有效的認證。 您可以參考應用程式教學課程，以尋找應用程式的認證類型和需求。 在 Azure 入口網站中，您可以讓 Azure AD 使用提供的認證嘗試連線至應用程式的佈建應用程式，以測試認證。

如果應用程式也設定了 SAML 型單一登入，Azure AD 的內部、每個應用程式的儲存體限制將是 1024 個位元組。 此限制包含所有憑證、祕密權杖、認證，以及與單一應用程式執行個體關聯的相關設定資料 (在 Azure AD 中也稱為服務主體記錄)。 在設定了 SAML 型單一登入時，用來簽署 SAML 權杖的憑證通常會耗用超過 50% 的空間。 您在使用者佈建設定期間輸入的任何其他項目 (秘密權杖、URI、通知電子郵件地址、使用者名稱和密碼) 都可能超過儲存體限制。 如需詳細資訊，請參閱[在設定使用者佈建期間儲存管理員認證時發生問題](./application-provisioning-config-problem-storage-limit.md)。

## <a name="mapping-attributes"></a>對應屬性

如果您啟用了第三方 SaaS 應用程式的使用者佈建，Azure 入口網站將會透過屬性對應控制其屬性值。 對應會決定在佈建或更新使用者帳戶時，在 Azure AD 與目標應用程式之間流動的使用者屬性。

在 Azure AD 使用者物件和每個 SaaS 應用程式的使用者物件之間，有一組預先設定的屬性和屬性對應。 有些應用程式除了使用者以外，還會管理其他類型的物件，例如群組。

在設定佈建時，請務必檢視和設定屬性 (Attribute) 對應，以及定義哪些使用者 (或群組) 屬性 (Property) 會從 Azure AD 流向應用程式的工作流程。 請檢視並設定在兩個系統之間用來唯一識別和比對使用者/群組的比對屬性 (**使用此屬性來比對物件**)。

您可以根據您的業務需求自訂預設的屬性對應。 因此，您可以變更或刪除現有的屬性對應，或建立新的屬性對應。 如需詳細資訊，請參閱[自訂 SaaS 應用程式的使用者佈建屬性對應](./customize-application-attributes.md)。

當您設定佈建到 SaaS 應用程式時，您可以指定的其中一種屬性對應類型是運算式對應。 針對這些對應，您必須撰寫類似指令碼的運算式，以便將使用者的資料轉換為 SaaS 應用程式更能接受的格式。 如需詳細資訊，請參閱[撰寫屬性對應的運算式](functions-for-customizing-application-data.md)。

## <a name="scoping"></a>範圍 
### <a name="assignment-based-scoping"></a>以指派為基礎的範圍

針對從 Azure AD 到 SaaS 應用程式的輸出佈建，[使用者或群組指派](../manage-apps/assign-user-or-group-access-portal.md)是判斷哪些使用者位於佈建範圍之中時最常用的依據。 由於使用者指派也可用來啟用單一登入，因此可以使用相同的方法來管理存取與佈建。 以指派為基礎的範圍不適用於輸入佈建案例，例如 Workday 和 Successfactors。

* **群組。** 透過 Azure AD Premium 授權方案，您可以使用群組指派對 SaaS 應用程式的存取權。 然後，當佈建範圍設定為 [只同步已指派的使用者和群組] 時，Azure AD 佈建服務將會根據使用者是否為已指派給應用程式的群組成員，來佈建或取消佈建使用者。 除非應用程式支援群組物件，否則不會佈建群組物件本身。 請確定指派給應用程式的群組將 "SecurityEnabled" 屬性設定為 "True"。

* **動態群組。** Azure AD 使用者佈建服務可以讀取和佈建[動態群組](../enterprise-users/groups-create-rule.md)中的使用者。 請留意下列注意事項和建議：

  * 對於從 Azure AD 到 SaaS 應用程式的端對端佈建，動態群組可能會影響到其效能。

  * 在 SaaS 應用程式中佈建或取消佈建動態群組使用者的速度，取決於動態群組評估成員資格變更的速度。 如需如何檢查動態群組處理狀態的相關資訊，請參閱[檢查成員資格規則的處理狀態](../enterprise-users/groups-create-rule.md)。

  * 當使用者失去動態群組的成員資格時，將會被視為取消佈建事件。 建立動態群組的規則時，請考量這種情況。

* **巢狀群組。** Azure AD 使用者佈建服務無法讀取或佈建巢狀群組中的使用者。 此服務只能讀取和佈建在明確指派的群組中屬於直接成員的使用者。 這種「應用程式的群組型指派」的限制對單一登入也會產生影響 (請參閱[使用群組管理 SaaS 應用程式的存取權](../enterprise-users/groups-saasapps.md))。 此時應明確指派所含的使用者需要佈建的群組，或[設定範圍](define-conditional-rules-for-provisioning-user-accounts.md)。

### <a name="attribute-based-scoping"></a>以屬性為基礎的範圍 

您可以使用範圍篩選器來定義以屬性為基礎的規則，以決定要將哪些使用者佈建到應用程式。 從 HCM 應用程式到 Azure AD 和 Active Directory 的輸入佈建常會使用此方法。 系統會針對每個 Azure AD 使用者佈建連接器，將範圍篩選器設定為屬性對應的一部分。 若要進一步了解如何設定以屬性為基礎的範圍篩選器，請參閱[含範圍篩選器的屬性型應用程式佈建](define-conditional-rules-for-provisioning-user-accounts.md)。

### <a name="b2b-guest-users"></a>B2B (來賓) 使用者

您可以使用 Azure AD 使用者佈建服務，將 Azure AD 中的 B2B (或來賓) 使用者佈建到 SaaS 應用程式。 不過，若要讓 B2B 使用者使用 Azure AD 登入 SaaS 應用程式，SaaS 應用程式必須以特定方式設定其 SAML 型單一登入功能。 若想進一步了解如何設定 SaaS 應用程式以支援 B2B 使用者的登入，請參閱[設定適用於 B2B 共同作業的 SaaS 應用程式](../external-identities/configure-saas-apps.md)。

請注意，來賓使用者的 userPrincipalName 通常會儲存為 "alias#EXT#@domain.com"。 如果您的屬性對應中包含 userPrincipalName 作為來源屬性時，則會從 userPrincipalName 中去除 #EXT#。 如果需要有 #EXT# 存在，請將 userPrincipalName 取代為作為來源屬性的 originalUserPrincipalName。 

## <a name="provisioning-cycles-initial-and-incremental"></a>佈建週期：初始和增量

如果 Azure AD 是來源系統，則佈建服務會使用[使用差異查詢來追蹤 Microsoft Graph 資料中的變更](/graph/delta-query-overview)，以監視使用者和群組。 佈建服務會先對來源系統和目標系統執行初始週期，後續再執行定期增量週期。

### <a name="initial-cycle"></a>初始週期

當佈建服務啟動時，第一個週期將會：

1. 從來源系統查詢所有使用者和群組，其中會擷取[屬性對應](customize-application-attributes.md)中定義的所有屬性。

2. 使用任何已設定的[指派](../manage-apps/assign-user-or-group-access-portal.md)或[屬性型範圍設定篩選](define-conditional-rules-for-provisioning-user-accounts.md)來篩選所傳回的使用者和群組。

3. 已指派使用者進行佈建或使用者位於佈建範圍內時，服務會使用指定的[比對屬性](customize-application-attributes.md#understanding-attribute-mapping-properties)查詢目標系統中是否有相符的使用者。 範例：如果來源系統中的 userPrincipal 名稱是比對屬性並與目標系統中的 userName 對應，則佈建服務會查詢目標系統中是否有與來源系統中 userPrincipal 名稱值相符的 userName。

4. 如果在目標系統中找不到相符的使用者，就會使用從來源系統傳回的屬性來建立該使用者。 使用者帳戶建立後，佈建服務會偵測並快取新使用者的目標系統識別碼。 此識別碼會用來執行該使用者所有未來的作業。

5. 如果找到相符的使用者，則會使用來源系統所提供的屬性來更新該使用者。 比對使用者帳戶後，佈建服務會偵測並快取新使用者的目標系統識別碼。 此識別碼會用來執行該使用者所有未來的作業。

6. 如果屬性對應包含「參考」屬性，服務就會在目標系統上執行額外的更新來建立和連結所參考的物件。 例如，使用者可能在目標系統中有 "Manager" 屬性，而此屬性連結至在目標系統中建立的另一個使用者。

7. 在初始週期結束時保存浮水印，以為後續的增量週期提供起點。

有些應用程式 (例如 ServiceNow、G Suite 和 Box) 不僅支援佈建使用者，也支援佈建群組及其成員。 在這些情況下，如果已在[對應](customize-application-attributes.md)中啟用群組佈建，佈建服務就會同步處理使用者和群組，然後接著同步處理群組成員資格。

### <a name="incremental-cycles"></a>增量週期

在初始週期之後，其他所有的週期將會：

1. 查詢來源系統中是否有任何自上次儲存浮水印之後更新的使用者和群組。

2. 使用任何已設定的[指派](../manage-apps/assign-user-or-group-access-portal.md)或[屬性型範圍設定篩選](define-conditional-rules-for-provisioning-user-accounts.md)來篩選所傳回的使用者和群組。

3. 已指派使用者進行佈建或使用者位於佈建範圍內時，服務會使用指定的[比對屬性](customize-application-attributes.md#understanding-attribute-mapping-properties)查詢目標系統中是否有相符的使用者。

4. 如果在目標系統中找不到相符的使用者，就會使用從來源系統傳回的屬性來建立該使用者。 使用者帳戶建立後，佈建服務會偵測並快取新使用者的目標系統識別碼。 此識別碼會用來執行該使用者所有未來的作業。

5. 如果找到相符的使用者，則會使用來源系統所提供的屬性來更新該使用者。 如果比對出新指派的帳戶，則佈建服務會偵測並快取新使用者的目標系統識別碼。 此識別碼會用來執行該使用者所有未來的作業。

6. 如果屬性對應包含「參考」屬性，服務就會在目標系統上執行額外的更新來建立和連結所參考的物件。 例如，使用者可能在目標系統中有 "Manager" 屬性，而此屬性連結至在目標系統中建立的另一個使用者。

7. 如果將先前在佈建範圍中的使用者從範圍中移除 (包括解除指派)，則服務會透過更新，在目標系統中停用該使用者。

8. 如果將先前在佈建範圍中的使用者停用或在來源系統中虛刪除，則服務會透過更新，在目標系統中停用該使用者。

9. 如果將先前在佈建範圍中的使用者在來源系統中實刪除，則服務會在目標系統中刪除該使用者。 在 Azure AD 中，會在將使用者虛刪除 30 天之後將他們實刪除。

10. 在增量週期結束時保存新的浮水印，以為後續的增量週期提供起點。

> [!NOTE]
> 您可以使用[對應](customize-application-attributes.md)區段中的 [目標物件動作] 核取方塊，視需要停用**建立**、**更新**或**刪除**作業。 在更新期間停用使用者的邏輯也是透過來自 "accountEnabled" 這類欄位的屬性對應來控制。

佈建服務將會依據[每個應用程式特定的教學課程](../saas-apps/tutorial-list.md)中所定義的間隔，繼續無限期執行連續的增量週期。 增量週期會繼續進行，直到發生下列其中一個事件為止：

- 使用 Azure 入口網站或使用適當的 Microsoft Graph API 以手動方式停止服務。
- 使用 Azure 入口網站中的 [清除狀態並重新啟動] 選項，或使用適當的 Microsoft Graph API 命令，來觸發新的初始週期。 此動作也會清除所有已儲存的浮水印，並導致重新評估所有來源物件。
- 由於屬性對應或範圍設定篩選發生變更，而觸發新的初始週期。 此動作也會清除所有已儲存的浮水印，並導致重新評估所有來源物件。
- 佈建程序因為高錯誤率而進入隔離狀態 (參見下方)，並處於隔離狀態超過四週。 在此事件中，將會自動停用服務。

### <a name="errors-and-retries"></a>錯誤和重試

如果目標系統中的錯誤導致無法在目標系統中新增、更新或刪除個別使用者，則會在下一個同步週期中重試作業。 如果使用者持續發生失敗，則會開始降低重試頻率，逐漸調整回每天僅嘗試一次。 若要解決此錯誤，管理員必須檢查[佈建記錄](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)以判斷根本原因，並採取適當的動作。 常見的錯誤可能包括：

- 來源系統中未填入目標系統中所需的使用者屬性
- 使用者在來源系統中有一個在目標系統中有唯一條件約束的屬性值，而相同值存在於另一個使用者記錄中

請調整來源系統中受影響使用者的屬性值，或將屬性對應調整成不會造成衝突，以解決這些失敗。

### <a name="quarantine"></a>隔離

如果對目標系統進行的多數或所有呼叫持續因錯誤而失敗 (例如，管理員認證無效)，佈建作業就會進入「隔離」狀態。 在[佈建摘要報告](./check-status-user-account-provisioning.md)中會指出此狀態，或如果已在 Azure 入口網站中設定電子郵件通知，則會透過電子郵件指出。

處於隔離狀態時，增量週期的頻率會逐漸降低成一天一次。

在修正所有違規錯誤之後，佈建作業就會脫離隔離狀態，並開始下一個資料同步週期。 如果佈建作業處於隔離狀態超過四週，系統就會將它停用。 若要深入了解隔離狀態，請參閱[這裡](./application-provisioning-quarantine-status.md)。

### <a name="how-long-provisioning-takes"></a>佈建時間長短

效能取決於佈建作業是執行的是初始佈建週期還是增量週期。 如需佈建所需時間和如何監視佈建服務狀態的詳細資訊，請參閱[檢查使用者佈建的狀態](application-provisioning-when-will-provisioning-finish-specific-user.md)。

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>如何判斷是否已正確佈建使用者

使用者佈建服務所執行的所有作業，都會記錄在 Azure AD [佈建記錄 (預覽)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 中。 這些記錄包含對來源和目標系統所做的所有讀取和寫入作業，以及在每次作業期間讀取或寫入的使用者資料。 如需如何在 Azure 入口網站中讀取佈建記錄的相關資訊，請參閱[佈建報告指南](./check-status-user-account-provisioning.md)。

## <a name="de-provisioning"></a>取消佈建
Azure AD 布建服務會在移除使用者存取權時，透過解除布建帳戶讓來源和目標系統保持同步。

布建服務支援刪除和停用 (有時也稱為虛刪除) 使用者。 [停用] 和 [刪除] 的確切定義會根據目標應用程式的執行而有所不同，但通常是 [停用] 表示使用者無法登入。 刪除表示使用者已完全從應用程式移除。 針對 SCIM 應用程式，停用是在使用者上將 *使用中屬性設* 為 false 的要求。 

**設定您的應用程式以停用使用者**

確定您已選取 [更新] 核取方塊。

確定您的應用程式有作用 *中的對應* 。 如果您是使用應用程式資源庫中的應用程式，則對應可能會稍有不同。 請確定您使用的是資源庫應用程式的預設/外框對應。

:::image type="content" source="./media/how-provisioning-works/disable-user.png" alt-text="停用使用者" lightbox="./media/how-provisioning-works/disable-user.png":::


**設定您的應用程式以刪除使用者**

下列案例將會觸發停用或刪除： 
* 在 Azure AD 中，使用者會被虛刪除， (傳送至 [回收站]/[AccountEnabled] 屬性設定為 [false]) 。
    在 Azure AD 中刪除使用者的 30 天後，就會從租用戶中永久刪除使用者。 此時，佈建服務會傳送 DELETE 要求，以永久刪除應用程式中的使用者。 在 30 天的時間範圍內，您可以[手動永久刪除使用者](../fundamentals/active-directory-users-restore.md)，此動作會將刪除要求傳送至應用程式。
* 使用者會在 Azure AD 中永久刪除/刪除回收站。
* 從應用程式解除指派使用者。
* 使用者從範圍移至超出範圍 (不會再傳遞範圍篩選器) 。

:::image type="content" source="./media/how-provisioning-works/delete-user.png" alt-text="停用使用者" lightbox="./media/how-provisioning-works/delete-user.png":::

根據預設，Azure AD 佈建服務會虛刪除或停用超出範圍的使用者。 如果您想要覆寫此預設行為，您可以設定旗標來 [略過超出範圍的刪除。](skip-out-of-scope-deletions.md)

如果發生上述四個事件之一，而目標應用程式不支援虛刪除，則佈建服務會傳送 DELETE 要求，以從應用程式中永久刪除使用者。

如果您在屬性對應中看到 IsSoftDeleted 屬性，此屬性將用來決定使用者的狀態，以及是否要傳送 active = false 的更新要求以虛刪除使用者。

**已知的限制**

* 如果先前由布建服務管理的使用者未指派給應用程式或指派給應用程式的群組，則會傳送停用要求。 屆時，使用者不是由服務所管理，而且我們不會在從目錄中刪除時傳送刪除要求。
* 不支援布建在 Azure AD 中停用的使用者。 在布建之前，它們必須在 Azure AD 中處於作用中狀態。
* 當使用者從虛刪除變成作用中時，Azure AD 布建服務會在目標應用程式中啟動使用者，但不會自動還原群組成員資格。 目標應用程式應維持處於非使用中狀態之使用者的群組成員資格。 如果目標應用程式不支援此功能，您可以重新開機布建以更新群組成員資格。 

**建議**

開發應用程式時，一律支援虛刪除和實刪除。 它可讓客戶在意外停用使用者時復原。


## <a name="next-steps"></a>後續步驟

[規劃自動使用者佈建部署](../app-provisioning/plan-auto-user-provisioning.md)

[設定資源庫應用程式的佈建](./configure-automatic-user-provisioning-portal.md)

[在建立您自己的應用程式時建置 SCIM 端點並設定佈建](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[對設定使用者並將其佈建至應用程式時的問題進行疑難排解](./application-provisioning-config-problem.md)。
