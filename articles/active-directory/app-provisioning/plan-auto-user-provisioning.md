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
ms.date: 12/31/2020
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 689276d44a175f3c314b0fcf681fd6a995b8dde2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832619"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>規劃自動使用者佈建部署

許多組織都依賴軟體即服務 (SaaS) 應用程式，例如 ServiceNow、Zscaler 和因應使用者生產力的時差。 在過去，IT 人員必須依賴手動布建方法，例如上傳 CSV 檔案，或使用自訂腳本安全地管理每個 SaaS 應用程式中的使用者身分識別。 這些程式很容易出錯、不安全且難以管理。

Azure Active Directory (Azure AD) 自動使用者布建可簡化此程式，方法是在 SaaS 應用程式中，根據商務規則安全地自動建立、維護和移除使用者身分識別。 這項自動化可讓您在雲端式和混合式環境中，有效地調整您的身分識別管理系統，因為它們會在雲端解決方案上擴充其相依性。

請參閱 [使用 Azure Active Directory 自動化 SaaS 應用程式的使用者](../app-provisioning/user-provisioning.md) 布建和解除布建，以進一步瞭解其功能。

## <a name="learn"></a>學習

使用者布建會建立進行中身分識別治理的基礎，並增強依賴授權身分識別資料的商務程式品質。

### <a name="key-benefits"></a>主要權益

啟用自動使用者布建的主要優點包括：

* **提高生產力**。 您可以使用單一使用者布建管理介面，跨 SaaS 應用程式管理使用者身分識別。 此介面有一組布建原則。

* **管理風險**。 您可以根據定義角色及/或存取權的員工狀態或群組成員資格，將變更自動化，以提高安全性。

* **解決合規性和治理**。 Azure AD 支援每個使用者布建要求的原生審核記錄。 要求會在來源和目標系統中執行。 這可讓您從單一畫面追蹤誰可以存取應用程式。

* **降低成本**。 自動使用者布建可避免與手動布建相關的低效率和人為錯誤，以降低成本。 它可減少自訂開發的使用者布建解決方案、腳本和審核記錄的需求。

### <a name="licensing"></a>授權

