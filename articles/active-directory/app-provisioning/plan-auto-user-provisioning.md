---
title: 規劃 Azure 活動目錄的自動使用者預配部署
description: 規劃和執行自動使用者預配指南
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d2f284fddfc49632e467adbf5877856b40a81dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522405"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>規劃自動使用者佈建部署

許多組織依賴軟體即服務 （SaaS） 應用程式（如 ServiceNow、Zscaler 和 Slack）來提高最終使用者的工作效率。 從歷史上看，IT 人員一直依賴手動預配方法，例如上載 CSV 檔或使用自訂腳本安全地管理每個 SaaS 應用程式中的使用者身份。 這些進程容易出錯、不安全且難以管理。

Azure 活動目錄 （Azure AD） 自動使用者預配通過基於商務規則安全地自動化 SaaS 應用程式中的使用者標識的創建、維護和刪除，簡化了此過程。 這種自動化使您能夠在擴展對基於雲的解決方案的依賴時，在僅雲和混合環境中有效地擴展身份管理系統。

請參閱[使用 Azure 活動目錄自動預配和取消預配到 SaaS 應用程式](../app-provisioning/user-provisioning.md)以更好地瞭解功能。

## <a name="learn"></a>學習

使用者預配為持續的身份治理奠定了基礎，並提高了依賴于權威標識資料的業務流程的品質。

### <a name="key-benefits"></a>主要權益

啟用自動使用者預配的主要優點是：

* **提高生產力**。 您可以使用單個使用者預配管理介面跨 SaaS 應用程式管理使用者身份。 此介面具有一組預配策略。

* **管理風險**。 您可以通過根據定義角色和/或存取權限的員工狀態或組成員身份自動進行更改來提高安全性。

* **解決合規性和治理**問題。 Azure AD 支援每個使用者預配請求的本機稽核記錄。 請求在源系統和目標系統中執行。 這使您能夠跟蹤誰可以從單個螢幕訪問應用程式。

* **降低成本**。 自動使用者預配通過避免與手動預配相關的效率低下和人為錯誤來降低成本。 它減少了對自訂開發的使用者預配解決方案、腳本和稽核記錄的需求。

### <a name="licensing"></a>授權

