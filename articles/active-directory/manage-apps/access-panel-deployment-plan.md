---
title: 規劃 Azure 活動目錄訪問面板部署
description: 有關部署 Azure 活動目錄訪問面板的指南
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d8b6c6d40aa81bf56baed59f90417f2147fa56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897068"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>規劃 Azure 活動目錄訪問面板部署

Azure 活動目錄 （Azure AD） 訪問面板是一個基於 Web 的門戶，可説明降低支援成本、提高工作效率和安全性，並減少使用者挫折感。 該系統包括詳細報告，跟蹤您訪問系統時的情況，並通知管理員濫用或濫用。

通過使用 Azure AD 訪問面板，可以：

* 發現並訪問其公司的所有 Azure AD 連接資源，如應用程式
* 請求訪問新應用和組
* 為他人管理對這些資源的訪問
* 管理自助服務密碼重設和 Azure 多重要素驗證設置
* 管理其設備

它還允許管理員管理：

* 服務條款
* 組織
* 存取權檢閱


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Azure AD 訪問面板集成的優勢

Azure AD 訪問面板通過以下方式為企業帶來好處：

**提供直觀的使用者體驗**：訪問面板為所有 Azure 單一登入 （SSO） 連接的應用程式提供一個平臺。 您有一個統一的門戶來查找現有設置和新功能，如組管理和自助服務密碼重設，因為它們被添加。 直觀的體驗允許使用者更快地恢復工作並提高工作效率，同時減少他們的挫折感。

**提高工作效率**：訪問面板中的所有使用者應用程式都啟用了 SSO。 跨企業應用程式和 Office 365 啟用 SSO 可減少或消除其他登錄提示，從而創建卓越的登錄體驗。 訪問面板使用自助服務和動態成員資格，並提高了您的身份系統的整體安全性。 它通過確保適當的人員管理對應用程式的存取權限來做到這一點。 訪問面板是一個連貫的登錄頁，可供您快速查找資源並繼續工作任務。

**管理成本**：使用 Azure AD 啟用訪問面板有助於剝離本地基礎結構。 它通過為您提供一致的門戶來查找所有應用、請求訪問資源和管理帳戶，從而降低了支援成本。

**提高靈活性和安全性**：訪問面板使您能夠訪問雲平臺提供的安全性和靈活性。 管理員可以輕鬆地將設置更改為應用程式和資源，並可以容納新的安全要求，而不會影響使用者。

**支援可靠的審核和使用跟蹤**：所有使用者功能的審核和使用跟蹤可讓您瞭解使用者何時使用其資源，並確保您可以評估安全性。

### <a name="licensing-considerations"></a>授權考量