Azure AD 使用應用程式庫功能表中提供的範本，提供任何應用程式的自助整合。 如需授權需求的完整清單，請參閱 [Azure AD 授權頁面](https://azure.microsoft.com/pricing/details/active-directory/)。

#### <a name="application-licensing"></a>應用程式授權

您需要有適當的授權，才能讓應用程式 () 您想要自動布建。 與應用程式擁有者討論是否已指派應用程式的使用者具有適當的應用程式角色授權。 如果 Azure AD 管理以角色為基礎的自動布建，Azure AD 中指派的角色必須與應用程式授權相符。 在應用程式中所擁有的授權不正確，可能會在布建/更新使用者時發生錯誤。

### <a name="terms"></a>詞彙

本文使用下列詞彙：

* CRUD 作業-對使用者帳戶採取的動作：建立、讀取、更新、刪除。

* 單一登入 (SSO) -使用者只需登入一次，就能存取已啟用 SSO 的所有應用程式。 在使用者布建的內容中，SSO 是使用者擁有單一帳戶以存取所有使用自動使用者布建之系統的結果。

* 來源系統-Azure AD 從中布建的使用者存放庫。 Azure AD 是大部分預先整合的布建連接器的來源系統。 不過，雲端應用程式（例如 SAP、Workday 和 AWS）有一些例外狀況。 例如，請參閱 [從 Workday 到 AD 的使用者](../saas-apps/workday-inbound-tutorial.md)布建。

* 目標系統-Azure AD 布建的使用者存放庫。 目標系統通常是 SaaS 應用程式，例如 ServiceNow、Zscaler 和時差。 目標系統也可以是內部部署系統（例如 AD）。

* [跨網域身分識別管理的系統 (SCIM) ](https://aka.ms/scimoverview) -可讓使用者布建自動化的開放標準。 SCIM 會在身分識別提供者（例如 Microsoft）和服務提供者（例如 Salesforce 或其他需要使用者身分識別資訊的 SaaS 應用程式）之間，傳達使用者身分識別資料。

### <a name="training-resources"></a>訓練資源

| 資源| 連結與描述 |
| - | - |
| 隨選網路研討會| [使用 Azure AD 管理您的企業應用程式](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>瞭解 Azure AD 如何協助您達成企業 SaaS 應用程式的 SSO，以及控制存取的最佳做法。 |
| 影片| [什麼是 Active Azure 目錄中的使用者布建？](https://youtu.be/_ZjARPpI6NI) <br> [如何在 Active Azure 目錄中部署使用者布建？](https://youtu.be/pKzyts6kfrw) <br> [將 Salesforce 與 Azure AD 整合：如何自動化使用者布建](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| 線上課程| SkillUp Online：[管理](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about)身分識別 <br> 瞭解如何整合多個 SaaS 應用程式的 Azure AD，以及保護使用者對這些應用程式的存取。 |
| 書籍| [Web 應用程式的新式驗證， (開發人員參考) 第一版的 Azure Active Directory](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0)。  <br> 這是建立這些新環境 Active Directory 驗證解決方案的權威深入指南。 |
| 教學課程| 請參閱 [有關如何整合 SaaS 應用程式與 Azure AD 的教學課程清單](../saas-apps/tutorial-list.md)。 |
| 常見問題集| 自動使用者布[建的常見問題](../app-provisioning/user-provisioning.md) |

### <a name="solution-architectures"></a>解決方案架構

Azure AD 布建服務會藉由連接到每個應用程式廠商所提供的使用者管理 API 端點，將使用者布建至 SaaS 應用程式和其他系統。 這些使用者管理 API 端點可以讓 Azure AD 以程式設計方式建立、更新和移除使用者。

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>混合式企業的自動使用者布建

在此範例中，會在連線到內部部署目錄的 HR 資料庫中建立使用者和或群組。 Azure AD 布建服務會管理目標 SaaS 應用程式的自動使用者布建。

 ![使用者布建](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**工作流程的描述：**

1. 使用者/群組是在內部部署 HR 應用程式/系統（例如 SAP）中建立。 

1. **Azure AD Connect 代理程式** 會 (從本機 AD) 到 Azure AD 的使用者和群組，執行已排程的身分識別同步處理。

1. **Azure AD** 布建服務會開始針對來源系統和目標系統的 [初始週期](../app-provisioning/user-provisioning.md) 。 

1. **Azure AD** 布建服務會在來源系統中查詢初始週期之後變更的任何使用者和群組，並將變更推送至 [增量週期](../app-provisioning/user-provisioning.md)。

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>針對僅限雲端的企業自動提供使用者布建

在此範例中，使用者會在 Azure AD 中建立，而 Azure AD 布建服務會管理自動使用者布建至目標 (SaaS) 應用程式。

![此圖顯示透過 Azure A D 布建服務，從內部部署 H R 應用程式到目標 a 的應用程式的使用者/群組建立流程。](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**工作流程的描述：**

1. 在 Azure AD 中建立使用者/群組。

1. **Azure AD** 布建服務會開始針對來源系統和目標系統的 [初始週期](../app-provisioning/user-provisioning.md) 。 

1. **Azure AD** 布建服務會查詢來源系統是否有任何使用者和群組在初始迴圈之後更新，並執行任何累加 [週期](../app-provisioning/user-provisioning.md)。

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>雲端 HR 應用程式的自動使用者布建 

在此範例中，使用者和或群組會建立在雲端 HR 應用程式中，例如 Workday 和 SuccessFactors。 Azure AD 布建服務和 Azure AD Connect 布建代理程式會將使用者資料從雲端 HR 應用程式租使用者布建到 AD。 當帳戶在 AD 中更新後，它會透過 Azure AD Connect 與 Azure AD 同步處理，並可將電子郵件地址和使用者名稱屬性寫回給 cloud HR 應用程式租使用者。

![圖2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **HR 小組** 在雲端 HR 應用程式租使用者中執行交易。
2.  **Azure AD** 布建服務會從雲端 HR 應用程式租使用者執行排定的週期，並識別需要處理以與 AD 同步的變更。
3.  **Azure AD** 布建服務會使用包含 AD 帳戶建立/更新/啟用/停用作業的要求承載來叫用 Azure AD Connect 布建代理程式。
4.  **Azure AD Connect 布建代理程式** 使用服務帳戶來管理 AD 帳戶資料。
5.  **Azure AD Connect** 執行 delta sync 以在 AD 中提取更新。
6.  **AD** 更新會與 Azure AD 同步處理。 
7.  **Azure AD 將服務** 回寫電子郵件屬性和使用者名稱從 Azure AD 布建到雲端 HR 應用程式租使用者。

## <a name="plan-the-deployment-project"></a>規劃部署專案

請考慮您的組織需要判斷在您的環境中部署使用者布建的策略。

### <a name="engage-the-right-stakeholders"></a>包含正確的專案關係人

當技術專案失敗時，通常是因為對影響、結果和責任的預期不相符。 若要避免這些錯誤，請 [確定您已吸引適當的專案關係人](../fundamentals/active-directory-deployment-plans.md) ，並且藉由記錄專案關係人和專案的專案輸入和責任，瞭解專案中的專案關係人角色。

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功非常重要。 主動與您的使用者溝通其體驗將如何改變、何時會改變，以及如何在遇到問題時取得支援。

### <a name="plan-a-pilot"></a>規劃試驗

建議您在測試環境中設定自動使用者布建的初始設定，然後將其調整為生產環境中的所有使用者。 請參閱執行試驗的 [最佳作法](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) 。

#### <a name="best-practices-for-a-pilot"></a>試驗的最佳作法  

試驗可讓您使用小型群組進行測試，再為所有人部署功能。 確定在測試過程中，您組織內的每個使用案例都經過徹底測試。

在您的第一波，將目標設為 IT、可用性，以及可以測試並提供意見反應的其他適當使用者。 您可以使用此意見反應，進一步開發您傳送給使用者的通訊和指示，並讓您深入瞭解支援人員可能會看到的問題類型。

藉由增加) 目標的群組 (範圍，將推出擴大到較大的使用者群組。 這可以透過 [動態群組成員資格](../enterprise-users/groups-dynamic-membership.md)來完成，或手動將使用者新增至目標群組， (s) 。

## <a name="plan-application-connections-and-administration"></a>規劃應用程式連接和管理

使用 Azure AD 入口網站來查看和管理支援布建的所有應用程式。 請參閱 [在入口網站中尋找您的應用程式](../app-provisioning/configure-automatic-user-provisioning-portal.md)。

### <a name="determine-the-type-of-connector-to-use"></a>判斷要使用的連接器類型

啟用及設定自動佈建所需的實際步驟會因應用程式而有所不同。 如果您想要自動布建的應用程式列在 [Azure AD SaaS 應用程式庫](../saas-apps/tutorial-list.md)中，則您應該選取 [應用程式特定的整合教學](../saas-apps/tutorial-list.md) 課程，以設定其預先整合的使用者布建連接器。

如果沒有，請遵循下列步驟：

1. [建立](../develop/v2-howto-app-gallery-listing.md) 預先整合的使用者布建連接器的要求。 我們的團隊將與您和應用程式開發人員合作，讓您的應用程式在支援 SCIM 時，將應用程式上架到平臺。

1. 使用 [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 應用程式的一般使用者布建支援。 這是 Azure AD 在沒有預先整合的布建連接器的情況下，將使用者布建到應用程式的需求。

1. 如果應用程式能夠使用 BYOA SCIM 連接器，請參閱 [BYOA SCIM 整合教學](../app-provisioning/use-scim-to-provision-users-and-groups.md) 課程，以設定應用程式的 BYOA SCIM 連接器。

如需詳細資訊，請參閱[我可以使用哪些應用程式和系統來 Azure AD 自動使用者](../app-provisioning/user-provisioning.md)布建？

### <a name="collect-information-to-authorize-application-access"></a>收集資訊以授權應用程式存取

設定自動使用者布建是每個應用程式的進程。 針對每個應用程式，您需要提供 [系統管理員認證](../app-provisioning/configure-automatic-user-provisioning-portal.md) ，以連接到目標系統的使用者管理端點。

下圖顯示其中一個版本的必要系統管理員認證：

![管理使用者帳戶佈建設定的佈建畫面](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

雖然有些應用程式需要系統管理員使用者名稱和密碼，但其他應用程式可能需要持有人權杖。

## <a name="plan-user-and-group-provisioning"></a>規劃使用者和群組布建

如果您啟用企業應用程式的使用者布建， [Azure 入口網站](https://portal.azure.com/) 會透過屬性對應來控制其屬性值。

### <a name="determine-operations-for-each-saas-app"></a>判斷每個 SaaS 應用程式的作業

每個應用程式可能會有唯一的使用者或群組屬性，而這些屬性必須對應至您 Azure AD 中的屬性。 應用程式只能使用 CRUD 作業的子集。

針對每個應用程式，記錄下列資訊：

* 要在目標系統的使用者和群組物件上執行的 CRUD 布建作業。 例如，每個 SaaS 應用程式商務擁有者可能不想要所有可能的作業。

* 來源系統中的可用屬性

* 目標系統中的可用屬性

* 系統之間的屬性對應。

### <a name="choose-which-users-and-groups-to-provision"></a>選擇要布建的使用者和群組

在執行自動使用者布建之前，您必須先決定要布建到應用程式的使用者和群組。

* 使用 [範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 來定義以屬性為基礎的規則，以決定要將哪些使用者布建到應用程式。

* 接下來，視需要使用 [使用者和群組指派](../manage-apps/assign-user-or-group-access-portal.md) 進行額外的篩選。

### <a name="define-user-and-group-attribute-mapping"></a>定義使用者和群組屬性對應

若要執行自動使用者布建，您必須定義應用程式所需的使用者和群組屬性。 Azure AD 使用者物件和每個 SaaS 應用程式的使用者物件之間，有一組預先設定的屬性和 [屬性](../app-provisioning/configure-automatic-user-provisioning-portal.md) 對應。 並非所有的 SaaS 應用程式都會啟用群組屬性。

Azure AD 支援直接屬性對屬性的對應、提供常數值，或 [撰寫屬性對應的運算式](../app-provisioning/functions-for-customizing-application-data.md)。 這種彈性可讓您更精確地控制將在目標系統的屬性中填入的內容。 您可以使用 [MICROSOFT GRAPH API](../app-provisioning/export-import-provisioning-configuration.md) 和 Graph Explorer，將使用者布建屬性對應和架構匯出至 JSON 檔案，然後將它匯入回 Azure AD。

如需詳細資訊，請參閱 [Azure Active Directory 中的自訂 SaaS 應用程式的使用者](../app-provisioning/customize-application-attributes.md)布建 Attribute-Mappings。

### <a name="special-considerations-for-user-provisioning"></a>使用者布建的特殊考慮

請考慮下列各項，以減少部署後的問題：

* 確定在來源與目標應用程式之間用來對應使用者/群組物件的屬性具有復原能力。 如果屬性變更 (例如使用者移至公司) 的不同部分，則不會導致使用者/群組不正確布建。

* 應用程式可能會有需要符合的特定限制及/或需求，才能讓使用者布建正常運作。 例如，可截斷某些屬性值的時間。 請參閱每個應用程式專用的 [自動使用者](../saas-apps/tutorial-list.md) 布建教學課程。

* 確認來源與目標系統之間的架構一致性。 常見的問題包括 UPN 或郵件不相符的屬性。 例如，Azure AD 中的 UPN 會在 *john_smith@contoso.com* 應用程式中設定為和，則為 *jsmith@contoso.com* 。 如需詳細資訊，請參閱 [使用者和群組架構參考](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

## <a name="plan-testing-and-security"></a>規劃測試和安全性

在您部署的每個階段中，請確定您要測試結果是否如預期般，以及審核布建週期。

### <a name="plan-testing"></a>規劃測試

設定應用程式的自動使用者布建之後，您將會執行測試案例，以確認此解決方案符合您組織的需求。

| 案例| 預期的結果 |
| - | - |
| 將使用者新增至指派給目標系統的群組 | 系統會在目標系統中布建使用者物件。 <br>使用者可以登入目標系統，並執行所需的動作。 |
| 從指派給目標系統的群組中移除使用者 | 取消布建在目標系統中的使用者物件。<br>使用者無法登入目標系統。 |
| 任何方法都會在 Azure AD 中更新使用者資訊 | 更新的使用者屬性會在累加迴圈之後反映于目標系統中 |
| 使用者超出範圍 | 使用者物件已停用或刪除。 <br>注意：已針對 [Workday](skip-out-of-scope-deletions.md)布建覆寫此行為。 |

### <a name="plan-security"></a>規劃安全性

在部署過程中，需要進行安全性審核是很常見的。 如果您需要安全性審查，請參閱許多 Azure AD [白皮書](https://www.microsoft.com/download/details.aspx?id=36391) ，提供身分識別即服務的總覽。

### <a name="plan-rollback"></a>規劃復原

如果自動使用者布建實行在生產環境中無法正常運作，下列復原步驟可協助您還原為先前的已知良好狀態：

1. 請參閱布建 [摘要報告](../app-provisioning/check-status-user-account-provisioning.md) 和布建 [記錄](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) ，以判斷受影響的使用者和/或群組上發生不正確的作業。

1. 使用布建審核記錄來判斷受影響的使用者和/或群組最後已知的良好狀態。 此外，請參閱 (Azure AD 或 AD) 的來源系統。

1. 與應用程式擁有者合作，以更新直接在應用程式中受影響的使用者和/或群組，並使用最後一個已知的良好狀態值。

## <a name="deploy-automatic-user-provisioning-service"></a>部署自動使用者布建服務

選擇符合您解決方案需求的步驟。

### <a name="prepare-for-the-initial-cycle"></a>為初始週期做好準備

當 Azure AD 布建服務第一次執行時，針對來源系統和目標系統的初始迴圈會針對每個目標系統建立所有使用者物件的快照集。

為應用程式啟用自動布建時，初始週期可能需要20分鐘到數小時的時間。 持續時間取決於 Azure AD 目錄的大小和布建範圍中的使用者數目。

布建服務會在初始迴圈之後儲存這兩個系統的狀態，以改善後續累加週期的效能。

### <a name="configure-automatic-user-provisioning"></a>設定使用者自動佈建

使用 [Azure 入口網站](https://portal.azure.com/) 來管理使用者帳戶的自動布建和解除布建，以提供支援的應用程式。 遵循[如何? 設定自動布建至應用程式](../app-provisioning/user-provisioning.md)的步驟？

您也可以使用 [Microsoft Graph API](/graph/api/resources/synchronization-overview)來設定和管理 Azure AD 使用者佈建服務。

## <a name="manage-automatic-user-provisioning"></a>管理自動使用者布建

現在您已部署，您需要管理解決方案。

### <a name="monitor-user-provisioning-operation-health"></a>監視使用者布建操作健全狀況

在成功 [初始週期](../app-provisioning/user-provisioning.md)之後，Azure AD 布建服務將會以每個應用程式特定的間隔無限期地執行累加更新，直到發生下列其中一個事件為止：

* 這項服務會以手動方式停止，並使用 [Azure 入口網站](https://portal.azure.com/)或使用適當的 [Microsoft Graph API](/graph/api/resources/synchronization-overview) 命令來觸發新的初始週期。

* 新的初始週期是由屬性對應或範圍篩選準則的變更所觸發。

* 布建程式會因為錯誤率過高而進入隔離狀態，並會在其自動停用時維持隔離狀態超過四周。

若要檢查這些事件，以及布建服務所執行的所有其他活動，請參閱 Azure AD 布建 [記錄](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。

若要瞭解布建週期花費多少時間，並監視布建作業的進度，您可以 [檢查使用者](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)布建的狀態。

### <a name="gain-insights-from-reports"></a>從報表深入解析

Azure AD 可以透過審核記錄和報告，為您組織的使用者布建使用量和操作健全狀況提供 [更深入的見解](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) 。

系統管理員應檢查布建摘要報告，以監視布建作業的操作健全狀況。 布建服務所執行的所有活動都會記錄在 Azure AD audit 記錄檔中。 請參閱 [教學課程：自動布建使用者帳戶的報告](../app-provisioning/check-status-user-account-provisioning.md)。

我們建議您假設的擁有權，並以符合組織需求的步調來取用這些報表。 Azure AD 會保留最多30天的審核資料。

### <a name="troubleshoot"></a>疑難排解

請參閱下列連結，以針對可能在布建期間啟動的任何問題進行疑難排解：

* [設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題](../app-provisioning/application-provisioning-config-problem.md)

* [將屬性從內部部署 Active Directory 同步處理至 Azure AD 以布建至應用程式](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [在設定 Azure Active Directory 資源庫應用程式之使用者佈建的情況下儲存系統管理員認證時發生問題](./user-provisioning.md)

* [未將任何使用者佈建至 Azure AD 資源庫應用程式](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [一組錯誤的使用者佈建至 Azure AD 資源庫應用程式](../manage-apps/add-application-portal-assign-users.md)

### <a name="helpful-documentation"></a>實用的文件

* [撰寫屬性對應的運算式](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD 同步處理 API 概觀](/graph/api/resources/synchronization-overview)

* [略過刪除超出範圍的使用者帳戶](skip-out-of-scope-deletions.md)

* [Azure AD Connect 布建代理程式：版本發行歷程記錄](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>資源

* [提供產品意見反應](https://feedback.azure.com/forums/169401-azure-active-directory)

* [隨時掌握 Azure AD 的新功能](https://azure.microsoft.com/updates/?product=active-directory)

* [堆疊溢位 Azure AD 論壇](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>後續步驟
* [設定自動使用者布建](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [使用 Microsoft Graph API 來匯出或匯入布建設定](../app-provisioning/export-import-provisioning-configuration.md)

* [在 Azure Active directory 中撰寫屬性對應的運算式](../app-provisioning/functions-for-customizing-application-data.md)
