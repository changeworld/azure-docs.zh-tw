---
title: 規劃雲端 HR 應用程式以 Azure Active Directory 使用者布建
description: 本文說明整合雲端 HR 系統的部署程式，例如 Workday 和 SuccessFactors，以及 Azure Active Directory。 將 Azure AD 與您的雲端 HR 系統整合，會產生完整的身分識別生命週期管理系統。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 3a3f461941bfcd5091ebb14818bac05d6844b3fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706358"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>規劃雲端 HR 應用程式以 Azure Active Directory 使用者布建

在過去，IT 人員必須依賴手動方法來建立、更新及刪除員工。 它們使用了像是上傳 CSV 檔案或自訂腳本等方法來同步處理員工資料。 這些布建程式很容易出錯、不安全且難以管理。

為了管理員工、廠商或臨時員工的身分識別生命週期， [Azure Active Directory (Azure AD) 使用者](../app-provisioning/user-provisioning.md) 布建服務可與雲端式人力資源整合， (HR) 應用程式。 應用程式的範例包括 Workday 或 SuccessFactors。

Azure AD 使用這項整合來啟用下列雲端 HR 應用程式 (應用程式) 工作流程：

- **將使用者布建至 Active Directory：** 將選取的使用者集合從雲端 HR 應用程式布建到一或多個 Active Directory 網域。
- **將僅限雲端的使用者布建至 Azure AD：** 在未使用 Active Directory 的情況下，請直接從雲端 HR 應用程式將使用者布建到 Azure AD。
- **回寫到雲端 HR 應用程式：** 將電子郵件地址和使用者名稱屬性從 Azure AD 寫回雲端 HR 應用程式。

