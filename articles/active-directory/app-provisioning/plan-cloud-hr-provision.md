---
title: 將雲 HR 應用程式規劃到 Azure 活動目錄使用者預配
description: 本文介紹了將雲 HR 系統（如工作日和成功因素）與 Azure 活動目錄集成的部署過程。 將 Azure AD 與雲 HR 系統集成，可建立完整的身份生命週期管理系統。
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 28abe2dfa5a1a13ba09e20202180cb5e47d94072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522427"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>將雲 HR 應用程式規劃到 Azure 活動目錄使用者預配

從歷史上看，IT 員工一直依靠手動方法來創建、更新和刪除員工。 他們使用了上傳 CSV 檔或自訂腳本等方法來同步員工資料。 這些預配過程容易出錯、不安全且難以管理。

為了管理員工、供應商或臨時工作人員的身份生命週期[，Azure Active Directory （Azure AD） 使用者預配服務](../app-provisioning/user-provisioning.md)提供與基於雲的人力資源 （HR） 應用程式的集成。 應用程式的示例包括工作日或成功因素。

Azure AD 使用此集成啟用以下雲 HR 應用程式（應用）工作流：

- **將使用者預配到活動目錄：** 將所選使用者集從雲 HR 應用預配到一個或多個活動目錄域中。
- **將僅雲使用者預配到 Azure AD：** 在不使用 Active Directory 的情況下，將使用者直接從雲 HR 應用預配到 Azure AD。
- **寫回雲 HR 應用：** 將 Azure AD 的電子郵件地址和使用者名屬性寫回雲 HR 應用。

