---
title: 在 Azure Active Directory 入口網站中布建記錄 (預覽) |Microsoft Docs
description: 在 Azure Active Directory 入口網站中布建記錄報表的簡介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/07/2020
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 675c98e00b7458f326c95741529f7ce41a91dc18
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319734"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>在 Azure Active Directory 入口網站中布建報表 (預覽) 

Azure Active Directory (Azure AD) 中的報告架構包含下列元件：

- **活動** 
    - **登入** – 受控應用程式和使用者登入活動的使用情況相關資訊。
    - **審核記錄**  - [Audit 記錄](concept-audit-logs.md)檔提供有關使用者和群組管理、受控應用程式和目錄活動的系統活動資訊。
    - 布建**記錄**：提供 Azure AD 布建服務所布建之使用者、群組和角色的相關系統活動。 

- **安全性** 
    - 有**風險**的登入：有[風險](../identity-protection/overview-identity-protection.md)的登入是指登入嘗試的指標，該嘗試可能是使用者帳戶的合法擁有者所執行。
    - **標示為有風險的使用者** -有 [風險的使用者](../identity-protection/overview-identity-protection.md) 是指可能遭到盜用的使用者帳戶指標。

本主題提供布建報告的總覽。

## <a name="prerequisites"></a>先決條件

### <a name="who-can-access-the-data"></a>誰可以存取資料？
* 應用程式擁有者可以查看他們所擁有之應用程式的記錄
* 安全性系統管理員、安全性讀取者、報告讀取者、應用程式系統管理員和雲端應用程式系統管理員角色中的使用者
* 全域系統管理員


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>您需要哪些 Azure AD 授權才能存取布建活動？

您的租使用者必須有與其相關聯的 Azure AD Premium 授權，才能看到 [所有布建活動] 報告。 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。 

## <a name="provisioning-logs"></a>佈建記錄

布建記錄提供下列問題的答案：

* ServiceNow 中成功建立了哪些群組？
* 從 Amazon Web Services 匯入了哪些角色？
* 哪些使用者在 DropBox 中建立失敗？