訪問面板是免費的，無需在基本級別使用許可證。 但是，目錄中的物件數和要部署的其他功能可能需要其他許可證。 一些具有許可要求的常見 Azure AD 方案包括以下安全功能：

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [基於組的成員資格](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [自助服務密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

請參閱[Azure AD 的完整許可指南](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>部署 Azure AD 訪問面板的先決條件

在開始此專案之前完成以下先決條件：

* [集成應用程式 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [管理 Azure AD 使用者和組基礎結構](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>規劃 Azure AD 訪問面板部署

下表概述了訪問面板部署的關鍵用例：

| 區域| 描述 |
| - | - |
| 存取| 訪問面板門戶可從公司網路中的公司和個人設備訪問。 |
|存取 | 訪問面板門戶可從公司網路外部的公司設備訪問。 |
| 稽核| 使用資料至少每 29 天下載一次到公司系統中。 |
| 控管| 定義和監視使用者分配到 Azure AD 連接的應用程式和組的生命週期。 |
| 安全性| 通過使用者和組分配控制對資源的訪問。 只有經過授權的使用者可以管理資源訪問。 |
| 效能| 記錄和監視訪問分配傳播時間表。 |
| 使用者經驗| 使用者知道訪問面板功能以及如何使用它們。|
| 使用者經驗| 使用者可以管理他們對應用程式和組的訪問。|
| 使用者經驗| 使用者可以管理其帳戶。 |
| 使用者經驗| 使用者知道瀏覽器相容性。 |
| 支援| 使用者可以找到對訪問面板問題的支援。 |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>部署 Azure AD 訪問面板的最佳做法

可以逐步啟用訪問面板的功能。 我們建議以下部署順序：

1. 我的應用程式
   * 應用程式啟動器
   * 自助服務應用管理
   * 微軟 Office 365 集成

1. 自助服務應用發現
   * 自助式密碼重設
   * 多重要素驗證設置
   * 裝置管理
   * 使用規定
   * 管理組織

1. 我的群組
   * 自助式群組管理
1. 存取權檢閱
   * 訪問審核管理

從"我的應用"開始，將使用者引入門戶，作為訪問資源的常見位置。 自助應用程式發現的附加基於"我的應用"體驗。 我的組和訪問評審基於自助服務功能。

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>規劃 Azure AD 訪問面板的配置

下表列出了幾個重要的訪問面板配置以及您可以使用的典型值：

| 組態| 典型值 |
| - | - |
| 確定試點組| 確定要使用的 Azure AD 安全性群組，並確保所有試點成員都是該組的一部分。 |
| 確定要為生產啟用的組或組。| 標識要使用的 Azure AD 安全性群組或同步到 Azure AD 的活動目錄組。 確保所有試點成員都是小組的一部分。 |
| 允許使用者對某些類型的應用程式使用 SSO| 聯合 SSO、 OAuth、 密碼 SSO、 應用代理 |
| 允許使用者使用自助服務密碼重設 | 是 |
| 允許使用者使用多重要素驗證| 是 |
| 允許使用者對某些類型的組使用自助服務組管理| 安全性群組，Office 365 組 |
| 允許使用者使用自助服務應用管理| 是 |
| 允許使用者使用訪問評論| 是 |

### <a name="plan-consent-strategy"></a>計畫同意策略

使用者或管理員必須同意任何應用程式的使用條款和隱私政策。 如果可能，如果給定您的商務規則，請使用管理員同意，這為使用者提供了更好的體驗。

要使用管理員同意，您必須是組織的全域管理員，並且應用程式必須是：

* 在您的組織中註冊

* 在另一個 Azure AD 組織中註冊，以前至少由一個使用者同意

有關詳細資訊，請參閱[配置最終使用者同意在 Azure 活動目錄中的應用程式的方式](configure-user-consent.md)。

### <a name="engage-the-right-stakeholders"></a>讓合適的利益相關者參與進來

當技術專案失敗時，它們通常是由於對影響、結果和責任的期望不匹配而達到這一要求。 為了避免這些陷阱，[請確保您與合適的利益相關者接洽](../fundamentals/active-directory-deployment-plans.md)，並確保專案中的利益相關者角色得到充分理解。

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功都至關重要。 主動告知使用者他們的體驗將如何改變以及何時變化，以及如何在需要時獲得支援。

儘管訪問面板通常不會造成使用者問題，但請務必做好準備。 在啟動前為您的支援人員創建指南和所有資源的清單。

#### <a name="communications-templates"></a>通信範本

Microsoft 為訪問面板的電子郵件[和其他通信提供了可自訂的範本](https://aka.ms/APTemplates)。 您可以調整這些資產，以便根據企業文化在其它通信管道中使用。

## <a name="plan-your-sso-configuration"></a>規劃 SSO 配置

當使用者登錄到應用程式時，他們將經歷身份驗證過程，並且需要證明他們是誰。 如果沒有 SSO，密碼存儲在應用程式中，並且使用者必須知道此密碼。 使用 SSO，使用者的憑據將傳遞到應用程式，因此他們不需要為每個應用程式重新輸入密碼。

要在"我的應用"中啟動應用程式，必須啟用 SSO。

Azure AD 支援三種不同的方法在[應用程式中啟用單一登入](what-is-single-sign-on.md)：

* **同盟單一登入** 
    * 使應用程式能夠重定向到 Azure AD 進行使用者身份驗證，而不是提示輸入密碼。 
    * 支援使用協定的應用程式，如 SAML 2.0、WS-聯合或 OpenID 連接，並且是單一登入最豐富的模式。

* **基於密碼的單登錄** 
    * 通過使用 Web 瀏覽器延伸或移動應用程式，實現安全的應用程式密碼存儲和重播。 
    * 利用應用程式提供的現有登錄過程，但允許管理員管理密碼。 使用者不需要知道密碼。

* **現有單登錄** 
    * 使 Azure AD 能夠利用為應用程式佈建的任何現有單一登入。
    * 使這些應用程式能夠連結到 Office 365 或 Azure AD 訪問面板門戶。 
    * 在 Azure AD 中啟用其他報告，當應用程式啟動到那裡時。 
    * 包括使用 Azure 應用程式代理和連結的單點登錄模式。

瞭解如何在此處配置應用程式的 SSO 模式：[對 Azure 活動目錄中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

為了獲得最佳使用"我的應用"頁面的體驗，從集成可用於聯合 SSO 的雲應用程式開始。 聯合 SSO 允許使用者在其應用啟動表面上獲得一致的一鍵式體驗，並且在配置控制方面往往更加強大。

當應用程式支援 Azure AD 時，將聯合 SSO 與 Azure AD（OpenID 連接/SAML）一起使用，而不是使用基於密碼的 SSO 和 ADFS。

有關如何部署和配置 SaaS 應用程式的詳細資訊，請參閱[SaaS SSO 部署計畫](https://aka.ms/deploymentplans/sso)。

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>計畫部署"我的應用"瀏覽器延伸

當使用者登錄到基於密碼的 SSO 應用程式時，他們需要安裝和使用"我的應用"安全登錄擴展。 擴展執行將密碼傳輸到應用程式的登錄表單的腳本。 當使用者初次開機基於密碼的 SSO 應用程式時，系統會提示他們安裝擴展。 有關擴展的詳細資訊，請參閱有關[安裝訪問面板瀏覽器延伸的](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)本文檔。

如果必須集成基於密碼的 SSO 應用程式，則應定義一種機制，以便使用[受支援的瀏覽器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)大規模部署擴展。 選項包括：

* [Internet 資源管理器的群組原則](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [互聯網資源管理器的組態管理員](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Chrome、火狐、微軟邊緣或 IE 的使用者驅動的下載和配置](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

瞭解更多：[如何配置密碼單一登入](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)。

不使用基於密碼的 SSO 應用程式的使用者也會從擴展中受益。 這些好處包括能夠從搜索欄啟動任何應用、查找對最近使用的應用程式的存取權限以及指向"我的應用"頁面的連結。

以下是使用者初次開機基於密碼的 SSO 應用程式時將看到的內容：

!["我的應用"瀏覽器延伸安裝螢幕的螢幕截圖 ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>規劃移動訪問

對於啟動基於密碼的 SSO 應用程式移動使用者，需要使用 Intune 策略（Microsoft 邊緣或 Intune 託管瀏覽器）保護的瀏覽器。 受策略保護的瀏覽器允許傳輸為應用程式保存的密碼。 Microsoft Edge 或託管瀏覽器提供了一組 Web 資料保護功能。 您還可以在 iOS 和 Android 設備上將 Microsoft Edge 用於企業方案。 Microsoft Edge 支援與 Intune 託管瀏覽器相同的管理方案，並改善了使用者體驗。 瞭解更多：[使用 Microsoft Intune 策略保護瀏覽器管理 Web 訪問](https://docs.microsoft.com/intune/app-configuration-managed-browser)。

## <a name="plan-your-my-apps-deployment"></a>規劃"我的應用部署"

訪問面板的基礎是應用程式啟動器"我的應用程式"，使用者在 中[https://myapps.microsoft.com](https://myapps.microsoft.com/)訪問它。 "我的應用"頁面為使用者提供了一個開始工作並訪問其必要應用程式的位置。 在這裡，使用者找到他們具有單一登入存取權限的所有應用程式的清單。 

![應用面板的螢幕截圖](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

當使用者使用 Office 365 門戶時，Office 365 應用啟動器中將顯示相同的應用程式。

計畫將應用程式添加到"我的應用"啟動器的順序，並決定是逐步推出還是一次性推出所有應用程式。 為此，請創建一個應用程式清單，列出每個應用程式的身份驗證類型和任何現有的 SSO 集成。

#### <a name="add-applications-to-the-my-apps-panel"></a>將應用程式添加到"我的應用"面板

任何啟用 Azure AD SSO 的應用程式都可以添加到"我的應用"啟動器中。 其他應用程式通過使用連結 SSO 選項添加。 您可以配置連結到現有 Web 應用程式 URL 的應用程式磁貼。 連結的 SSO 允許您開始將使用者定向到"我的應用"門戶，而無需將所有應用程式遷移到 Azure AD SSO。 您可以逐漸遷移到 Azure AD SSO 配置的應用程式，而不會中斷使用者體驗。

#### <a name="use-my-apps-collections"></a>使用"我的應用"集合

預設情況下，所有應用程式都一起列在一頁上。 但是，您可以使用集合將相關應用程式組合在一起，並在單獨的選項卡上顯示它們，從而更易於查找它們。 例如，可以使用集合為特定作業角色、任務、專案等創建應用程式的邏輯分組。 有關詳細資訊，請參閱[如何使用"我的應用"集合自訂使用者訪問面板](access-panel-collections.md)。 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>計畫是使用"我的應用"還是現有門戶

您的使用者可能已經擁有"我的應用"以外的應用程式或閘戶。 如果是這樣，則決定是同時支援兩個門戶還是僅使用一個門戶。

如果現有門戶已用作使用者的起點，則可以使用使用者訪問 URL 集成"我的應用"功能。 使用者訪問 URL 功能是指向"我的應用"門戶中可用的應用程式的直接連結。 這些 URL 可以嵌入到任何現有網站中。 當使用者選擇該連結時，它會從"我的應用"門戶打開應用程式。

您可以在 Azure 門戶中的應用程式 **"屬性"** 區域中找到使用者訪問 URL 屬性。

![應用面板的螢幕截圖](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>規劃自助服務應用程式發現和訪問

將一組核心應用程式部署到使用者的"我的應用"頁後，應啟用自助服務應用管理功能。 自助服務應用發現使使用者能夠：

* 查找要添加到其"我的應用"的新應用。 
* 添加他們可能不知道在設置期間需要的可選應用。

審批工作流可用於顯式審批以訪問應用程式。 當有掛起的存取權限請求時，核准者將在"我的應用"門戶中接收通知。

## <a name="plan-self-service-group-membership"></a>計畫自助服務組成員身份 

您可以允許使用者在 Azure AD 中創建和管理自己的安全性群組或 Office 365 組。 組的擁有者可以批准或拒絕成員身份請求和委派組成員身份的控制。 自助服務組管理功能不適用於啟用郵件的安全性群組或通訊群組清單。

要規劃自助服務組成員身份，請確定是否允許組織中的所有使用者創建和管理組或僅允許使用者子集。 如果您允許使用者子集，則需要設置一個組，將這些人添加到其中。 有關啟用這些方案的詳細資訊，請參閱[在 Azure 活動目錄中設置自助服務組管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)。

## <a name="plan-reporting-and-auditing"></a>計畫報告和審計

Azure AD 提供[提供技術和業務見解的報告](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)。 與您的業務和技術應用程式擁有者合作，接管這些報告的擁有權，並定期使用它們。 下表提供了典型報告方案的一些示例。

|   | 管理風險| 提高生產率| 治理和合規性 |
|  - |- | - | - |
| 報表類型|  應用程式許可權和使用方式| 帳戶佈建活動| 查看訪問應用程式的人員 |
| 潛在行動| 審計訪問;撤銷許可權| 修復任何預配錯誤| [撤銷存取權] |

Azure AD 將大多數審核資料保留 30 天。 資料可通過 Azure 監管中心或 API 獲取，可供下載到分析系統中。

#### <a name="auditing"></a>稽核

應用程式訪問的稽核記錄提供 30 天。 如果企業內的安全審核需要更長的保留時間，則需要將日誌匯出到安全資訊事件和管理 （SIEM） 工具中，如 Splunk 或 ArcSight。

對於審核、報告和災害復原備份，請記錄所需的下載頻率、目標系統是什麼以及誰負責管理每個備份。 您可能不需要單獨的審核和報告備份。 災害復原備份應該是一個單獨的實體。

## <a name="deploy-applications-to-users-my-apps-panel"></a>將應用程式部署到使用者的"我的應用"面板

為 SSO 配置應用程式後，將分配組存取權限。 分配的組中的使用者將有權訪問，並且將在其"我的應用"和 Office 365 應用啟動器中看到該應用程式。

請參閱[將使用者和組分配給活動目錄中的應用程式](methods-for-assigning-users-and-groups.md)。

如果在測試或部署期間要添加組，但尚未允許應用程式顯示在"我的應用"中，請參閱在 Azure[活動目錄中從使用者體驗中隱藏應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>將 Microsoft Office 365 應用程式部署到我的應用程式

對於 Office 365 應用程式，使用者會根據分配給他們的許可證接收 Office 的副本。 訪問 Office 應用程式的先決條件是為使用者分配與 Office 應用程式綁定的正確許可證。 當您為使用者分配許可證時，他們將自動在其"我的應用"頁面和 Office 365 應用啟動器中看到與許可證關聯的應用程式。

如果要向使用者隱藏一組 Office 應用程式，可以選擇從"我的應用"門戶隱藏應用，同時仍允許從 Office 365 門戶進行訪問。 在應用程式的"使用者設置"部分找到這些設置。 瞭解更多：[在 Azure 活動目錄中從使用者體驗中隱藏應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。

![配置如何隱藏應用程式的螢幕截圖](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>部署應用程式自助服務功能

自助服務應用程式訪問允許使用者自行發現和請求對應用程式的訪問。 使用者可以自由地訪問他們需要的應用，而無需每次通過 IT 組請求訪問。 當使用者請求訪問並經應用擁有者自動或手動批准時，它們將添加到後端的組中。 啟用了對誰請求、批准或刪除存取權限的報告，並使您能夠控制管理分配的角色。

您可以將應用程式訪問請求的審批委託給業務審批人。 業務審批人可以從業務審批人"我的應用"版面設定應用訪問密碼。

瞭解更多：[如何使用自助服務應用程式訪問](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)。

![配置自助服務應用程式管理的螢幕截圖](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>驗證部署

確保訪問面板部署經過全面測試，並實施回滾計畫。

以下測試應同時使用公司擁有的設備和個人設備進行。 這些測試案例還應反映您的業務用例。 以下是根據本文檔中的示例業務需求和典型技術方案進行的幾個案例。 添加特定于您需求的其他元件。

#### <a name="application-sso-access-test-case-examples"></a>應用程式 SSO 訪問測試案例示例：


| 商業案例| 預期的結果 |
| - | -|
| 使用者登錄"我的應用"門戶| 使用者可以登錄並查看其應用程式 |
| 使用者啟動聯合 SSO 應用程式| 使用者自動登入到應用程式 |
| 使用者初次開機密碼 SSO 應用程式| 使用者需要安裝"我的應用"擴展 |
| 使用者隨後啟動密碼 SSO 應用程式| 使用者自動登入到應用程式 |
| 使用者從 Office 365 門戶啟動應用| 使用者自動登入到應用程式 |
| 使用者從託管瀏覽器啟動應用| 使用者自動登入到應用程式 |


#### <a name="application-self-service-capabilities-test-case-examples"></a>應用程式自助服務功能測試用例示例


| 商業案例| 預期的結果 |
| - | - |
| 使用者可以管理應用程式的成員資格| 使用者可以添加/刪除有權訪問應用的成員 |
| 使用者可以編輯應用程式| 使用者可以編輯密碼 SSO 應用程式的應用程式描述和憑據 |

### <a name="rollback-steps"></a>復原步驟

請務必規劃無法如預期部署時的因應措施。 如果 SSO 配置在部署期間失敗，則必須瞭解如何[解決 SSO 問題](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso)並減少對使用者的影響。 在極端情況下，您可能需要[回滾 SSO。](../manage-apps/plan-sso-deployment.md)


## <a name="manage-your-implementation"></a>管理您的實現

應使用特權最低的角色來完成 Azure 活動目錄中所需的任務。 [查看可用的不同角色，](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)並選擇正確的角色來解決此應用程式每個角色的需求。 某些角色可能需要臨時應用並在部署完成後刪除。

| 角色| 角色| Azure AD 角色  |
| - | -| -|
| 説明台管理員| 第 1 層支援| None |
| 身份管理員| 當問題影響 Azure AD 時配置和調試| 全域管理員 |
| 應用程式管理員| 使用者在應用程式中的證明，對具有許可權的使用者的配置| None |
| 基礎架構管理員| 證書滾存擁有者| 全域管理員 |
| 企業主/利益相關者| 使用者在應用程式中的證明，對具有許可權的使用者的配置| None |

您可以使用[特權標識管理](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)來管理角色，為具有目錄許可權的使用者提供其他審核、控制和訪問審核。

### <a name="troubleshoot-access-panel-issues"></a>排除訪問面板問題

為具有常見方案的支援組織創建故障排除指南，這些方案將指向其解決方案中的 Microsoft 文檔。 您可能希望創建將支援分解為組織使用的層的指南。

請參閱以下故障排除指南，瞭解以下參考：

[未顯示應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[出現意外應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[使用者無法登錄到訪問面板](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[使用自助服務應用程式訪問的問題](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[瀏覽器延伸的問題](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>後續步驟

[規劃 Azure 多重要素驗證的部署](https://aka.ms/deploymentplans/mfa)
