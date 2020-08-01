---
title: 規劃 Azure Active Directory 的自動使用者布建部署
description: 規劃和執行自動使用者布建的指引
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: cecea24fe002ee64d54052635a6d7dec982aeee2
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445656"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>規劃自動使用者佈建部署

許多組織都依賴軟體即服務（SaaS）應用程式，例如 ServiceNow、Zscaler 和可供使用者生產力的時差。 在過去，IT 人員依賴手動布建方法（例如上傳 CSV 檔案），或使用自訂腳本來安全地管理每個 SaaS 應用程式中的使用者身分識別。 這些進程容易出錯、不安全，而且難以管理。

Azure Active Directory （Azure AD）自動使用者布建會藉由在 SaaS 應用程式中，根據商務規則安全地自動建立、維護和移除使用者身分識別，來簡化此流程。 這項自動化可讓您在擴充其雲端式解決方案的相依性時，有效地在僅限雲端和混合式環境上調整身分識別管理系統。

請參閱[使用 Azure Active Directory 來自動化 SaaS 應用程式的使用者](../app-provisioning/user-provisioning.md)布建和解除布建，以進一步瞭解功能。

## <a name="learn"></a>學習

使用者布建會建立持續身分識別管理的基礎，並增強依賴授權身分識別資料的商務程式品質。

### <a name="key-benefits"></a>主要權益

啟用自動使用者布建的主要優點如下：

* **提高生產力**。 您可以使用單一使用者布建管理介面，跨 SaaS 應用程式管理使用者身分識別。 此介面具有一組布建原則。

* **管理風險**。 您可以根據定義角色和（或）存取權的員工狀態或群組成員資格來自動化變更，以提高安全性。

* **解決合規性與治理**。 Azure AD 支援每個使用者布建要求的原生 audit 記錄。 要求會同時在來源和目標系統中執行。 這可讓您從單一畫面追蹤可存取應用程式的人員。

* **降低成本**。 自動使用者布建藉由避免與手動布建相關聯的效率和人為錯誤，來降低成本。 它可減少自訂開發的使用者布建解決方案、腳本和 audit 記錄的需求。

### <a name="licensing"></a>授權