> [!NOTE]
> 此部署計畫說明如何使用 Azure AD 使用者布建來部署雲端 HR 應用程式工作流程。 如需有關如何將自動使用者布建部署到軟體即服務 (SaaS) 應用程式的詳細資訊，請參閱 [規劃自動使用者](https://aka.ms/deploymentplans/provisioning)布建部署。

## <a name="enabled-hr-scenarios"></a>啟用的 HR 案例

Azure AD 的使用者布建服務可讓您自動化下列 HR 式身分識別生命週期管理案例：

- **雇用新員工：** 當新的員工新增至 cloud HR 應用程式時，會自動在 Active Directory 中建立使用者帳戶，並 Azure AD 並提供將電子郵件地址和使用者名稱屬性回寫至雲端 HR 應用程式的選項。
- **員工屬性和設定檔更新：** 當 cloud HR 應用程式中的員工記錄（例如姓名、職稱或管理員）更新時，其使用者帳戶會在 Active Directory 中自動更新並 Azure AD。
- **員工終止：** 當員工在雲端 HR 應用程式中結束時，其使用者帳戶會在 Active Directory 和 Azure AD 中自動停用。
- **員工重新雇用：** 當員工在雲端 HR 應用程式中 workday 重新雇用時，舊帳戶可以自動重新開機或重新布建至 Active Directory 並 Azure AD。

## <a name="who-is-this-integration-best-suited-for"></a>這項整合最適合哪些？

雲端 HR 應用程式與 Azure AD 使用者布建整合非常適合下列組織：

- 需要雲端 HR 使用者布建的預建雲端式解決方案。
- 需要從雲端 HR 應用程式直接布建使用者，以 Active Directory 或 Azure AD。
- 要求使用者使用從雲端 HR 應用程式取得的資料進行布建。
- 需要根據在雲端 HR 應用程式中偵測到的變更資訊，來加入、移動及離開使用者，使其同步至一或多個 Active Directory 的樹系、網域和 Ou。
- 使用電子郵件的 Microsoft 365。

## <a name="learn"></a>學習

使用者布建可為進行中的身分識別治理建立基礎。 它能增強依賴授權身分識別資料的商務程式品質。

### <a name="terms"></a>詞彙

本文使用下列詞彙：

- **來源系統**： Azure AD 布建來源的使用者存放庫。 例如 Workday 或 SuccessFactors 等雲端 HR 應用程式。
- **目標系統**： Azure AD 布建的使用者存放庫。 範例包括 Active Directory、Azure AD、Microsoft 365 或其他 SaaS 應用程式。
- **權變-權變 process**：使用雲端 HR 應用程式作為記錄的系統，用於新進員工、傳輸和終止的詞彙。 當服務成功將必要的屬性布建到目標系統時，程式就會完成。

### <a name="key-benefits"></a>主要權益

這項 HR 驅動 IT 布建的功能提供下列重要的商業優勢：

- **提高生產力：** 您現在可以自動指派使用者帳戶和 Microsoft 365 授權，並提供金鑰群組的存取權。 自動指派可讓新進員工立即存取其作業工具，並提高生產力。
- **管理風險：** 您可以根據員工狀態或群組成員資格，使用從雲端 HR 應用程式流入的資料來自動化變更，以提高安全性。 自動化變更可確保使用者在轉換或離開組織時，會自動更新使用者身分識別以及金鑰應用程式的存取權。
- **解決合規性和治理：** Azure AD 針對來源和目標系統的應用程式所執行的使用者布建要求，支援原生審核記錄。 您可以使用 [審核]，從單一畫面追蹤可存取應用程式的人員。
- **管理成本：** 自動布建可避免與手動布建相關的低效率和人為錯誤，以降低成本。 它可減少使用舊版和過期平臺建立的自訂開發使用者布建解決方案需求。

### <a name="licensing"></a>授權

若要將雲端 HR 應用程式設定為 Azure AD 使用者布建整合，您需要有效的 [Azure AD Premium 授權](https://azure.microsoft.com/pricing/details/active-directory/) 和雲端 HR 應用程式的授權，例如 Workday 或 SuccessFactors。

針對將源自雲端 HR 應用程式並布建至 Active Directory 或 Azure AD 的每個使用者，您也需要有效的 Azure AD Premium P1 或更高的訂用帳戶授權。 在雲端 HR 應用程式中所擁有的授權數量不正確，可能會在使用者布建期間導致錯誤。

### <a name="prerequisites"></a>必要條件

- Azure AD [混合式身分識別管理員](../users-groups-roles/directory-assign-admin-roles.md#hybrid-identity-administrator)  來設定 Azure AD Connect 布建代理程式。
- Azure AD [應用程式系統管理員](../users-groups-roles/directory-assign-admin-roles.md#application-administrator) 角色，以在 Azure 入口網站中設定布建應用程式
- 雲端 HR 應用程式的測試和實際執行實例。
- 雲端 HR 應用程式中的系統管理員許可權，可建立系統整合使用者並進行變更以測試員工資料，以供測試之用。
- 針對使用者布建至 Active Directory，需要執行 Windows Server 2012 或更新版本與 .NET 4.7.1 + runtime 的伺服器，才能裝載 Azure AD Connect 布建代理程式
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) 在 Active Directory 和 Azure AD 之間同步處理使用者。

### <a name="training-resources"></a>訓練資源

| **資源** | **連結和描述** |
|:-|:-|
| 影片 | [什麼是 Active Azure 目錄中的使用者布建？](https://youtu.be/_ZjARPpI6NI) |
| | [如何在 Active Azure 目錄中部署使用者布建](https://youtu.be/pKzyts6kfrw) |
| 教學課程 | [有關如何整合 SaaS 應用程式與 Azure AD 的教學課程清單](../saas-apps/tutorial-list.md) |
| | [教學課程：設定 Workday 來自動佈建使用者](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| 常見問題集 | [自動的使用者佈建](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [從 Workday 布建至 Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>方案架構

下列範例說明一般混合式環境的端對端使用者布建解決方案架構，包括：

- **從雲端 HR 應用程式到 Active Directory 的授權 HR 資料流程程。** 在此流程中，HR 事件 (權變-權變 process) 是在雲端 HR 應用程式租使用者中起始。 Azure AD 布建服務和 Azure AD Connect 布建代理程式會將使用者資料從雲端 HR 應用程式租使用者布建到 Active Directory。 視事件而定，它可能會導致在 Active Directory 中建立、更新、啟用和停用作業。
- **與 Azure AD 同步處理，並將內部部署 Active Directory 的電子郵件和使用者名稱寫回雲端 HR 應用程式。** 在 Active Directory 中的帳戶更新之後，就會透過 Azure AD Connect 與 Azure AD 同步處理。 電子郵件地址和使用者名稱屬性可以寫回至 cloud HR 應用程式租使用者。

![Workflow diagram](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>工作流程的描述

下列主要步驟如下圖所示：  

1. **HR 小組** 在雲端 HR 應用程式租使用者中執行交易。
2. **Azure AD** 布建服務會從雲端 HR 應用程式租使用者執行排定的週期，並識別需要處理以與 Active Directory 同步的變更。
3. **Azure AD** 布建服務會使用包含 Active Directory 帳戶建立、更新、啟用和停用作業的要求承載，叫用 Azure AD Connect 布建代理程式。
4. **Azure AD Connect 布建代理程式** 使用服務帳戶來管理 Active Directory 帳戶資料。
5. **Azure AD Connect** 在 Active Directory 中執行 delta [sync](../hybrid/how-to-connect-sync-whatis.md) 以提取更新。
6. **Active Directory** 更新會與 Azure AD 同步處理。
7. **Azure AD** 布建服務會將電子郵件屬性和使用者名稱從 Azure AD 備份到雲端 HR 應用程式租使用者。

## <a name="plan-the-deployment-project"></a>規劃部署專案

在決定環境中此部署的策略時，請考慮您的組織需求。

### <a name="engage-the-right-stakeholders"></a>包含正確的專案關係人

當技術專案失敗時，通常會原因，以避免影響、結果和責任的預期。 若要避免這些問題，請 [確定您正參與適當的專案關係人](https://aka.ms/deploymentplans)。 此外，也請確定專案中的專案關係人角色已充分瞭解。 記錄專案關係人及其專案輸入和責任。

包含 HR 組織的代表，可提供現有 HR 商務程式和背景工作角色身分識別的輸入，以及作業資料處理需求。

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功都非常重要。 主動與您的使用者溝通其體驗的變更時機和方式。 讓他們知道如何在遇到問題時獲得支援。

### <a name="plan-a-pilot"></a>規劃試驗

將 HR 商務程式和身分識別工作流程從雲端 HR 應用程式整合到目標系統，需要大量的資料驗證、資料轉換、資料清理，以及端對端測試，您才能將解決方案部署到生產環境。

在 [試驗環境](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) 中執行初始設定，然後將其調整為生產環境中的所有使用者。

## <a name="select-cloud-hr-provisioning-connector-apps"></a>選取雲端 HR 布建連接器應用程式

為了協助 Azure AD 在雲端 HR 應用程式和 Active Directory 之間布建工作流程，您可以從 Azure AD 應用程式資源庫新增多個布建連接器應用程式：

- **雲端 hr 應用程式可 Active Directory 使用者**布建：此布建連接器應用程式可協助從雲端 hr 應用程式布建到單一 Active Directory 網域的使用者帳戶。 如果您擁有多個網域，則可以針對要進行佈建的每個 Active Directory 網域，從 Azure AD 應用程式庫新增此應用程式的執行個體。
- **雲端 hr 應用程式可 Azure AD 使用者**布建：雖然 Azure AD Connect 是用來同步處理 Active Directory 使用者 Azure AD 的工具，但此布建連接器應用程式可用來協助將僅限雲端的使用者從雲端 HR 應用程式布建到單一 Azure AD 租使用者。
- **雲端 hr 應用程式回寫**：此布建連接器應用程式可協助將使用者的電子郵件地址回寫，從 Azure AD 到雲端 HR 應用程式。

例如，下圖列出 Azure AD 應用程式庫中可用的 Workday 連接器應用程式。

![Azure Active Directory 入口網站應用程式資源庫](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>決策流程圖

使用下列決策流程圖，找出與您的案例相關的雲端 HR 布建應用程式。

![決策流程圖](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>設計 Azure AD Connect 布建代理程式部署拓撲

雲端 HR 應用程式與 Active Directory 之間的布建整合需要四個元件：

- 雲端 HR 應用程式租使用者
- 布建連接器應用程式
- Azure AD Connect 布建代理程式
- Active Directory 網域

Azure AD Connect 布建代理程式部署拓撲取決於雲端 HR 應用程式租使用者的數目，以及您打算整合的 Active Directory 子域。 如果您有多個 Active Directory 網域，則取決於 Active Directory 網域是 [連續或不](/windows-server/identity/ad-ds/plan/disjoint-namespace)相鄰。

根據您的決定，選擇其中一種部署案例：

- 單一雲端 HR 應用程式租使用者-> 以受信任樹系中的單一或多個 Active Directory 子域為目標
- 單一雲端 HR 應用程式租使用者-> 在脫離的 Active Directory 樹系中的多個子域

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>單一雲端 HR 應用程式租使用者-> 以受信任樹系中的單一或多個 Active Directory 子域為目標

我們建議您執行下列生產環境設定：

|需求|建議|
|:-|:-|
|要部署的 Azure AD Connect 布建代理程式數目|高可用性和容錯移轉的兩個 () 
|要設定的布建連接器應用程式數目|每個子網域一個應用程式|
|Azure AD Connect 布建代理程式的伺服器主機|具有地理位置 Active Directory 網域控制站的 Windows 2012 R2 +</br>可以與 Azure AD Connect 服務共存|

![流向內部部署代理程式](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>單一雲端 HR 應用程式租使用者-> 在脫離的 Active Directory 樹系中的多個子域

此案例牽涉到將使用者從雲端 HR 應用程式布建到脫離的 Active Directory 樹系中的網域。

我們建議您執行下列生產環境設定：

|需求|建議|
|:-|:-|
|要在內部部署環境中部署的 Azure AD Connect 布建代理程式數目|每個脫離 Active Directory 樹系的兩個|
|要設定的布建連接器應用程式數目|每個子網域一個應用程式|
|Azure AD Connect 布建代理程式的伺服器主機|具有地理位置 Active Directory 網域控制站的 Windows 2012 R2 +</br>可以與 Azure AD Connect 服務共存|

![單一雲端 HR 應用程式租使用者脫離 Active Directory 樹系](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect 布建代理程式需求

需要在執行 Windows 2012 R2 或更新版本的伺服器上部署一或多個 Azure AD Connect 布建代理程式，才能 Active Directory 使用者布建解決方案的雲端 HR 應用程式。 伺服器至少必須具有 4 GB RAM 和 .NET 4.7.1 + 執行時間。 確定主機伺服器具有目標 Active Directory 網域的網路存取權。

若要準備內部部署環境，Azure AD Connect 布建代理程式設定向導會向您的 Azure AD 租使用者註冊代理程式、 [開啟埠](../manage-apps/application-proxy-add-on-premises-application.md#open-ports)、 [允許存取 url](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)，以及支援 [輸出 HTTPS proxy](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)設定。

布建代理程式使用服務帳戶與 Active Directory 網域進行通訊。 在安裝代理程式之前，請在 Active Directory 消費者和電腦中建立符合下列需求的服務帳戶：

- 未過期的密碼
- 讀取、建立、刪除和管理使用者帳戶的委派控制項許可權

您可以選取應處理布建要求的網域控制站。 如果您有數個地理位置分散的網域控制站，請將布建代理程式安裝在與您慣用的網域控制站相同的網站中。 這項定位可改善端對端解決方案的可靠性和效能。

為了達到高可用性，您可以部署一個以上的 Azure AD Connect 布建代理程式。 註冊代理程式來處理同一組內部部署 Active Directory 網域。

## <a name="plan-scoping-filters-and-attribute-mapping"></a>規劃範圍篩選器和屬性對應

當您啟用從雲端 HR 應用程式布建至 Active Directory 或 Azure AD 時，Azure 入口網站會透過屬性對應來控制屬性值。

### <a name="define-scoping-filters"></a>定義範圍篩選器

使用 [範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 來定義以屬性為基礎的規則，以決定哪些使用者應該從雲端 HR 應用程式布建至 Active Directory 或 Azure AD。

當您起始權變流程時，請收集下列需求：

- 雲端 HR 應用程式是否用來將員工和臨時工作者都帶進面板？
- 您是否計畫使用雲端 HR 應用程式來 Azure AD 使用者布建，以管理員工和臨時工作者？
- 您是否打算推出雲端 HR 應用程式，只針對一部分雲端 HR 應用程式使用者 Azure AD 使用者布建？ 範例可能只是員工。

視您的需求而定，當您設定屬性對應時，可以設定 [ **來源物件範圍** ] 欄位，以選取雲端 HR 應用程式中的哪些使用者集合應在布建至 Active Directory 的範圍內。 如需詳細資訊，請參閱適用于常用範圍篩選器的 cloud HR 應用程式教學課程。

### <a name="determine-matching-attributes"></a>判斷相符的屬性

透過布建，您可以在來源與目標系統之間符合現有帳戶的能力。 當您將雲端 HR 應用程式與 Azure AD 布建服務整合時，您可以 [設定屬性對應](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) ，以決定哪些使用者資料應該從雲端 HR 應用程式流向 Active Directory 或 Azure AD。

當您起始權變流程時，請收集下列需求：

- 此雲端 HR 應用程式中用來識別每位使用者的唯一識別碼為何？
- 從身分識別生命週期的觀點來看，您要如何處理重新雇用？ 重新雇用要保留舊的員工識別碼嗎？
- 您是否處理未來的員工，並事先為他們建立 Active Directory 帳戶？
- 從身分識別生命週期的觀點來看，您要如何處理員工以進行臨時工作的轉換，或者是什麼？
- 轉換的使用者是否保留舊的 Active Directory 帳戶，或是否取得新帳戶？

視您的需求而定，Azure AD 藉由提供常數值或 [撰寫屬性對應的運算式](../app-provisioning/functions-for-customizing-application-data.md)，支援直接屬性對屬性的對應。 這項彈性可讓您充分掌控已在目標應用程式屬性中填入的功能。 您可以使用 [MICROSOFT GRAPH API](../app-provisioning/export-import-provisioning-configuration.md) 和 Graph Explorer，將使用者布建屬性對應和架構匯出至 JSON 檔案，然後將它匯入回 Azure AD。

根據預設，雲端 HR 應用程式中代表唯一員工識別碼的屬性會用來做為對應*至 Active Directory 中唯一屬性*的相符屬性。 例如，在 Workday 應用程式案例中， **workday** **WorkerID** 屬性會對應到 Active Directory 的 [ **員工** ] 屬性。

您可以設定多個相符的屬性，並指派相符的優先順序。 它們是在比對優先順序上進行評估。 只要找到相符項目，便不會評估進一步比對屬性。

您也可以 [自訂預設屬性](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)對應，例如變更或刪除現有的屬性對應。 您也可以根據您的業務需求建立新的屬性對應。 如需詳細資訊，請參閱雲端 HR 應用程式教學課程 (例如 [Workday](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration)) ，以取得要對應的自訂屬性清單。

### <a name="determine-user-account-status"></a>判斷使用者帳戶狀態

根據預設，布建連接器應用程式會將 HR 使用者設定檔狀態對應至 Active Directory 或 Azure AD 中的使用者帳戶狀態，以判斷是否要啟用或停用使用者帳戶。

當您起始 Joiners-Leavers 進程時，請收集下列需求。

| 處理序 | 需求 |
| - | - |
| **權變** | 從身分識別生命週期的觀點來看，您要如何處理重新雇用？ 重新雇用要保留舊的員工識別碼嗎？ |
| | 您是否處理未來的員工，並事先為他們建立 Active Directory 帳戶？ 這些帳戶會建立為啟用或停用狀態嗎？ |
| | 從身分識別生命週期的觀點來看，您要如何處理員工以進行臨時工作的轉換，或者是什麼？ |
| | 轉換的使用者是否保留舊的 Active Directory 帳戶，或是否取得新帳戶？ |
| **畢業生** | Active Directory 中的員工和臨時工作者的終止處理方式是否不同？ |
| | 處理使用者終止時，會考慮哪些有效日期？ |
| | 員工和臨時工作者轉換如何影響現有的 Active Directory 帳戶？ |
| | 您要如何處理 Active Directory 中的 Rescind 作業？ 如果未來雇用的員工是在 Active Directory 中建立，作為加入程式的一部分，則必須處理 Rescind 作業。 |

視您的需求而定，您可以使用 [Azure AD 運算式](../app-provisioning/functions-for-customizing-application-data.md) 來自訂對應邏輯，以便根據資料點的組合來啟用或停用 Active Directory 帳戶。

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>將雲端 HR 應用程式對應至 Active Directory 的使用者屬性

每個雲端 HR 應用程式都會隨附預設的雲端 HR 應用程式來 Active Directory 對應。

當您起始權變-權變進程時，請收集下列需求。

| 處理序 | 需求 |
| - | - |
| **權變** | Active Directory 帳戶建立程式是手動、自動或部分自動化？ |
| | 您是否計畫將自訂屬性從雲端 HR 應用程式傳播至 Active Directory？ |
| **搬運工** | 在雲端 HR 應用程式中進行移動器作業時，您想要處理哪些屬性？ |
| | 您是否在使用者更新時執行任何特定的屬性驗證？ 如果是，請提供詳細資料。 |
| **畢業生** | Active Directory 中的員工和臨時工作者的終止處理方式是否不同？ |
| | 處理使用者終止時，會考慮哪些有效日期？ |
| | 員工和臨時工作者轉換如何影響現有的 Active Directory 帳戶？ |

視您的需求而定，您可以修改對應以符合您的整合目標。 如需詳細資訊，請參閱特定的雲端 HR 應用程式教學課程 (例如 [Workday](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings)) ，以取得要對應的自訂屬性清單。

### <a name="generate-a-unique-attribute-value"></a>產生唯一的屬性值

當您起始權變程式時，當您設定的屬性（例如 CN、samAccountName 和 UPN）有唯一的條件約束時，您可能需要產生唯一的屬性值。

Azure AD 函數 [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) 會評估每個規則，然後檢查為目標系統中的唯一性所產生的值。 如需範例，請參閱為 [userPrincipalName (UPN) 屬性產生唯一值](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)。

> [!NOTE]
> 目前只有 Workday 可 Active Directory 使用者布建支援此函數。 它無法與其他布建應用程式搭配使用。

### <a name="configure-active-directory-ou-container-assignment"></a>設定 Active Directory OU 容器指派

常見的需求是將 Active Directory 使用者帳戶放入以商務單位、地點和部門為基礎的容器。 當您起始移動流覽程式時，如果有監督式組織變更，您可能需要在 Active Directory 中將使用者從一個 OU 移至另一個 OU。

使用 [參數 ( # B1 ](../app-provisioning/functions-for-customizing-application-data.md#switch) 函數來設定 OU 指派的商務邏輯，並將其對應至 Active Directory 屬性 **>parentdistinguishedname**。

例如，如果您想要根據 HR 屬性 **自治區**在 OU 中建立使用者，您可以使用下列運算式：

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

使用此運算式時，如果自治區值為達拉斯、奧斯丁、西雅圖或倫敦，則會在對應的 OU 中建立使用者帳戶。 如果沒有相符的，則會在預設 OU 中建立帳戶。

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>規劃新使用者帳戶的密碼傳遞

當您起始權變流程時，您必須設定並傳遞新使用者帳戶的暫時密碼。 透過雲端 HR Azure AD 使用者布建，您可以為使用者在第一天推出 Azure AD 的 [自助式密碼重設](../authentication/tutorial-enable-sspr.md) (SSPR) 功能。

SSPR 是一種簡單的方法，可讓 IT 系統管理員讓使用者重設其密碼或解除鎖定其帳戶。 您可以從雲端 HR 應用程式布建 **行動電話號碼** 屬性，以 Active Directory 並與 Azure AD 同步處理。 在 [ **行動電話號碼** ] 屬性位於 Azure AD 之後，您可以為使用者的帳戶啟用 SSPR。 然後，在第一天，新的使用者可以使用已註冊和已驗證的行動電話號碼進行驗證。

## <a name="plan-for-initial-cycle"></a>規劃初始週期

當 Azure AD 布建服務第一次執行時，它會針對雲端 HR 應用程式執行 [初始迴圈](../app-provisioning/how-provisioning-works.md#initial-cycle) ，以建立雲端 hr 應用程式中所有使用者物件的快照集。 初始週期所花費的時間，直接取決於存在於來源系統中的使用者人數。 有超過100000使用者的部分雲端 HR 應用程式租使用者的初始週期可能需要很長的時間。

**針對大型雲端 HR 應用程式租使用者 ( # B0 30000 使用者) ，** 以漸進階段執行初始週期。 只有在您驗證針對不同使用者布建案例的 Active Directory 中設定正確的屬性之後，才啟動增量更新。 遵循此處的順序。

1. 藉由設定 [範圍篩選器](#plan-scoping-filters-and-attribute-mapping)，只針對一組有限的使用者執行初始週期。
2. 確認 Active Directory 帳戶布建，以及為第一次執行選取的使用者設定的屬性值。 如果結果符合您的預期，請展開範圍篩選器以漸進方式包含更多使用者，並確認第二次執行的結果。

當您對測試使用者的初始週期結果感到滿意之後，請啟動累加 [式更新](../app-provisioning/how-provisioning-works.md#incremental-cycles)。

## <a name="plan-testing-and-security"></a>規劃測試和安全性

在您部署的每個階段，透過啟用使用者布建的初始試驗，請確定您要測試結果是否如預期般執行，以及審核布建週期。

### <a name="plan-testing"></a>規劃測試

設定 cloud HR 應用程式來 Azure AD 使用者布建之後，請執行測試案例，以確認此解決方案是否符合您組織的需求。

|案例|預期的結果|
|:-|:-|
|在雲端 HR 應用程式中雇用新的員工。| -使用者帳戶會在 Active Directory 中布建。</br>-使用者可以登入 Active Directory 網域應用程式，並執行所需的動作。</br>-如果已設定 Azure AD Connect 同步，則也會在 Azure AD 中建立使用者帳戶。
|使用者會在雲端 HR 應用程式中終止。|-Active Directory 中的使用者帳戶已停用。</br>-使用者無法登入受 Active Directory 保護的任何企業應用程式。
|雲端 HR 應用程式中的使用者監督組織已更新。|根據屬性對應，使用者帳戶會在 Active Directory 中從一個 OU 移至另一個 OU。|
|HR 會更新雲端 HR 應用程式中的使用者管理員。|Active Directory 中的 [管理員] 欄位會更新，以反映新的管理員名稱。|
|HR 重新雇用員工成為新的角色。|行為取決於雲端 HR 應用程式如何設定來產生員工識別碼：</br>-如果舊的員工識別碼重複使用於 rehire，連接器就會為使用者啟用現有的 Active Directory 帳戶。</br>-如果 rehire 取得新的員工識別碼，連接器會為使用者建立新的 Active Directory 帳戶。|
|HR 將員工轉換成合約工作者，反之亦然。|新的角色會建立新的 Active Directory 帳戶，而舊帳戶會在轉換生效日期停用。|

使用先前的結果來決定如何根據您建立的時程表，將自動使用者布建的執行轉換到生產環境。

> [!TIP]
> 當您重新整理具有生產資料的測試環境，以移除或遮罩敏感性個人資料以符合隱私權和安全性標準時，請使用像是減少資料和資料清除等技術。 

### <a name="plan-security"></a>規劃安全性

部署新的服務時，通常需要進行安全性審核。 如果需要或尚未進行安全性審核，請參閱許多 Azure AD [白皮書](https://www.microsoft.com/download/details.aspx?id=36391) ，這些白皮書提供身分識別即服務的總覽。

### <a name="plan-rollback"></a>規劃復原

雲端 HR 使用者布建實行可能無法在生產環境中如預期運作。 如果是，下列復原步驟可協助您還原為先前的已知良好狀態。

1. 請參閱布建 [摘要報告](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) 和布建 [記錄](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) ，以判斷哪些作業對受影響的使用者或群組執行了哪些不正確的作業。 如需布建摘要報告和記錄的詳細資訊，請參閱 [管理雲端 HR 應用程式使用者](#manage-your-configuration)布建。
2. 受影響的使用者或群組最後一個已知的良好狀態，可透過布建審核記錄來決定，或藉由檢查目標系統 (Azure AD 或 Active Directory) 。
3. 使用最後一個已知的良好狀態值，與應用程式擁有者合作，以更新直接在應用程式中影響的使用者或群組。

## <a name="deploy-the-cloud-hr-app"></a>部署雲端 HR 應用程式

選擇符合您解決方案需求的雲端 HR 應用程式。

**Workday**：若要將 workday 設定檔從 Workday 匯入 Active Directory 和 Azure AD，請參閱 [教學課程：設定 workday 來自動](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment)布建使用者。 （選擇性）您可以將電子郵件地址、使用者名稱和電話號碼寫回 Workday。

**SAP SuccessFactors**：若要將 SuccessFactors 中的背景工作設定檔匯入 Active Directory 和 Azure AD，請參閱 [教學課程：設定 SAP SuccessFactors 來自動](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)布建使用者。 （選擇性）您可以將電子郵件地址和使用者名稱寫回給 SuccessFactors。

## <a name="manage-your-configuration"></a>管理您的設定

Azure AD 可以透過審核記錄和報告，為您組織的使用者布建使用量和操作健全狀況提供更深入的見解。

### <a name="gain-insights-from-reports-and-logs"></a>從報表和記錄深入解析

在成功 [初始迴圈](../app-provisioning/how-provisioning-works.md#initial-cycle)之後，Azure AD 布建服務會繼續以每個應用程式專用的教學課程中定義的間隔，無限期地持續執行後端增量更新，直到發生下列其中一個事件為止：

- 已手動停止服務。 使用 [Azure 入口網站](https://portal.azure.com/) 或適當的 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) 命令觸發新的初始週期。
- 原因變更屬性對應或範圍篩選準則時，會觸發新的初始週期。
- 布建程式會因為錯誤率過高而進入隔離。 它會保持隔離狀態超過四周，此時會自動停用。

若要檢查這些事件和布建服務所執行的所有其他活動，請 [瞭解如何查看記錄並取得布建活動的報告](../app-provisioning/check-status-user-account-provisioning.md)。

#### <a name="azure-monitor-logs"></a>Azure 監視器記錄

布建服務所執行的所有活動都會記錄在 Azure AD audit 記錄檔中。 您可以將 Azure AD audit 記錄路由傳送到 Azure 監視器記錄檔，以供進一步分析。 使用 Azure 監視器記錄 (也稱為 Log Analytics 工作區) ，您可以查詢資料來尋找事件、分析趨勢，以及跨各種資料來源執行相互關聯。 觀賞這段 [影片](https://youtu.be/MP5IaCTwkQg) ，瞭解在實際使用者案例中使用 Azure 監視器記錄 Azure AD 記錄的優點。

安裝 [Azure AD 活動記錄的 log analytics 視圖](../reports-monitoring/howto-install-use-log-analytics-views.md) ，以存取您環境中布建事件的 [預建報表](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) 。

如需詳細資訊，請參閱如何 [使用您的 Azure 監視器記錄來分析 Azure AD 的活動記錄](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)。

### <a name="manage-personal-data"></a>管理個人資料

安裝在 Windows server 上的 Azure AD Connect 布建代理程式會根據您的雲端 HR 應用程式，在 Windows 事件記錄檔中建立記錄檔，以 Active Directory 屬性對應。 若要符合使用者隱私權義務，請設定 Windows 排程工作來清除事件記錄檔，並確定沒有任何資料保留超過48小時。

Azure AD 布建服務不會產生報告、執行分析或提供超過30天的見解，因為服務不會儲存、處理或保留超過30天的任何資料。

### <a name="troubleshoot"></a>疑難排解

若要針對可能在布建期間啟動的任何問題進行疑難排解，請參閱下列文章：

- [設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題](application-provisioning-config-problem.md)
- [將屬性從內部部署 Active Directory 同步處理至 Azure AD 以布建至應用程式](user-provisioning-sync-attributes-for-mapping.md)
- [在設定 Azure Active Directory 資源庫應用程式之使用者佈建的情況下儲存系統管理員認證時發生問題](application-provisioning-config-problem-storage-limit.md)
- [未將任何使用者佈建至 Azure AD 資源庫應用程式](application-provisioning-config-problem-no-users-provisioned.md)
- [一組錯誤的使用者佈建至 Azure AD 資源庫應用程式](application-provisioning-config-problem-wrong-users-provisioned.md)
- [設定 Windows 事件檢視器以進行代理程式疑難排解](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [設定 Azure 入口網站稽核記錄以進行服務疑難排解](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [了解 AD 使用者帳戶建立作業的記錄](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [了解管理員更新作業的記錄](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [解決常見的錯誤](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>後續步驟

- [撰寫屬性對應的運算式](functions-for-customizing-application-data.md)
- [Azure AD 同步處理 API 概觀](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [略過刪除超出範圍的使用者帳戶](skip-out-of-scope-deletions.md)
- [Azure AD Connect 布建代理程式：版本發行歷程記錄](provisioning-agent-release-version-history.md)