您可以在[Azure 入口網站](https://portal.azure.com)的**Azure Active Directory**分頁的 [**監視**] 區段中選取 [布建**記錄**]，以存取布建記錄。 某些布建記錄最多可能需要兩個小時才會顯示在入口網站中。

![佈建記錄](./media/concept-provisioning-logs/access-provisioning-logs.png "佈建記錄")


布建記錄有一個預設的清單視圖，顯示：

- 身分識別
- 動作
- 來源系統
- 目標系統
- 狀態
- 日期


![預設資料行](./media/concept-provisioning-logs/default-columns.png "預設資料行")

您可以按一下工具列中的 [資料 **行** ]，以自訂清單查看。

![資料行選擇器](./media/concept-provisioning-logs/column-chooser.png "資料行選擇器")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![可用的資料行](./media/concept-provisioning-logs/available-columns.png "可用的資料行")

選取清單檢視中的項目，即可取得更詳細的資訊。

![詳細資訊](./media/concept-provisioning-logs/steps.png "Filter")


## <a name="filter-provisioning-activities"></a>篩選布建活動

您可以篩選布建資料。 某些篩選值會根據您的租使用者動態填入。 例如，如果您的租使用者中沒有任何建立事件，就不會有 create 的篩選選項。
在預設視圖中，您可以選取下列篩選準則：

- 身分識別
- Date
- 狀態
- 動作


![新增篩選條件](./media/concept-provisioning-logs/default-filter.png "Filter")

身分 **識別** 篩選器可讓您指定您關心的名稱或身分識別。 此身分識別可以是使用者、群組、角色或其他物件。 您可以依物件的名稱或識別碼進行搜尋。 此識別碼會因案例而異。 例如，從 Azure AD 將物件布建到 SalesForce 時，來源識別碼是 Azure AD 中使用者的物件識別碼，而 TargetID 是 Salesforce 中使用者的識別碼。 從 Workday 布建至 Active Directory 時，來源識別碼是 Workday 背景工作員工識別碼。 請注意，使用者的名稱不一定會出現在識別欄位中。 一律會有一個識別碼。 


[日期]**** 篩選條件可讓您定義傳回資料的時間範圍。  
可能的值包括：

- 1 個月
- 7 天
- 30 天
- 24 小時
- 自訂時間間隔

當您選取自訂時間範圍時，可以設定開始日期和結束日期。


[狀態]**** 篩選條件可讓您選取︰

- 全部
- Success
- 失敗
- 已略過



**動作**篩選器可讓您篩選：

- 建立 
- 更新
- 刪除
- 停用
- 其他

此外，如果是預設視圖的篩選，您也可以設定下列篩選準則：

- 作業識別碼
- 週期識別碼
- 變更識別碼
- 來源識別碼
- 目標識別碼
- 應用程式


![挑選欄位](./media/concept-provisioning-logs/add-filter.png "挑選欄位")


- **作業識別碼** -唯一的工作識別碼會與您已啟用布建的每個應用程式相關聯。   

- **週期識別碼** -唯一識別布建週期。 您可以共用此識別碼以支援，以查詢發生此事件的週期。

- **變更** 布建事件的識別碼-唯一識別碼。 您可以共用此識別碼以支援查詢布建事件。   


- **來源系統** -可讓您指定要從何處布建身分識別。 例如，從 Azure AD 將物件布建至 ServiceNow 時，會 Azure AD 來源系統。 

- **目標系統** -可讓您指定要將身分識別布建至何處。 例如，從 Azure AD 將物件布建至 ServiceNow 時，目標系統為 ServiceNow。 

- **應用程式** -可讓您只顯示包含特定字串之顯示名稱的應用程式記錄。

 

## <a name="provisioning-details"></a>布建詳細資料 

當您在布建清單視圖中選取專案時，您會取得更多有關此專案的詳細資料。
詳細資料會根據下列類別進行分組：

- 步驟

- 疑難排解和建議

- 修改的屬性

- 摘要


![布建詳細資料](./media/concept-provisioning-logs/provisioning-tabs.png "索引標籤")



### <a name="steps"></a>步驟

[ **步驟** ] 索引標籤會列出布建物件所採取的步驟。 布建物件可能包含四個步驟： 

- 匯入物件
- 判斷物件是否在範圍內
- 符合來源與目標之間的物件
- 布建物件 (採取動作-這可能是建立、更新、刪除或停用) 



![螢幕擷取畫面顯示 [步驟] 索引標籤，其中顯示布建步驟。](./media/concept-provisioning-logs/steps.png "Filter")


### <a name="troubleshoot-and-recommendations"></a>疑難排解和建議


[ **疑難排解和建議** ] 索引標籤提供錯誤代碼和原因。 只有當失敗時，才會提供錯誤資訊。 


### <a name="modified-properties"></a>修改的屬性

**修改過的屬性**會顯示舊值和新值。 在沒有舊值的情況下，舊的值資料行是空白的。 


### <a name="summary"></a>摘要

[ **摘要** ] 索引標籤提供來源和目標系統中物件的發生狀況和識別碼的總覽。 

## <a name="what-you-should-know"></a>您應該知道的事項

- 如果您有 premium edition，Azure 入口網站會將回報的布建資料儲存30天，而如果您有免費版本，則會儲存7天。布建記錄可以發佈至 [log analytics](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-log-analytics) ，以保留超過30天的時間。 

- 您可以使用 [變更識別碼] 屬性做為唯一識別碼。 例如，當與產品支援互動時，這會很有説明。

- 目前沒有任何選項可將布建資料下載為 CSV 檔案，但您可以使用 [Microsoft Graph](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta)來匯出資料。

- 您可能會看到不在範圍中的使用者略過的事件。 這是預期的情況，特別是當同步處理範圍設定為 [所有使用者和群組] 時。 我們的服務會評估租使用者中的所有物件，甚至是超出範圍的物件。 

- 布建記錄目前無法在政府雲端中使用。 如果您無法存取布建記錄，請使用「審核記錄」作為暫時的因應措施。  

## <a name="error-codes"></a>錯誤碼

使用下表，進一步瞭解如何解決您在布建記錄檔中可能會發現的錯誤。 針對任何遺失的錯誤碼，請使用此頁面底部的連結來提供意見反應。 

|錯誤碼|描述|
|---|---|
|衝突，EntryConflict|更正 Azure AD 或應用程式中衝突的屬性值，或者如果衝突的使用者帳戶應該進行比對和接管，請檢查相符的屬性設定。 如需設定相符屬性的詳細資訊，請參閱下列 [檔](../app-provisioning/customize-application-attributes.md) 。|
|TooManyRequests|目標應用程式拒絕此嘗試更新使用者，因為它已多載且收到太多要求。 不需要執行任何動作。 這次嘗試將會自動淘汰。 Microsoft 也已收到此問題的通知。|
|InternalServerError |目標應用程式傳回未預期的錯誤。 目標應用程式可能發生服務問題，導致無法運作。 這項嘗試會在40分鐘內自動淘汰。|
|InsufficientRights、MethodNotAllowed、NotPermitted、未經授權| Azure AD 可以向目標應用程式進行驗證，但未獲授權執行更新。 請參閱目標應用程式所提供的任何指示，以及個別的應用程式 [教學](../saas-apps/tutorial-list.md)課程。|
|UnprocessableEntity|目標應用程式傳回未預期的回應。 目標應用程式的設定可能不正確，或目標應用程式可能發生服務問題，導致無法運作。|
|WebExceptionProtocolError |連接到目標應用程式時發生 HTTP 通訊協定錯誤。 不需要執行任何動作。 這項嘗試會在40分鐘內自動淘汰。|
|InvalidAnchor|先前由布建服務所建立或比對的使用者已不存在。 檢查以確定使用者存在。 若要強制重新符合所有的使用者，請使用 MS 圖形 API [重新開機作業](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)。 請注意，重新開機布建將會觸發初始迴圈，這可能需要一些時間才能完成。 它也會刪除布建服務用來操作的快取，這表示租使用者中的所有使用者和群組都必須重新評估，而且可能會卸載某些布建事件。|
|NotImplemented | 目標應用程式傳回未預期的回應。 應用程式的設定可能不正確，或目標應用程式可能發生服務問題，導致無法運作。 請參閱目標應用程式所提供的任何指示，以及個別的應用程式 [教學](../saas-apps/tutorial-list.md)課程。 |
|MandatoryFieldsMissing, MissingValues |因為遺漏必要的值，所以無法建立使用者。 更正來源記錄中遺漏的屬性值，或檢查相符的屬性設定，以確保不會省略必要的欄位。 [深入瞭解](../app-provisioning/customize-application-attributes.md) 如何設定相符的屬性。|
|SchemaAttributeNotFound |無法執行作業，因為指定了不存在於目標應用程式中的屬性。 請參閱有關屬性自訂的 [檔](../app-provisioning/customize-application-attributes.md) ，並確保您的設定正確無誤。|
|InternalError |Azure AD 布建服務內發生內部服務錯誤。 不需要執行任何動作。 這次嘗試將會在40分鐘內自動重試。|
|InvalidDomain |因為屬性值包含不正確功能變數名稱，所以無法執行作業。 更新使用者的功能變數名稱，或將其新增至目標應用程式中的允許清單。 |
|逾時 |無法完成作業，因為目標應用程式花費的時間太長而無法回應。 不需要執行任何動作。 這次嘗試將會在40分鐘內自動重試。|
|LicenseLimitExceeded|因為沒有此使用者的可用授權，所以無法在目標應用程式中建立使用者。 針對目標應用程式購買額外的授權，或查看您的使用者指派和屬性對應設定，以確保已使用正確的屬性來指派正確的使用者。|
|DuplicateTargetEntries  |無法完成作業，因為找不到目標應用程式中有一個以上的使用者具有設定的相符屬性。 請從目標應用程式中移除重複的使用者，或重新設定屬性對應，[如下所述。](../app-provisioning/customize-application-attributes.md)|
|DuplicateSourceEntries | 無法完成作業，因為發現多個使用者具有設定的相符屬性。 請移除重複的使用者，或重新設定您的屬性對應[，如下所述。](../app-provisioning/customize-application-attributes.md)|
|ImportSkipped | 評估每位使用者時，我們會嘗試從來源系統匯入使用者。 當匯入的使用者缺少屬性對應中定義的相符屬性時，通常會發生這個錯誤。 在使用者物件上沒有符合屬性的值時，我們無法評估範圍、比對或匯出變更。 請注意，此錯誤是否存在，不表示使用者在範圍內，但尚未評估使用者的範圍。|
|EntrySynchronizationSkipped | 布建服務已成功查詢來源系統並識別使用者。 使用者沒有採取任何進一步的動作，而且已略過。 略過的原因可能是使用者超出範圍，或使用者已存在於目標系統中，因此不需要進一步變更。|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| 當執行 GET 要求以取得使用者或群組時，我們在回應中收到多個使用者或群組。 我們預期只會在回應中收到一個使用者或群組。 [例如](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)，如果我們進行 GET 要求以抓取群組並提供篩選器來排除成員，而您的 SCIM 端點會傳回成員，我們會擲回此錯誤。|

## <a name="next-steps"></a>後續步驟

* [檢查使用者布建的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題](../app-provisioning/application-provisioning-config-problem.md)
* [布建記錄圖形 API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)