Azure AD 使用應用程式庫功能表中提供的範本提供任何應用程式的自助服務集成。 有關許可證要求的完整清單，請參閱[Azure AD 許可頁](https://azure.microsoft.com/pricing/details/active-directory/)。

#### <a name="application-licensing"></a>應用程式許可

您需要為要自動預配的應用程式提供適當的許可證。 與應用程式擁有者討論分配給應用程式的使用者是否為其應用程式角色具有適當的許可證。 如果 Azure AD 根據角色管理自動預配，則 Azure AD 中分配的角色必須與應用程式許可證保持一致。 應用程式中擁有的許可證不正確可能會導致使用者預配/更新期間出錯。

### <a name="terms"></a>詞彙

本文使用以下術語：

* CRUD 操作 - 對使用者帳戶執行的操作：創建、讀取、更新、刪除。

* 單一登入 （SSO） - 使用者登錄一次並訪問所有啟用 SSO 的應用程式的能力。 在使用者預配上下文中，SSO 是使用者擁有單個帳戶以訪問使用自動使用者預配的所有系統的結果。

* 源系統 - Azure AD 提供的使用者的存儲庫。 Azure AD 是大多數預集成預配連接器的源系統。 但是，雲應用程式（如 SAP、工作日和 AWS）也有一些例外。 例如，請參閱[從工作日到 AD 的使用者預配](../saas-apps/workday-inbound-tutorial.md)。

* 目標系統 - Azure AD 規定的使用者存儲庫。 目標系統通常是 SaaS 應用程式，如服務現在、Zscaler 和 Slack。 目標系統也可以是本地系統，如AD。

* [跨域身份管理系統 （SCIM）](https://aka.ms/scimoverview) - 允許使用者預配自動化的開放標準。 SCIM 在標識提供程式（如 Microsoft）和 Salesforce 或其他需要使用者標識資訊的 SaaS 應用之間通信使用者標識資料。

### <a name="training-resources"></a>培訓資源

| 資源| 連結和描述 |
| - | - |
| 隨選網路研討會| [使用 Azure AD 管理企業應用程式](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>瞭解 Azure AD 如何説明您實現企業 SaaS 應用程式的 SSO 以及控制訪問的最佳做法。 |
| 影片| [活動 Azure 目錄中的使用者預配是什麼？](https://youtu.be/_ZjARPpI6NI) <br> [如何在活動 Azure 目錄中部署使用者預配？](https://youtu.be/pKzyts6kfrw) <br> [將銷售人員與 Azure AD 集成：如何自動調配使用者](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| 線上課程| 線上技能：[管理身份](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> 瞭解如何將 Azure AD 與許多 SaaS 應用程式集成，以及如何保護使用者對這些應用程式的訪問。 |
| 書籍| [Web 應用程式 Azure 活動目錄（開發人員參考）第一版的現代身份驗證](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0)。  <br> 這是為這些新環境構建 Active Directory 身份驗證解決方案的權威深度指南。 |
| 教學課程| 請參閱[有關如何將 SaaS 應用與 Azure AD 集成的教程清單](../saas-apps/tutorial-list.md)。 |
| 常見問題集| 有關自動使用者預配的[常見問題](../app-provisioning/user-provisioning.md) |

### <a name="solution-architectures"></a>解決方案架構

Azure AD 預配服務通過連接到每個應用程式供應商提供的使用者管理 API 終結點，將使用者預配到 SaaS 應用和其他系統。 這些使用者管理 API 端點可以讓 Azure AD 以程式設計方式建立、更新和移除使用者。

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>混合動力企業的自動使用者配置

在此示例中，使用者和/或組是在連接到本地目錄的 HR 資料庫中創建的。 Azure AD 預配服務管理對目標 SaaS 應用程式的自動使用者預配。

 ![使用者預配](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**工作流描述：**

1. 使用者/組是在本地 HR 應用程式/系統（如 SAP）中創建的。 

1. **Azure AD Connect 代理**運行從本地 AD 到 Azure AD 的標識（使用者和組）的計畫同步。

1. **Azure AD 預配服務**針對源系統和目標系統開始[初始週期](../app-provisioning/user-provisioning.md)。 

1. **Azure AD 預配服務**查詢自初始週期以來更改的任何使用者和組的源系統，並在[增量週期](../app-provisioning/user-provisioning.md)中推送更改。

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>僅限雲的企業自動使用者預配

在此示例中，使用者創建發生在 Azure AD 中，Azure AD 預配服務管理對目標 （SaaS） 應用程式的自動使用者預配。

![圖 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**工作流描述：**

1. 使用者/組在 Azure AD 中創建。

1. **Azure AD 預配服務**針對源系統和目標系統開始[初始週期](../app-provisioning/user-provisioning.md)。 

1. **Azure AD 預配服務**查詢自初始週期以來更新的任何使用者和組的源系統，並執行任何[增量週期](../app-provisioning/user-provisioning.md)。

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>雲 HR 應用程式的自動使用者配置 

在此示例中，使用者和/或組是在雲 HR 應用程式中（如工作日和成功因素）中創建的。 Azure AD 預配服務和 Azure AD 連接預配代理將雲 HR 應用租戶的使用者資料預配到 AD 中。 在 AD 中更新帳戶後，它將通過 Azure AD 連接與 Azure AD 同步，電子郵件地址和使用者名屬性可以寫回雲 HR 應用租戶。

![圖 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **HR 團隊**在雲 HR 應用租戶中執行事務。
2.  **Azure AD 預配服務**從雲 HR 應用租戶運行計畫週期，並標識需要處理的更改才能與 AD 同步。
3.  **Azure AD 預配服務**調用 Azure AD 連接預配代理，其請求有效負載包含 AD 帳戶創建/更新/啟用/禁用操作。
4.  **Azure AD 連接預配代理**使用服務帳戶來管理 AD 帳戶資料。
5.  **Azure AD 連接**運行增量同步以在 AD 中提取更新。
6.  **AD**更新與 Azure AD 同步。 
7.  **Azure AD 預配服務**將電子郵件屬性和使用者名從 Azure AD 寫入雲 HR 應用租戶。

## <a name="plan-the-deployment-project"></a>規劃部署專案

考慮組織需要確定在環境中部署使用者預配的策略。

### <a name="engage-the-right-stakeholders"></a>讓合適的利益相關者參與進來

當技術專案失敗時，通常是由於對影響、結果和責任的期望不匹配。 為了避免這些陷阱，[確保您與合適的利益相關者接洽](https://aka.ms/deploymentplans)，並通過記錄利益相關者及其專案投入和問責制，充分理解專案中的利益相關者角色。

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功都至關重要。 主動與使用者溝通他們的體驗將如何改變、何時更改，以及如果他們遇到問題如何獲得支援。

### <a name="plan-a-pilot"></a>規劃試點

我們建議自動使用者預配的初始配置在具有少量使用者的測試環境中，然後再將其擴展到生產中的所有使用者。 請參閱運行試驗的[最佳做法](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)。

#### <a name="best-practices-for-a-pilot"></a>飛行員的最佳做法  

通過試驗，您可以在為每個人部署功能之前使用一小群進行測試。 確保作為測試的一部分，對組織中的每個用例都進行了全面測試。

在第一波中，針對 IT、可用性和其他可以測試和提供回饋的適當使用者。 使用此回饋可進一步開發您發送給使用者的通信和說明，並深入瞭解支援人員可能看到的問題類型。

通過擴大目標群組的範圍，將推出範圍擴大到更大的使用者組。 這可以通過[動態組成員身份](../users-groups-roles/groups-dynamic-membership.md)或手動將使用者添加到目標群組來實現。

## <a name="plan-application-connections-and-administration"></a>規劃應用程式連接和管理

使用 Azure AD 門戶查看和管理支援預配的所有應用程式。 請參閱[在門戶中查找應用](../app-provisioning/configure-automatic-user-provisioning-portal.md)。

### <a name="determine-the-type-of-connector-to-use"></a>確定要使用的連接器類型

啟用及設定自動佈建所需的實際步驟會因應用程式而有所不同。 如果要自動預配的應用程式在[Azure AD SaaS 應用庫中](../saas-apps/tutorial-list.md)列出，則應選擇[特定于應用的集成教程](../saas-apps/tutorial-list.md)來配置其預集成的使用者預配連接器。

如果沒有，請按照以下步驟操作：

1. [創建](../develop/howto-app-gallery-listing.md)預集成使用者預配連接器的請求。 如果應用程式支援 SCIM，我們的團隊將與您和應用程式開發人員合作，將您的應用程式連接到我們的平臺。

1. 使用[BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)通用使用者預配支援。 這是 Azure AD 在沒有預集成預配連接器的情況下向應用預配使用者的要求。

1. 如果應用程式能夠利用 BYOA SCIM 連接器，請參閱[BYOA SCIM 集成教程](../app-provisioning/use-scim-to-provision-users-and-groups.md)，為應用程式佈建 BYOA SCIM 連接器。

有關詳細資訊，請參閱[哪些應用程式和系統可以與 Azure AD 自動使用者預配一起使用？](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>收集資訊以授權應用程式訪問

設置自動使用者預配是每個應用程式的過程。 對於每個應用程式，您需要提供[管理員憑據](../app-provisioning/configure-automatic-user-provisioning-portal.md)才能連接到目標系統的使用者管理終結點。

下圖顯示了所需管理員憑據的一個版本：

![預配螢幕以管理使用者帳戶預配設置](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

雖然某些應用程式需要管理員使用者名和密碼，但其他應用程式可能需要無記名權杖。

## <a name="plan-user-and-group-provisioning"></a>計畫使用者和組預配

如果為企業應用啟用使用者預配[，Azure 門戶](https://portal.azure.com/)將通過屬性對應控制其屬性值。

### <a name="determine-operations-for-each-saas-app"></a>確定每個 SaaS 應用的操作

每個應用程式可能具有唯一的使用者或組屬性，這些屬性必須映射到 Azure AD 中的屬性。 應用程式可能只有 CRUD 操作的子集可用。

對於每個應用程式，記錄以下資訊：

* 要對目標系統的使用者和/或組物件執行 CRUD 預配操作。 例如，每個 SaaS 應用業務擁有者可能不希望所有可能的操作。

* 源系統中可用的屬性

* 目標系統中可用的屬性

* 系統之間的屬性對應。

### <a name="choose-which-users-and-groups-to-provision"></a>選擇要預配的使用者和組

在實現自動使用者預配之前，必須確定要預配到應用程式的使用者和組。

* 使用[範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)定義基於屬性的規則，這些規則確定哪些使用者預配到應用程式。

* 接下來，根據需要使用[使用者和組分配](../manage-apps/assign-user-or-group-access-portal.md)進行其他篩選。

### <a name="define-user-and-group-attribute-mapping"></a>定義使用者和組屬性對應

要實現自動使用者預配，您需要定義應用程式所需的使用者和組屬性。 Azure AD 使用者物件和每個 SaaS 應用程式的使用者物件之間有一組預先配置的屬性和[屬性對應](../app-provisioning/configure-automatic-user-provisioning-portal.md)。 並非所有 SaaS 應用都啟用組屬性。

Azure AD 支援直接的屬性到屬性對應、提供常量值或[編寫屬性對應運算式](../app-provisioning/functions-for-customizing-application-data.md)。 這種靈活性使您能夠精細地控制目標系統屬性中將填充的內容。 您可以使用[Microsoft 圖形 API](../app-provisioning/export-import-provisioning-configuration.md)和圖形資源管理器將使用者預配屬性對應和架構匯出到 JSON 檔並將其導入 Azure AD。

有關詳細資訊，請參閱在[Azure 活動目錄中為 SaaS 應用程式自訂使用者預配屬性對應](../app-provisioning/customize-application-attributes.md)。

### <a name="special-considerations-for-user-provisioning"></a>使用者預配的特殊注意事項

請考慮以下事項，以減少部署後的問題：

* 確保用於在源應用程式和目標應用程式之間映射使用者/組物件的屬性具有彈性。 如果屬性發生更改（例如，使用者移動到公司的不同部分），它們不應導致使用者/組預配不正確。

* 應用程式可能具有特定的限制和/或要求，需要滿足使用者預配才能正常工作。 例如，Slack 會截截某些屬性的值。 請參閱特定于每個應用程式的[自動使用者預配教程](../saas-apps/tutorial-list.md)。

* 確認源系統和目標系統之間的架構一致性。 常見問題包括諸如 UPN 或郵件不匹配等屬性。 例如，Azure AD 中的 UPN*john_smith@contoso.com*設置為 app 中，它是*jsmith@contoso.com*。 有關詳細資訊，請參閱[使用者和組架構引用](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

## <a name="plan-testing-and-security"></a>計畫測試和安全

在部署的每個階段，確保測試結果與預期結果一樣，並審核預配週期。

### <a name="plan-testing"></a>計畫測試

為應用程式佈建自動使用者預配後，將運行測試案例以驗證此解決方案是否滿足組織的要求。

| 案例| 預期的結果 |
| - | - |
| 使用者將添加到分配給目標系統的組 | 使用者物件在目標系統中預配。 <br>使用者可以登錄到目標系統並執行所需的操作。 |
| 使用者從分配給目標系統的組中刪除 | 使用者物件在目標系統中取消預配。<br>使用者無法登錄到目標系統。 |
| 使用者資訊在 Azure AD 中由任何方法更新 | 更新的使用者屬性在增量迴圈後反映在目標系統中 |
| 使用者範圍外 | 使用者物件被禁用或刪除。 <br>注意： 此行為將被覆蓋為[工作日預配](skip-out-of-scope-deletions.md)。 |

### <a name="plan-security"></a>規劃安全性

在部署中通常需要進行安全審查。 如果需要安全審核，請參閱許多 Azure AD 白皮書，這些[白皮書](https://www.microsoft.com/download/details.aspx?id=36391)提供了作為服務的身份概述。

### <a name="plan-rollback"></a>計畫回滾

如果自動使用者預配實現在生產環境中無法正常工作，以下回滾步驟可以説明您恢復到以前的已知良好狀態：

1. 查看[預配摘要報告](../app-provisioning/check-status-user-account-provisioning.md)和[預配日誌](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)，以確定受影響的使用者和/或組發生了哪些不正確的操作。

1. 使用預配稽核記錄確定受影響的使用者和/或組的最後已知良好狀態。 還要查看源系統（Azure AD 或 AD）。

1. 與應用程式擁有者合作，使用最後一個已知良好的狀態值更新應用程式中直接受影響的使用者和/或組。

## <a name="deploy-automatic-user-provisioning-service"></a>部署自動使用者預配服務

選擇與您的解決方案要求一致的步驟。

### <a name="prepare-for-the-initial-cycle"></a>準備初始週期

當 Azure AD 預配服務首次運行時，針對源系統和目標系統的初始週期將為每個目標系統創建所有使用者物件的快照。

為應用程式啟用自動預配時，初始週期可能需要 20 分鐘到幾個小時。 持續時間取決於 Azure AD 目錄的大小和預配範圍內的使用者數。 請參閱[如何提高預配性能](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)。

預配服務在初始週期後存儲兩個系統的狀態，從而提高後續增量週期的性能。

### <a name="configure-automatic-user-provisioning"></a>設定使用者自動佈建

使用[Azure 門戶](https://portal.azure.com/)管理支援它的應用程式的自動使用者帳戶預配和取消預配。 按照[如何設置應用程式自動預配中的步驟操作？](../app-provisioning/user-provisioning.md)

您也可以使用 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)來設定和管理 Azure AD 使用者佈建服務。

## <a name="manage-automatic-user-provisioning"></a>管理自動使用者預配

部署後，需要管理解決方案。

### <a name="monitor-user-provisioning-operation-health"></a>監視使用者預配操作運行狀況

成功[的初始週期](../app-provisioning/user-provisioning.md)後，Azure AD 預配服務將無限期地以特定于每個應用程式的時間間隔運行增量更新，直到發生以下事件之一：

* 該服務將手動停止，並使用[Azure 門戶](https://portal.azure.com/)或使用相應的 Microsoft[圖形 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)命令觸發新的初始週期。

* 新的初始週期由屬性對應或範圍篩選器的更改觸發。

* 預配過程由於錯誤率很高而進入隔離狀態，並在隔離中停留超過四周，然後將自動禁用。

要查看這些事件以及預配服務執行的所有其他活動，請參閱 Azure AD[預配日誌](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。

要瞭解預配週期需要多長時間並監視預配作業的進度，可以[檢查使用者預配的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。

### <a name="gain-insights-from-reports"></a>從報告中獲取見解

Azure AD 可通過稽核記錄和報告提供有關組織使用者預配使用方式和操作運行狀況[的其他見解](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。

管理員應檢查預配摘要報告，以監視預配作業的操作運行狀況。 預配服務執行的所有活動都記錄在 Azure AD 稽核記錄中。 請參閱[教程：報告自動使用者帳戶預配](../app-provisioning/check-status-user-account-provisioning.md)。

我們建議您根據滿足組織要求的節奏承擔這些報表的擁有權並使用這些報告。 Azure AD 保留大多數審核資料 30 天。

### <a name="troubleshoot"></a>疑難排解

請參閱以下連結，以排除預配期間可能出現的任何問題：

* [設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題](../app-provisioning/application-provisioning-config-problem.md)

* [將屬性從本地活動目錄同步到 Azure AD 以預配到應用程式](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [將使用者佈建至 Azure AD 資源庫應用程式花費數小時以上](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

* [在設定 Azure Active Directory 資源庫應用程式之使用者佈建的情況下儲存系統管理員認證時發生問題](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [未將任何使用者佈建至 Azure AD 資源庫應用程式](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [一組錯誤的使用者佈建至 Azure AD 資源庫應用程式](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>有用的文檔

* [為屬性對應編寫運算式](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD 同步處理 API 概觀](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [跳過刪除超出範圍的使用者帳戶](skip-out-of-scope-deletions.md)

* [Azure AD 連接預配代理：版本發佈歷史記錄](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>資源

* [提供產品回饋](https://feedback.azure.com/forums/169401-azure-active-directory)

* [瞭解 Azure AD 的新增功能](https://azure.microsoft.com/updates/?product=active-directory)

* [堆疊溢位 Azure AD 論壇](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>後續步驟
* [配置自動使用者預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [使用 Microsoft 圖形 API 匯出或導入預配配置](../app-provisioning/export-import-provisioning-configuration.md)

* [為 Azure 活動目錄中的屬性對應編寫運算式](../app-provisioning/functions-for-customizing-application-data.md)