Azure AD 使用 [應用程式庫] 功能表中提供的範本，提供任何應用程式的自助整合。 如需授權需求的完整清單，請參閱[Azure AD 授權頁面](https://azure.microsoft.com/pricing/details/active-directory/)。

#### <a name="application-licensing"></a>應用程式授權

您需要您想要自動布建之應用程式的適當授權。 與應用程式擁有者討論，無論指派給應用程式的使用者是否具有其應用程式角色的適當授權。 如果 Azure AD 管理以角色為基礎的自動布建，Azure AD 中指派的角色必須與應用程式授權一致。 應用程式所擁有的不正確授權，可能會在布建/更新使用者期間導致錯誤。

### <a name="terms"></a>詞彙

本文使用下列詞彙：

* CRUD 作業-對使用者帳戶採取的動作：建立、讀取、更新、刪除。

* 單一登入（SSO）-使用者能夠登入一次，並存取所有啟用 SSO 的應用程式。 在使用者布建的內容中，SSO 是使用者具有單一帳戶來存取所有使用自動使用者布建之系統的結果。

* 來源系統-Azure AD 從中布建之使用者的儲存機制。 Azure AD 是適用于大部分預先整合布建連接器的來源系統。 不過，某些雲端應用程式（例如 SAP、Workday 和 AWS）有一些例外狀況。 例如，請參閱[從 Workday 至 AD 的使用者](../saas-apps/workday-inbound-tutorial.md)布建。

* 目標系統-Azure AD 布建到的使用者存放庫。 目標系統通常是一個 SaaS 應用程式，例如 ServiceNow、Zscaler 和時差。 目標系統也可以是內部部署系統（例如 AD）。

* [跨網域身分識別管理（SCIM）的系統](https://aka.ms/scimoverview)-可讓使用者布建自動化的開放標準。 SCIM 會在身分識別提供者（如 Microsoft）和服務提供者（例如 Salesforce 或其他需要使用者身分識別資訊的 SaaS 應用程式）之間通訊使用者識別資料。

### <a name="training-resources"></a>訓練資源

| 資源| 連結與描述 |
| - | - |
| 隨選網路研討會| [使用 Azure AD 管理您的企業應用程式](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>瞭解 Azure AD 如何協助您達成企業 SaaS 應用程式的 SSO，以及控制存取的最佳做法。 |
| 影片| [Azure Active Directory 中的使用者布建是什麼？](https://youtu.be/_ZjARPpI6NI) <br> [如何在 Active Directory 中部署使用者布建？](https://youtu.be/pKzyts6kfrw) <br> [整合 Salesforce 與 Azure AD：如何自動化使用者布建](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| 線上課程| 線上 SkillUp：[管理](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about)身分識別 <br> 瞭解如何整合 Azure AD 與許多 SaaS 應用程式，以及保護使用者存取這些應用程式的安全。 |
| 書籍| [使用 Web 應用程式 Azure Active Directory 的新式驗證（開發人員參考）第1版](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0)。  <br> 這是建立這些新環境 Active Directory 驗證解決方案的權威深入指南。 |
| 教學課程| 請參閱[教學課程清單，以瞭解如何整合 SaaS 應用程式與 Azure AD](../saas-apps/tutorial-list.md)。 |
| 常見問題集| 自動化使用者布[建的常見問題](../app-provisioning/user-provisioning.md) |

### <a name="solution-architectures"></a>解決方案架構

Azure AD 布建服務會藉由連接到每個應用程式廠商所提供的使用者管理 API 端點，將使用者布建到 SaaS 應用程式和其他系統。 這些使用者管理 API 端點可以讓 Azure AD 以程式設計方式建立、更新和移除使用者。

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>混合式企業的自動使用者布建

在此範例中，會在連接到內部部署目錄的 HR 資料庫中建立使用者和或群組。 Azure AD 布建服務會管理對目標 SaaS 應用程式的自動使用者布建。

 ![使用者布建](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**工作流程的描述：**

1. 使用者/群組會建立于內部部署 HR 應用程式/系統中，例如 SAP。 

1. **Azure AD Connect 代理程式**會執行從本機 AD 到 Azure AD 的排程同步處理身分識別（使用者和群組）。

1. **Azure AD**布建服務會開始對來源系統和目標系統進行[初始迴圈](../app-provisioning/user-provisioning.md)。 

1. **Azure AD**布建服務會向來源系統查詢自初始週期後變更的任何使用者和群組，並在[增量迴圈](../app-provisioning/user-provisioning.md)中推送變更。

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>為僅限雲端的企業自動布建使用者

在此範例中，使用者建立會在 Azure AD 中進行，而 Azure AD 布建服務會管理對目標（SaaS）應用程式的自動使用者布建。

![圖 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**工作流程的描述：**

1. 使用者/群組會建立在 Azure AD 中。

1. **Azure AD**布建服務會開始對來源系統和目標系統進行[初始迴圈](../app-provisioning/user-provisioning.md)。 

1. **Azure AD**布建服務會在來源系統中查詢初始迴圈後所更新的任何使用者和群組，並執行任何[增量迴圈](../app-provisioning/user-provisioning.md)。

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>雲端 HR 應用程式的自動使用者布建 

在此範例中，使用者和或群組是在雲端 HR 應用程式（例如 Workday 和 SuccessFactors）中建立的。 Azure AD 的布建服務和 Azure AD Connect 布建代理程式會將使用者資料從雲端 HR 應用程式租使用者布建到 AD。 當帳戶在 AD 中更新之後，它會透過 Azure AD Connect 與 Azure AD 同步處理，而電子郵件地址和使用者名稱屬性則可回寫到雲端 HR 應用程式租使用者。

![圖 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **Hr 小組**會在雲端 hr 應用程式租使用者中執行交易。
2.  **Azure AD**布建服務會從雲端 HR 應用程式租使用者執行排程週期，並識別需要處理以與 AD 同步的變更。
3.  **Azure AD**布建服務會使用包含 AD 帳戶建立/更新/啟用/停用作業的要求承載，叫用 Azure AD Connect 布建代理程式。
4.  **Azure AD Connect 布建代理程式**會使用服務帳戶來管理 AD 帳戶資料。
5.  **Azure AD Connect**在 AD 中執行差異同步處理以提取更新。
6.  **AD**更新會與 Azure AD 同步處理。 
7.  **Azure AD**布建服務回寫電子郵件屬性，以及從 Azure AD 到雲端 HR 應用程式租使用者的使用者名稱。

## <a name="plan-the-deployment-project"></a>規劃部署專案

請考慮您的組織需要判斷在您的環境中部署使用者布建的策略。

### <a name="engage-the-right-stakeholders"></a>包含正確的專案關係人

當技術專案失敗時，通常是因為對影響、結果和責任的預期不相符。 若要避免這些錯誤，請[確定您正在吸引正確的專案關係人](https://aka.ms/deploymentplans)，而且可以記錄專案關係人和其專案輸入和標準責任，以充分瞭解專案中的專案關係人角色。

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功非常重要。 主動與您的使用者溝通其體驗將如何改變、何時會改變，以及如何在遇到問題時取得支援。

### <a name="plan-a-pilot"></a>規劃試驗

我們建議您在測試環境中使用自動使用者布建的初始設定，然後再將其調整為生產中的所有使用者。 請參閱執行試驗的[最佳做法](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)。

#### <a name="best-practices-for-a-pilot"></a>試驗的最佳做法  

試驗可讓您使用小型群組進行測試，然後再為所有人部署功能。 請確定在測試過程中，您組織內的每個使用案例都經過徹底測試。

在您的第一波，以 IT、可用性及其他可測試並提供意見反應的適當使用者為目標。 請使用此意見反應進一步開發您傳送給使用者的通訊和指示，並深入瞭解您的支援人員可能會看到的問題類型。

藉由增加目標群組的範圍，將首度發行擴大至較大的使用者群組。 這可以透過[動態群組成員資格](../users-groups-roles/groups-dynamic-membership.md)來完成，或手動將使用者新增至目標群組。

## <a name="plan-application-connections-and-administration"></a>規劃應用程式連接和系統管理

使用 Azure AD 入口網站來查看和管理支援布建的所有應用程式。 請參閱[在入口網站中尋找您的應用程式](../app-provisioning/configure-automatic-user-provisioning-portal.md)。

### <a name="determine-the-type-of-connector-to-use"></a>決定要使用的連接器類型

啟用及設定自動佈建所需的實際步驟會因應用程式而有所不同。 如果您想要自動布建的應用程式列在[Azure AD SaaS 應用程式庫](../saas-apps/tutorial-list.md)中，則您應該選取[應用程式特定的整合教學](../saas-apps/tutorial-list.md)課程，以設定其預先整合的使用者布建連接器。

如果沒有，請遵循下列步驟：

1. [建立](../develop/howto-app-gallery-listing.md)預先整合的使用者布建連接器的要求。 我們的團隊會與您和應用程式開發人員合作，將您的應用程式上架到我們的平臺（如果支援 SCIM）。

1. 使用[BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)應用程式的一般使用者布建支援。 這是 Azure AD 在沒有預先整合的布建連接器的情況下，將使用者布建到應用程式的必要條件。

1. 如果應用程式能夠利用 BYOA SCIM 連接器，請參閱[BYOA SCIM 整合教學](../app-provisioning/use-scim-to-provision-users-and-groups.md)課程，以設定應用程式的 BYOA SCIM 連接器。

如需詳細資訊，請參閱[我可以將哪些應用程式和系統與 Azure AD 自動使用者](../app-provisioning/user-provisioning.md)布建搭配使用？

### <a name="collect-information-to-authorize-application-access"></a>收集資訊以授權應用程式存取

設定自動使用者布建是每個應用程式的進程。 針對每個應用程式，您必須提供[系統管理員認證](../app-provisioning/configure-automatic-user-provisioning-portal.md)，才能連接到目標系統的使用者管理端點。

下圖顯示一個所需的系統管理員認證版本：

![管理使用者帳戶佈建設定的佈建畫面](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

有些應用程式需要系統管理員使用者名稱和密碼，有些則可能需要持有人權杖。

## <a name="plan-user-and-group-provisioning"></a>規劃使用者和群組布建

如果您啟用企業應用程式的使用者布建， [Azure 入口網站](https://portal.azure.com/)會透過屬性對應來控制其屬性值。

### <a name="determine-operations-for-each-saas-app"></a>判斷每個 SaaS 應用程式的作業

每個應用程式可能都有唯一的使用者或群組屬性，必須對應到您 Azure AD 中的屬性。 應用程式可能只有部分 CRUD 作業可供使用。

針對每個應用程式，記錄下列資訊：

* 要對目標系統的使用者和或群組物件執行的 CRUD 布建作業。 例如，每個 SaaS 應用程式商務擁有者可能不會想要所有可能的作業。

* 來源系統中可用的屬性

* 目標系統中可用的屬性

* 系統之間的屬性對應。

### <a name="choose-which-users-and-groups-to-provision"></a>選擇要布建的使用者和群組

在執行自動使用者布建之前，您必須先決定要提供給應用程式的使用者和群組。

* 使用[範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)來定義以屬性為基礎的規則，以決定要將哪些使用者布建到應用程式。

* 接下來，視需要使用[使用者和群組指派](../manage-apps/assign-user-or-group-access-portal.md)來進行額外的篩選。

### <a name="define-user-and-group-attribute-mapping"></a>定義使用者和群組屬性對應

若要執行自動使用者布建，您必須定義應用程式所需的使用者和群組屬性。 Azure AD 使用者物件和每個 SaaS 應用程式的使用者物件之間，有一組預先設定的屬性和[屬性](../app-provisioning/configure-automatic-user-provisioning-portal.md)對應。 並非所有 SaaS 應用程式都會啟用群組屬性。

Azure AD 支援直接屬性與屬性的對應、提供常數值，或[撰寫屬性對應的運算式](../app-provisioning/functions-for-customizing-application-data.md)。 這項彈性可讓您更精確地控制將會在目標系統的屬性中填入哪些內容。 您可以使用[MICROSOFT GRAPH API](../app-provisioning/export-import-provisioning-configuration.md)和 Graph Explorer，將您的使用者布建屬性對應和架構匯出至 JSON 檔案，並將其匯入回 Azure AD。

如需詳細資訊，請參閱[在 Azure Active Directory 中自訂 SaaS 應用程式的使用者布建屬性對應](../app-provisioning/customize-application-attributes.md)。

### <a name="special-considerations-for-user-provisioning"></a>使用者布建的特殊考慮

請考慮下列事項以減少部署後的問題：

* 請確定用來對應來源和目標應用程式之間使用者/群組物件的屬性是可復原的。 如果屬性變更（例如，使用者移至公司的不同部分），則不應讓使用者/群組不正確地布建。

* 應用程式可能會有特定的限制和（或）需求，讓使用者布建正常運作。 例如，時差會截斷某些屬性的值。 請參閱每個應用程式特定的[自動使用者](../saas-apps/tutorial-list.md)布建教學課程。

* 確認來源與目標系統之間的架構一致性。 常見的問題包括 UPN 或郵件不相符等屬性。 例如，Azure AD 中的 UPN 在 *john_smith@contoso.com* 應用程式中設定為和，則為 *jsmith@contoso.com* 。 如需詳細資訊，請參閱[使用者和群組架構參考](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

## <a name="plan-testing-and-security"></a>規劃測試和安全性

在您部署的每個階段中，請確定您要測試的結果是否如預期般進行，以及審核布建週期。

### <a name="plan-testing"></a>規劃測試

設定應用程式的自動使用者布建之後，您將會執行測試案例，以確認此解決方案符合您組織的需求。

| 案例| 預期的結果 |
| - | - |
| 使用者已新增至指派給目標系統的群組 | 使用者物件是在目標系統中布建。 <br>使用者可以登入目標系統，並執行所需的動作。 |
| 已將使用者從指派給目標系統的群組移除 | 在目標系統中取消布建使用者物件。<br>使用者無法登入目標系統。 |
| 任何方法都會更新 Azure AD 中的使用者資訊 | 更新後的使用者屬性在目標系統中會反映在增量迴圈之後 |
| 使用者超出範圍 | 使用者物件已停用或刪除。 <br>注意：會覆寫此行為以進行[Workday](skip-out-of-scope-deletions.md)布建。 |

### <a name="plan-security"></a>規劃安全性

在部署過程中需要進行安全性審查，這是很常見的。 如果您需要安全性審查，請參閱許多 Azure AD[白皮書](https://www.microsoft.com/download/details.aspx?id=36391)，其中提供身分識別即服務的總覽。

### <a name="plan-rollback"></a>規劃復原

如果自動使用者布建的執行無法在生產環境中如預期般運作，下列的復原步驟可協助您還原至先前已知的良好狀態：

1. 請參閱布建[摘要報告](../app-provisioning/check-status-user-account-provisioning.md)和布建[記錄](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)，以判斷受影響的使用者和/或群組上發生了哪些不正確的作業。

1. 使用布建審核記錄來判斷受影響的使用者和/或群組的上次已知良好狀態。 另請參閱來源系統（Azure AD 或 AD）。

1. 與應用程式擁有者合作，使用最後一個已知的正確狀態值，更新直接在應用程式中影響的使用者和/或群組。

## <a name="deploy-automatic-user-provisioning-service"></a>部署自動使用者布建服務

選擇符合您解決方案需求的步驟。

### <a name="prepare-for-the-initial-cycle"></a>準備初始迴圈

當 Azure AD 布建服務第一次執行時，針對來源系統和目標系統的初始週期會針對每個目標系統建立所有使用者物件的快照集。

啟用應用程式的自動布建時，初始週期可能需要20分鐘到數小時的時間。 持續時間取決於 Azure AD 目錄大小和布建範圍中的使用者數目。

布建服務會在初始迴圈後儲存這兩個系統的狀態，以改善後續增量迴圈的效能。

### <a name="configure-automatic-user-provisioning"></a>設定使用者自動佈建

使用[Azure 入口網站](https://portal.azure.com/)來管理自動使用者帳戶的布建和解除布建，以用於支援它的應用程式。 依照[如何? 設定自動布建至應用程式](../app-provisioning/user-provisioning.md)中的步驟進行：

您也可以使用 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)來設定和管理 Azure AD 使用者佈建服務。

## <a name="manage-automatic-user-provisioning"></a>管理自動使用者布建

現在您已部署，您必須管理解決方案。

### <a name="monitor-user-provisioning-operation-health"></a>監視使用者布建操作健全狀況

在成功[初始迴圈](../app-provisioning/user-provisioning.md)之後，Azure AD 布建服務會無限期地以每個應用程式的特定間隔執行累加式更新，直到發生下列其中一個事件為止：

* 此服務會以手動方式停止，並使用[Azure 入口網站](https://portal.azure.com/)或使用適當的[Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)命令來觸發新的初始迴圈。

* 新的初始週期是由屬性對應或範圍篩選準則的變更所觸發。

* 布建程式會因為高錯誤率而進入隔離狀態，並在被自動停用後的四周內持續隔離。

若要檢查這些事件，以及布建服務所執行的所有其他活動，請參閱 Azure AD 布建[記錄](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。

若要瞭解布建週期所需的時間和監視布建作業的進度，您可以[檢查使用者](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)布建的狀態。

### <a name="gain-insights-from-reports"></a>從報表取得見解

Azure AD 可以透過 audit 記錄和報告，為您組織的使用者布建使用量和操作健全狀況提供[額外的深入](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)解析。

系統管理員應該檢查布建摘要報告，以監視布建作業的操作健全狀況。 布建服務所執行的所有活動都會記錄在 Azure AD audit 記錄檔中。 請參閱[教學課程：關於自動使用者帳戶布建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

我們建議您以符合組織需求的步調來取得的擁有權，並使用這些報表。 Azure AD 會保留最多30天的審核資料。

### <a name="troubleshoot"></a>疑難排解

請參閱下列連結，以針對可能在布建期間開啟的任何問題進行疑難排解：

* [設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題](../app-provisioning/application-provisioning-config-problem.md)

* [將內部部署 Active Directory 的屬性同步處理至 Azure AD 以布建至應用程式](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [在設定 Azure Active Directory 資源庫應用程式之使用者佈建的情況下儲存系統管理員認證時發生問題](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [未將任何使用者佈建至 Azure AD 資源庫應用程式](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [一組錯誤的使用者佈建至 Azure AD 資源庫應用程式](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>實用的文件

* [撰寫屬性對應的運算式](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD 同步處理 API 概觀](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [略過刪除超出範圍的使用者帳戶](skip-out-of-scope-deletions.md)

* [Azure AD Connect 布建代理程式：版本發行歷程記錄](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>資源

* [提供產品意見反應](https://feedback.azure.com/forums/169401-azure-active-directory)

* [隨時掌握最新的 Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [堆疊溢位 Azure AD 論壇](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>後續步驟
* [設定自動使用者布建](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [使用 Microsoft Graph API 匯出或匯入您的布建設定](../app-provisioning/export-import-provisioning-configuration.md)

* [在 Azure Active directory 中撰寫屬性對應的運算式](../app-provisioning/functions-for-customizing-application-data.md)