> [!NOTE]
> 此部署計畫演示如何使用 Azure AD 使用者預配部署雲 HR 應用工作流。 有關如何將自動使用者預配部署到軟體即服務 （SaaS） 應用的資訊，請參閱[計畫自動使用者預配部署](https://aka.ms/deploymentplans/provisioning)。

## <a name="enabled-hr-scenarios"></a>啟用 HR 方案

Azure AD 使用者預配服務支援以下基於 HR 的身份生命週期管理方案的自動化：

- **新員工招聘：** 將新員工添加到雲 HR 應用時，將自動在 Active Directory 和 Azure AD 中創建使用者帳戶，並可以選擇將電子郵件地址和使用者名屬性寫回雲 HR 應用。
- **員工屬性和設定檔更新：** 在雲 HR 應用中更新員工記錄（如姓名、職務或經理）時，其使用者帳戶將自動在 Active Directory 和 Azure AD 中更新。
- **員工解雇：** 當員工在雲 HR 應用中終止時，其使用者帳戶將自動在活動目錄和 Azure AD 中禁用。
- **員工重新雇用：** 在雲 HR 應用中重新雇用員工時，可以自動重新啟動其舊帳戶或重新預配到活動目錄和 Azure AD。

## <a name="who-is-this-integration-best-suited-for"></a>這種集成最適合誰？

與 Azure AD 使用者預配的雲 HR 應用集成非常適合以下組織：

- 想要一個用於雲 HR 使用者預配的基於雲的預構建解決方案。
- 需要從雲 HR 應用直接使用者預配到活動目錄或 Azure AD。
- 要求使用從雲 HR 應用獲取的資料預配使用者。
- 要求僅根據雲 HR 應用中檢測到的更改資訊將使用者連接到一個或多個活動目錄林、域和 O。
- 使用 Office 365 進行電子郵件。

## <a name="learn"></a>學習

使用者預配為持續的身份治理奠定了基礎。 它增強了依賴于權威標識資料的業務流程的品質。

### <a name="terms"></a>詞彙

本文使用以下術語：

- **源系統**：Azure AD 提供的使用者的存儲庫。 例如雲 HR 應用，如工作日或成功因素。
- **目標系統**：Azure AD 規定的使用者存儲庫。 示例包括活動目錄、Azure AD、Office 365 或其他 SaaS 應用。
- **加入者-移動器-離開者流程**：使用雲 HR 應用作為記錄系統，用於新員工、轉移和終止的術語。 當服務成功向目標系統提供必要的屬性時，該過程將完成。

### <a name="key-benefits"></a>主要權益

這種 HR 驅動的 IT 配置功能提供了以下重大業務優勢：

- **提高生產率：** 您現在可以自動分配使用者帳戶和 Office 365 許可證，並提供對關鍵組的訪問。 自動化作業使新員工能夠立即訪問他們的工作工具並提高工作效率。
- **管理風險：** 您可以通過根據員工狀態或組成員身份（資料從雲 HR 應用流入）自動進行更改來提高安全性。 自動更改可確保使用者身份和對關鍵應用的訪問在使用者過渡或離開組織時自動更新。
- **解決合規性和治理問題：** Azure AD 支援本機稽核記錄，用於由源系統和目標系統的應用執行的使用者預配請求。 通過審核，您可以跟蹤誰可以從單個螢幕訪問應用。
- **管理成本：** 自動預配通過避免與手動預配相關的效率低下和人為錯誤來降低成本。 它通過使用舊版和過時的平臺，減少了對長期構建的自訂開發的使用者預配解決方案的需求。

### <a name="licensing"></a>授權

要將雲 HR 應用配置為 Azure AD 使用者預配集成，需要有效的[Azure AD 高級許可證](https://azure.microsoft.com/pricing/details/active-directory/)和雲 HR 應用（如工作日或成功因素）的許可證。

對於將從雲 HR 應用獲取並預配到活動目錄或 Azure AD 的每個使用者，還需要有效的 Azure AD 高級 P1 或更高訂閱許可證。 雲 HR 應用中擁有的任何不當數量的許可證都可能導致使用者預配過程中的錯誤。

### <a name="prerequisites"></a>Prerequisites

- Azure AD 全域管理員訪問以配置 Azure AD 連接預配代理。
- 雲 HR 應用的測試和生產實例。
- 雲 HR 應用中的管理員許可權，用於創建系統集成使用者並更改以測試員工資料以進行測試。
- 對於使用者預配到活動目錄，運行 Windows Server 2012 或更高版本具有 .NET 4.7.1+ 運行時的伺服器需要承載[Azure AD Connect 預配代理](https://go.microsoft.com/fwlink/?linkid=847801)。
- [Azure AD 連接](../hybrid/whatis-azure-ad-connect.md)用於在活動目錄和 Azure AD 之間同步使用者。

### <a name="training-resources"></a>培訓資源

| **資源** | **連結和說明** |
|:-|:-|
| 影片 | [活動 Azure 目錄中的使用者預配是什麼？](https://youtu.be/_ZjARPpI6NI) |
| | [如何在活動 Azure 目錄中部署使用者預配](https://youtu.be/pKzyts6kfrw) |
| 教學課程 | [有關如何將 SaaS 應用與 Azure AD 集成的教程清單](../saas-apps/tutorial-list.md) |
| | [教學課程︰設定 Workday 來自動佈建使用者](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| 常見問題集 | [自動的使用者佈建](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [從工作日預配到 Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>方案架構

以下示例描述了常見混合環境的端到端使用者預配解決方案體系結構，包括：

- **從雲 HR 應用到活動目錄的權威 HR 資料流程。** 在此流中，HR 事件（連接器-移動器-離開者進程）在雲 HR 應用租戶中啟動。 Azure AD 預配服務和 Azure AD 連接預配代理將雲 HR 應用租戶的使用者資料預配到活動目錄。 根據事件，它可能導致在 Active Directory 中創建、更新、啟用和禁用操作。
- **與 Azure AD 同步，並將電子郵件和使用者名從本地活動目錄寫回雲 HR 應用。** 在活動目錄中更新帳戶後，它將通過 Azure AD 連接與 Azure AD 同步。 電子郵件地址和使用者名屬性可以寫回雲 HR 應用租戶。

![Workflow diagram](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>工作流描述

下圖中指明瞭以下關鍵步驟：  

1. **HR 團隊**在雲 HR 應用租戶中執行事務。
2. **Azure AD 預配服務**從雲 HR 應用租戶運行計畫週期，並標識需要處理的更改才能與 Active Directory 同步。
3. **Azure AD 預配服務**調用 Azure AD Connect 預配代理，其請求有效負載包含活動目錄帳戶創建、更新、啟用和禁用操作。
4. **Azure AD 連接預配代理**使用服務帳戶來管理活動目錄帳戶資料。
5. **Azure AD 連接**運行增量[同步](../hybrid/how-to-connect-sync-whatis.md)以在活動目錄中提取更新。
6. **活動目錄**更新與 Azure AD 同步。
7. **Azure AD 預配服務**將電子郵件屬性和使用者名從 Azure AD 寫入雲 HR 應用租戶。

## <a name="plan-the-deployment-project"></a>規劃部署專案

在確定環境中此部署的策略時，請考慮組織需求。

### <a name="engage-the-right-stakeholders"></a>讓合適的利益相關者參與進來

當技術專案失敗時，它們通常是由於對影響、結果和責任的期望不匹配而達到這一要求。 為了避免這些陷阱[，請確保您與合適的利益相關者接洽](https://aka.ms/deploymentplans)。 還要確保專案中的利益相關者角色得到充分理解。 記錄利益相關者及其專案投入和責任。

包括人力資源組織的代表，他可以就現有 HR 業務流程和員工身份以及作業資料處理要求提供意見。

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功都至關重要。 主動與使用者溝通，瞭解其體驗何時和如何變化。 讓他們知道，如果他們遇到問題，如何獲得支援。

### <a name="plan-a-pilot"></a>規劃試點

將 HR 業務流程和身份工作流從雲 HR 應用集成到目標系統需要大量資料驗證、資料轉換、資料清理和端到端測試，然後才能將解決方案部署到生產中。

在將初始配置擴展到生產中的所有使用者之前，在[試驗環境中](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)運行初始配置。

## <a name="select-cloud-hr-provisioning-connector-apps"></a>選擇雲 HR 預配連接器應用

為了方便 Azure AD 預配雲 HR 應用和活動目錄之間的工作流，可以從 Azure AD 應用庫中添加多個預配連接器應用：

- **雲 HR 應用到活動目錄使用者預配**：此預配連接器應用有助於將使用者帳戶從雲 HR 應用預配到單個活動目錄域。 如果您擁有多個網域，則可以針對要進行佈建的每個 Active Directory 網域，從 Azure AD 應用程式庫新增此應用程式的執行個體。
- **雲 HR 應用到 Azure AD 使用者預配**：雖然 Azure AD 連接是應該用於將 Active Directory 使用者同步到 Azure AD 的工具，但此預配連接器應用可用於促進將僅雲使用者從雲 HR 應用預配到單個 Azure AD 租戶。
- **雲 HR 應用寫回**：此預配連接器應用便於將使用者的電子郵件地址從 Azure AD 寫回雲 HR 應用。

例如，下圖列出了 Azure AD 應用庫中可用的工日連接器應用。

![Azure 活動目錄門戶應用庫](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>決策流程圖

使用以下決策流程圖確定哪些雲 HR 預配應用與您的方案相關。

![決策流程圖](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>設計 Azure AD 連接預配代理部署拓撲

雲 HR 應用和活動目錄之間的預配集成需要四個元件：

- 雲 HR 應用租戶
- 預配連接器應用
- Azure AD 連接預配代理
- Active Directory 網域

Azure AD Connect 預配代理部署拓撲取決於計畫集成的雲 HR 應用租戶和活動目錄子域的數量。 如果您有多個活動目錄域，則取決於活動目錄域是連續的還是[不相聯的](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)。

根據您的決策，選擇其中一種部署方案：

- 單個雲 HR 應用租戶 ->目標信任樹系中的單個或多個活動目錄子域
- 單雲 HR 應用租戶 - >在不相交的活動目錄林中定位多個子域

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>單個雲 HR 應用租戶 ->目標信任樹系中的單個或多個活動目錄子域

我們建議使用以下生產配置：

|需求|建議|
|:-|:-|
|要部署的 Azure AD 連接預配代理數|兩個（用於高可用性和容錯移轉）
|要配置的預配連接器應用數|每個子域一個應用|
|Azure AD 連接預配代理的伺服器主機|Windows 2012 R2+ 具有定位活動目錄網域控制站的視線</br>可與 Azure AD 連接服務共存|

![流向本地代理](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>單雲 HR 應用租戶 - >在不相交的活動目錄林中定位多個子域

此方案涉及將使用者從雲 HR 應用預配到不相交的活動目錄林中的域。

我們建議使用以下生產配置：

|需求|建議|
|:-|:-|
|要在本地部署的 Azure AD 連接預配代理數|每個不交合一的原始目錄林兩個|
|要配置的預配連接器應用數|每個子域一個應用|
|Azure AD 連接預配代理的伺服器主機|Windows 2012 R2+ 具有定位活動目錄網域控制站的視線</br>可與 Azure AD 連接服務共存|

![單雲 HR 應用租戶不交對的活動目錄林](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD 連接預配代理要求

雲 HR 應用到 Active Directory 使用者預配解決方案要求您在運行 Windows 2012 R2 或更高版本的伺服器上部署一個或多個 Azure AD Connect 預配代理。 伺服器必須至少具有 4 GB RAM 和 .NET 4.7.1+ 運行時。 確保主機伺服器具有對目標活動目錄域的網路存取權限。

要準備本地環境，Azure AD Connect 預配代理設定精靈將代理註冊到 Azure AD 租戶，[打開端口](../manage-apps/application-proxy-add-on-premises-application.md#open-ports)，[允許訪問 URL，](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)並支援[出站 HTTPS 代理配置](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)。

預配代理使用服務帳戶與活動目錄域通信。 在安裝代理之前，請在 Active Directory 使用者和電腦中創建滿足以下要求的服務帳戶：

- 不會過期的密碼
- 委派的讀取、創建、刪除和管理使用者帳戶的控制項許可權

您可以選擇應處理預配請求的網域控制站。 如果您有多個地理分佈的網域控制站，請與首選網域控制站在同一網站中安裝預配代理。 這種定位提高了端到端解決方案的可靠性和性能。

為獲得高可用性，可以部署多個 Azure AD Connect 預配代理。 註冊代理以處理同一組本地活動目錄域。

## <a name="plan-scoping-filters-and-attribute-mapping"></a>規劃範圍篩選器和屬性對應

當您啟用從雲 HR 應用到活動目錄或 Azure AD 的預配時，Azure 門戶將通過屬性對應控制屬性值。

### <a name="define-scoping-filters"></a>定義範圍篩選器

使用[範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)定義基於屬性的規則，這些規則確定應從雲 HR 應用預配到活動目錄或 Azure AD 的使用者。

啟動聯接器進程時，收集以下要求：

- 雲 HR 應用是否用於引入員工和臨時員工？
- 您是否計畫將雲 HR 應用用於 Azure AD 使用者預配來管理員工和臨時工作人員？
- 您是否計畫僅為雲 HR 應用使用者的子集向 Azure AD 使用者預配雲 HR 應用？ 例如，員工可能只有員工。

根據您的要求，在配置屬性對應時，可以設置 **"源物件範圍"** 欄位，以選擇雲 HR 應用中哪些使用者集應位於預配到活動目錄的範圍內。 有關詳細資訊，請參閱雲 HR 應用教程，瞭解常用的範圍篩選器。

### <a name="determine-matching-attributes"></a>確定匹配屬性

通過預配，您可以匹配源系統和目標系統之間的現有帳戶。 將雲 HR 應用與 Azure AD 預配服務集成時，可以[配置屬性對應](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings)以確定應從雲 HR 應用流向活動目錄或 Azure AD 的使用者資料。

啟動聯接器進程時，收集以下要求：

- 此雲 HR 應用中用於標識每個使用者的唯一 ID 是什麼？
- 從身份生命週期的角度來看，如何處理重新雇用？ 重新雇用員工保留其舊員工 ID 嗎？
- 您是否處理未來雇傭關係並提前為其創建活動目錄帳戶？
- 從身份生命週期的角度來看，您如何處理員工向臨時員工轉換，或者以其他方式？
- 轉換後的使用者是否保留其舊的活動目錄帳戶，或者他們是否獲得新的活動目錄帳戶？

根據您的要求，Azure AD 支援通過為屬性對應提供常量值或[寫入運算式，](../app-provisioning/functions-for-customizing-application-data.md)支援直接的屬性到屬性對應。 這種靈活性使您能夠最終控制目標應用屬性中填充的內容。 您可以使用 Microsoft[圖形 API](../app-provisioning/export-import-provisioning-configuration.md)和圖形資源管理器將使用者預配屬性對應和架構匯出到 JSON 檔並將其導入 Azure AD。

預設情況下，雲 HR 應用中表示唯一員工 ID 的屬性用作映射到 Active Directory*中的唯一屬性的*匹配屬性。 例如，在工作日應用方案中，**工作日****工作 ID**屬性對應到活動目錄**員工 ID**屬性。

您可以設置多個匹配屬性並分配匹配優先順序。 它們按匹配優先順序進行評估。 只要找到相符項目，便不會評估進一步比對屬性。

還可以[自訂預設屬性映射](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)，例如更改或刪除現有屬性對應。 您還可以根據業務需求創建新的屬性對應。 有關詳細資訊，請參閱雲 HR 應用教程（如[工作日](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)），瞭解要映射的自訂屬性的清單。

### <a name="determine-user-account-status"></a>確定使用者帳戶狀態

預設情況下，預配連接器應用將 HR 使用者設定檔狀態映射到 Active Directory 或 Azure AD 中的使用者帳戶狀態，以確定是啟用還是禁用使用者帳戶。

啟動"加入者-離開者"進程時，請收集以下要求。

| Process | 需求 |
| - | - |
| **聯接器** | 從身份生命週期的角度來看，如何處理重新雇用？ 重新雇用員工保留其舊員工 ID 嗎？ |
| | 您是否處理未來雇傭關係並提前為其創建活動目錄帳戶？ 這些帳戶是在啟用或禁用狀態下創建的？ |
| | 從身份生命週期的角度來看，您如何處理員工向臨時員工轉換，或者以其他方式？ |
| | 轉換後的使用者是否保留其舊的活動目錄帳戶，或者他們是否獲得新的活動目錄帳戶？ |
| **畢業生** | 在 Active Directory 中，員工和臨時工作人員的解雇處理方式是否不同？ |
| | 處理使用者終止時考慮生效日期是什麼？ |
| | 員工和臨時工作人員轉換如何影響現有的活動目錄帳戶？ |
| | 如何在活動目錄中處理"撤銷"操作？ 如果在"已加入者"流程的一部分在 Active Directory 中創建將來的日期雇用，則需要處理撤銷操作。 |

根據您的要求，可以使用[Azure AD 運算式](../app-provisioning/functions-for-customizing-application-data.md)自訂映射邏輯，以便根據資料點的組合啟用或禁用 Active Directory 帳戶。

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>將雲 HR 應用映射到活動目錄使用者屬性

每個雲 HR 應用都附帶預設雲 HR 應用到活動目錄映射。

啟動"連接者-移動器-離開者"進程時，請收集以下要求。

| Process | 需求 |
| - | - |
| **聯接器** | 活動目錄帳戶創建過程是手動、自動化還是部分自動化？ |
| | 您是否計畫將自訂屬性從雲 HR 應用傳播到活動目錄？ |
| **搬運工** | 每當雲 HR 應用中發生移動器操作時，您希望處理哪些屬性？ |
| | 在使用者更新時，您是否執行任何特定的屬性驗證？ 如果是，請提供詳細資訊。 |
| **畢業生** | 在 Active Directory 中，員工和臨時工作人員的解雇處理方式是否不同？ |
| | 處理使用者終止時考慮生效日期是什麼？ |
| | 員工和臨時工作人員轉換如何影響現有的活動目錄帳戶？ |

根據您的要求，您可以修改映射以滿足您的集成目標。 有關詳細資訊，請參閱特定的雲 HR 應用教程（如[工作日](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)），瞭解要映射的自訂屬性的清單。

### <a name="generate-a-unique-attribute-value"></a>生成唯一的屬性值

啟動聯接器進程時，在設置具有唯一約束的 CN、samAccountName 和 UPN 等屬性時，可能需要生成唯一的屬性值。

Azure AD 函數["選擇唯一值](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)"計算每個規則，然後檢查生成的值，以檢查目標系統中的唯一性。 例如，請參閱[為使用者主體名稱 （UPN） 屬性生成唯一值](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)。

> [!NOTE]
> 當前僅支援"工作日到活動目錄"使用者預配此功能。 它不能與其他預配應用一起使用。

### <a name="configure-active-directory-ou-container-assignment"></a>配置活動目錄 OU 容器分配

根據營業單位、位置和部門將 Active Directory 使用者帳戶放入容器中是一項常見要求。 啟動移動器進程時，如果監督組織發生更改，則可能需要在 Active Directory 中將使用者從一個 OU 移動到另一個 OU。

使用[Switch（）](../app-provisioning/functions-for-customizing-application-data.md#switch)函數配置 OU 分配的業務邏輯，並將其映射到活動目錄屬性**父分母名稱**。

例如，如果要根據 HR 屬性 **"市政"** 在 OU 中創建使用者，則可以使用以下運算式：

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

使用此運算式，如果市政值為達拉斯、奧斯丁、西雅圖或倫敦，則使用者帳戶將在相應的 OU 中創建。 如果沒有匹配項，則在預設 OU 中創建該帳戶。

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>計畫新使用者帳戶的密碼傳遞

啟動 Joiners 進程時，需要設置並傳遞新使用者帳戶的臨時密碼。 借助雲 HR 到 Azure AD 使用者預配，您可以在第一天為使用者推出 Azure AD[自助服務密碼重設](../authentication/quickstart-sspr.md)（SSPR） 功能。

SSPR 是 IT 管理員使使用者能夠重置密碼或解鎖帳戶的簡單方法。 您可以將**移動號碼**屬性從雲 HR 應用預配到活動目錄，並將其與 Azure AD 同步。 **移動號碼**屬性位於 Azure AD 中後，可以為使用者帳戶啟用 SSPR。 然後在第一天，新使用者可以使用已註冊和驗證的手機號碼進行身份驗證。

## <a name="plan-for-initial-cycle"></a>計畫初始週期

首次運行 Azure AD 預配服務時，它會針對雲 HR 應用執行[初始週期](../app-provisioning/how-provisioning-works.md#initial-cycle)，以創建雲 HR 應用中所有使用者物件的快照。 初始週期所佔用的時間直接取決於源系統中有多少使用者。 對於具有 100，000 多個使用者的雲 HR 應用租戶而言，初始週期可能需要很長時間。

**對於大型雲 HR 應用租戶（>30，000 個使用者），** 在逐步階段運行初始週期。 僅在驗證在 Active Directory 中為不同的使用者預配方案設置了正確的屬性後，才能啟動增量更新。 按照此處的順序操作。

1. 通過設置[範圍篩選器](#plan-scoping-filters-and-attribute-mapping)，僅為有限的使用者組運行初始週期。
2. 驗證活動目錄帳戶預配和為首次運行選定的使用者設置的屬性值。 如果結果符合您的預期，請展開範圍篩選器以逐步包括更多使用者，並驗證第二次運行的結果。

對測試使用者的初始週期的結果感到滿意後，啟動[增量更新](../app-provisioning/how-provisioning-works.md#incremental-cycles)。

## <a name="plan-testing-and-security"></a>計畫測試和安全

在部署的每個階段，從初始試驗到啟用使用者預配，確保測試結果與預期結果一樣，並審核預配週期。

### <a name="plan-testing"></a>計畫測試

將雲 HR 應用配置為 Azure AD 使用者預配後，請運行測試案例以驗證此解決方案是否符合組織的要求。

|案例|預期的結果|
|:-|:-|
|新員工在雲 HR 應用中被雇用。| - 使用者帳戶預配在活動目錄中。</br>- 使用者可以登錄到活動目錄域應用並執行所需的操作。</br>- 如果配置了 Azure AD 連接同步，則還會在 Azure AD 中創建使用者帳戶。
|使用者在雲 HR 應用中終止。|- 在活動目錄中禁用使用者帳戶。</br>- 使用者無法登錄到受活動目錄保護的任何企業應用。
|使用者監督組織在雲 HR 應用程式中更新。|根據屬性對應，使用者帳戶在活動目錄中從一個 OU 移動到另一個 OU。|
|HR 在雲 HR 應用中更新使用者的經理。|活動目錄中的經理欄位將更新以反映新經理的名稱。|
|人力資源部將員工重新雇用到新職位。|行為取決於雲 HR 應用如何配置為生成員工 ID：</br>- 如果重新雇用舊員工 ID，連接器將啟用使用者的現有 Active Directory 帳戶。</br>- 如果重新雇用獲取新的員工 ID，連接器將為使用者提供新的活動目錄帳戶。|
|人力資源部將員工轉換為合同工作人員，反之亦然。|為新角色創建新的 Active Directory 帳戶，並在轉換生效日期禁用舊帳戶。|

使用前面的結果確定如何根據已建立的時程表將自動使用者預配實現轉換為生產。

> [!TIP]
> 使用使用生產資料刷新測試環境時，使用資料縮減和資料清理等技術來刪除或遮罩敏感的個人資料，以符合隱私和安全標準。 

### <a name="plan-security"></a>規劃安全性

在部署新服務時，通常需要進行安全審查。 如果需要或尚未進行安全審核，請參閱許多 Azure AD 白皮書，這些[白皮書](https://www.microsoft.com/download/details.aspx?id=36391)提供作為服務的身份概述。

### <a name="plan-rollback"></a>計畫回滾

雲 HR 使用者預配實現可能無法在生產環境中根據需要工作。 如果是這樣，以下回滾步驟可以説明您恢復到以前的已知良好狀態。

1. 查看[預配摘要報告](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal)和[預配日誌](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)，以確定對受影響的使用者或組執行了哪些不正確的操作。 有關預配摘要報告和日誌的詳細資訊，請參閱[管理雲 HR 應用使用者預配](#manage-your-configuration)。
2. 受影響的使用者或組的最後已知良好狀態可以通過預配稽核記錄或查看目標系統（Azure AD 或活動目錄）來確定。
3. 使用應用擁有者使用最後一個已知良好的狀態值更新直接在應用中受影響的使用者或組。

## <a name="deploy-the-cloud-hr-app"></a>部署雲 HR 應用

選擇符合解決方案要求的雲 HR 應用。

**工作日**：要將工作日的工作設定檔導入活動目錄和 Azure AD，請參閱[教程：為自動使用者預配配置工作日](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment)。 或者，您可以將電子郵件地址和使用者名寫回工作日。

## <a name="manage-your-configuration"></a>管理配置

Azure AD 可通過稽核記錄和報告提供有關組織使用者預配使用方式和操作運行狀況的其他見解。

### <a name="gain-insights-from-reports-and-logs"></a>從報告和日誌中獲取見解

成功[的初始週期](../app-provisioning/how-provisioning-works.md#initial-cycle)後，Azure AD 預配服務將繼續無限期地以特定于每個應用的教程中定義的間隔無限期地運行增量更新，直到發生以下事件之一：

- 該服務已手動停止。 通過使用[Azure 門戶](https://portal.azure.com/)或相應的 Microsoft[圖形 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)命令觸發新的初始週期。
- 由於屬性對應或範圍篩選器的變化，將觸發新的初始週期。
- 由於錯誤率高，預配過程進入隔離狀態。 它在隔離中停留超過四周，此時它會自動禁用。

要查看這些事件和預配服務執行的所有其他活動，[瞭解如何查看日誌並獲取有關預配活動的報告](../app-provisioning/check-status-user-account-provisioning.md)。

#### <a name="azure-monitor-logs"></a>Azure 監視器記錄

預配服務執行的所有活動都記錄在 Azure AD 稽核記錄中。 您可以將 Azure AD 稽核記錄路由到 Azure 監視器日誌以進行進一步分析。 使用 Azure 監視器日誌（也稱為日誌分析工作區），可以查詢資料以查找事件、分析趨勢並跨各種資料來源執行相關性。 觀看此[視頻](https://youtu.be/MP5IaCTwkQg)，瞭解在實際使用者方案中使用 Azure AD 日誌的 Azure 監視器日誌的好處。

安裝[Azure AD 活動日誌的日誌分析視圖](../reports-monitoring/howto-install-use-log-analytics-views.md)，以訪問有關環境中預配事件的[預構建報告](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views)。

有關詳細資訊，請參閱如何使用 Azure[監視器日誌分析 Azure AD 活動日誌](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)。

### <a name="manage-personal-data"></a>管理個人資料

Windows 伺服器上安裝的 Azure AD Connect 預配代理在 Windows 事件日誌中創建日誌，這些日誌可能包含個人資料，具體取決於雲 HR 應用到 Active Directory 屬性對應。 為了遵守使用者隱私義務，請設置 Windows 計畫任務以清除事件日誌，並確保資料不會保留超過 48 小時。

Azure AD 預配服務不會生成報告、執行分析或提供超過 30 天的見解，因為該服務不會存儲、處理或保留任何資料超過 30 天。

### <a name="troubleshoot"></a>疑難排解

要排除預配期間可能出現的任何問題，請參閱以下文章：

- [設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題](application-provisioning-config-problem.md)
- [將屬性從本地活動目錄同步到 Azure AD 以預配到應用程式](user-provisioning-sync-attributes-for-mapping.md)
- [將使用者佈建至 Azure AD 資源庫應用程式花費數小時以上](application-provisioning-when-will-provisioning-finish.md)
- [在設定 Azure Active Directory 資源庫應用程式之使用者佈建的情況下儲存系統管理員認證時發生問題](application-provisioning-config-problem-storage-limit.md)
- [未將任何使用者佈建至 Azure AD 資源庫應用程式](application-provisioning-config-problem-no-users-provisioned.md)
- [一組錯誤的使用者佈建至 Azure AD 資源庫應用程式](application-provisioning-config-problem-wrong-users-provisioned.md)
- [設定 Windows 事件檢視器以進行代理程式疑難排解](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [設定 Azure 入口網站稽核記錄以進行服務疑難排解](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [了解 AD 使用者帳戶建立作業的記錄](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [瞭解管理器更新操作的日誌](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [解決常見的錯誤](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>後續步驟

- [為屬性對應編寫運算式](functions-for-customizing-application-data.md)
- [Azure AD 同步處理 API 概觀](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [跳過刪除超出範圍的使用者帳戶](skip-out-of-scope-deletions.md)
- [Azure AD 連接預配代理：版本發佈歷史記錄](provisioning-agent-release-version-history.md)
