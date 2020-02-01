---
title: 規劃 Azure Active Directory 存取面板部署
description: 部署 Azure Active Directory 存取面板的指引
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897068"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>規劃 Azure Active Directory 存取面板部署

Azure Active Directory （Azure AD）存取面板是以 web 為基礎的入口網站，可協助降低支援成本、提高生產力和安全性，並減少使用者挫折。 系統包含詳細的報告，會在您存取系統時進行追蹤，並通知系統管理員誤用或濫用。

藉由使用 Azure AD 存取面板，您可以：

* 探索並存取其公司的所有 Azure AD 連線資源，例如應用程式
* 要求存取新的應用程式和群組
* 為其他人管理這些資源的存取權
* 管理自助式密碼重設和 Azure 多重要素驗證設定
* 管理其裝置

它也可讓系統管理員管理：

* 服務條款
* 各
* 存取檢閱


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Azure AD 存取面板整合的優點

Azure AD 存取面板以下列方式獲益企業：

**提供直覺的使用者體驗**：存取面板為您的所有 Azure 單一登入（SSO）連線應用程式提供單一平臺。 您有一個整合的入口網站，可在新增時尋找現有設定和新功能，例如群組管理和自助式密碼重設。 直覺的體驗可讓使用者更快速地恢復工作，並提高生產力，同時減少他們的挫折。

**提高生產力**：存取面板中的所有使用者應用程式都已啟用 SSO。 跨企業應用程式和 Office 365 啟用 SSO，藉由減少或排除額外的登入提示，來建立更優異的登入體驗。 存取面板會使用自助和動態成員資格，並改善您的身分識別系統的整體安全性。 這會藉由確保適當人員管理應用程式的存取權來完成這項工作。 存取面板可作為一致的登陸頁面，讓您快速尋找資源並繼續工作。

**管理成本**：透過 Azure AD 啟用存取面板有助於 divestment 內部部署基礎結構。 它藉由提供一致的入口網站來尋找您的所有應用程式、要求資源的存取權，以及管理帳戶，以降低支援成本。

**增加彈性和安全性**：存取面板可讓您存取雲端平臺所提供的安全性和彈性。 系統管理員可以輕鬆地將設定變更為應用程式和資源，並可容納新的安全性需求，而不會影響使用者。

**啟用健全的審核和使用方式追蹤**：所有使用者功能的審核和使用方式追蹤可讓您知道使用者何時使用其資源，並確保您可以評估安全性。

### <a name="licensing-considerations"></a>授權考量

存取面板是免費的，而且不需要在基本層級使用任何授權。 不過，目錄中的物件數目和您想要部署的其他功能可能需要額外的授權。 某些具有授權需求的常見 Azure AD 案例包括下列安全性功能：

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [以群組為基礎的成員資格](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

如需 Azure AD，請參閱[完整授權指南](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>部署 Azure AD 存取面板的必要條件

開始此專案之前，請先完成下列必要條件：

* [整合應用程式 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [管理 Azure AD 使用者和群組基礎結構](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>規劃 Azure AD 存取面板部署

下表概述存取面板部署的主要使用案例：

| 區域| 說明 |
| - | - |
| 存取| 您可以從公司網路內的公司和個人裝置存取存取面板入口網站。 |
|存取 | 存取面板入口網站可從公司網路外部的公司裝置存取。 |
| 稽核| 使用量資料會至少每隔29天下載至公司系統。 |
| 控管| 定義和監視 Azure AD 連線應用程式和群組的使用者指派生命週期。 |
| 安全性| 資源的存取權是透過使用者和群組指派來控制。 只有授權的使用者可以管理資源存取權。 |
| 效能| 已記載並監視存取指派傳播時間軸。 |
| 使用者體驗| 使用者知道存取面板功能以及如何使用它們。|
| 使用者體驗| 使用者可以管理其對應用程式和群組的存取權。|
| 使用者體驗| 使用者可以管理自己的帳戶。 |
| 使用者體驗| 使用者瞭解瀏覽器的相容性。 |
| 支援| 使用者可以尋找存取面板問題的支援。 |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>部署 Azure AD 存取面板的最佳做法

存取面板的功能可以逐步啟用。 我們建議採用下列部署順序：

1. 我的應用程式
   * 應用程式啟動器
   * 自助應用程式管理
   * Microsoft Office 365 整合

1. 自助應用程式探索
   * 自助式密碼重設
   * 多重要素驗證設定
   * 裝置管理
   * 使用規定
   * 管理組織

1. 我的群組
   * 自助式群組管理
1. 存取檢閱
   * 存取審查管理

從開始，我的應用程式會將使用者引進入口網站，做為存取資源的常見位置。 新增自助式應用程式探索是以我的應用程式的經驗為基礎。 我的群組和存取權審查是以自助功能為依據。

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Azure AD 存取面板的規劃設定

下表列出數個重要的存取面板設定和您可能會使用的一般值：

| 組態| 一般值 |
| - | - |
| 判斷試驗群組| 識別要使用的 Azure AD 安全性群組，並確保所有試驗成員都是群組的一部分。 |
| 決定要針對生產環境啟用的群組。| 識別要使用的 Azure AD 安全性群組或已同步處理至 Azure AD 的 Active Directory 群組。 請確定所有試驗成員都是群組的一部分。 |
| 允許使用者對特定類型的應用程式使用 SSO| 同盟 SSO，OAuth，密碼 SSO，應用程式 Proxy |
| 允許使用者使用自助密碼重設 | 是 |
| 允許使用者使用多重要素驗證| 是 |
| 允許使用者針對特定類型的群組使用自助式群組管理| 安全性群組，Office 365 群組 |
| 允許使用者使用自助應用程式管理| 是 |
| 允許使用者使用存取權審查| 是 |

### <a name="plan-consent-strategy"></a>規劃同意策略

使用者或系統管理員必須同意任何應用程式的使用規定和隱私權原則。 可能的話，假設您的商務規則，請使用系統管理員同意，讓使用者獲得更好的體驗。

若要使用系統管理員同意，您必須是組織的全域管理員，而且應用程式必須是下列其中一項：

* 已在您的組織中註冊

* 已在另一個 Azure AD 組織中註冊，且先前至少由一位使用者同意

如需詳細資訊，請參閱[在 Azure Active Directory 中設定使用者同意應用程式的方式](configure-user-consent.md)。

### <a name="engage-the-right-stakeholders"></a>參與適當的專案關係人

當技術專案失敗時，通常是因為對影響、結果和責任的預期不相符所造成。 若要避免這些錯誤，請[確定您正在吸引正確的專案關係人](../fundamentals/active-directory-deployment-plans.md)，而且已充分瞭解專案中的專案關係人角色。

### <a name="plan-communications"></a>規劃通訊

通訊對於任何新服務的成功非常重要。 主動通知使用者其經驗和時機，以及如何在需要時取得支援。

雖然存取面板通常不會造成使用者的問題，但請務必準備。 開始之前，請先建立輔助支援人員的指南和所有資源的清單。

#### <a name="communications-templates"></a>通訊範本

Microsoft 為存取面板的[電子郵件和其他通訊提供可自訂的範本](https://aka.ms/APTemplates)。 您可以根據您的公司文化特性，調整這些資產以用於其他通道。

## <a name="plan-your-sso-configuration"></a>規劃您的 SSO 設定

當使用者登入應用程式時，他們會通過驗證程式，而且必須證明他們的身分。 如果沒有 SSO，密碼會儲存在應用程式中，而使用者必須知道此密碼。 使用 SSO 時，會將使用者的認證傳遞給應用程式，因此他們不需要重新輸入每個應用程式的密碼。

若要在我的應用程式中啟動應用程式，必須啟用 SSO。

Azure AD 支援三種不同的方式，可[在應用程式中啟用單一登入](what-is-single-sign-on.md)：

* **同盟單一登入** 
    * 可讓應用程式重新導向至 Azure AD 進行使用者驗證，而不是提示輸入密碼。 
    * 支援使用通訊協定的應用程式，例如 SAML 2.0、WS-同盟或 OpenID Connect，而且是單一登入的最豐富模式。

* **以密碼為基礎的單一登入** 
    * 使用網頁瀏覽器延伸模組或行動應用程式，啟用安全的應用程式密碼儲存和重新執行。 
    * 利用應用程式提供的現有登入程式，但可讓系統管理員管理密碼。 使用者不需要知道密碼。

* **現有的單一登入** 
    * 讓 Azure AD 能夠利用已針對應用程式設定的任何現有單一登入。
    * 可讓這些應用程式連結至 Office 365 或 Azure AD 存取面板入口網站。 
    * 當應用程式啟動時，會在 Azure AD 中啟用額外的報告。 
    * 包含使用 Azure 應用程式 Proxy 和已連結的單一登入模式。

在這裡瞭解如何設定應用程式的 SSO 模式： [Azure Active Directory 中的應用程式的單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

如需我的應用程式頁面的最佳體驗，請從可用於同盟 SSO 的雲端應用程式整合開始。 同盟 SSO 可讓使用者在其應用程式啟動介面之間擁有一致的單鍵體驗，並傾向于設定控制中更健全。

當應用程式支援時，使用同盟 SSO 搭配 Azure AD （OpenID Connect/SAML），而不是以密碼為基礎的 SSO 和 ADFS。

如需如何部署及設定 SaaS 應用程式的詳細資訊，請參閱[SAAS SSO 部署計畫](https://aka.ms/deploymentplans/sso)。

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>規劃部署我的應用程式的瀏覽器擴充功能

當使用者登入密碼 SSO 應用程式時，他們必須安裝並使用我的應用程式安全登入延伸模組。 延伸模組會執行腳本，將密碼傳送至應用程式的登入表單。 使用者第一次啟動密碼型 SSO 應用程式時，系統會提示他們安裝延伸模組。 如需擴充功能的詳細資訊，請參閱[安裝存取面板瀏覽器擴充](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)功能的檔。

如果您必須整合密碼型 SSO 應用程式，您應該定義使用[支援的瀏覽器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)大規模部署擴充功能的機制。 選項包括：

* [Internet Explorer 的群組原則](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Internet Explorer 的 Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [適用于 Chrome、Firefox、Microsoft Edge 或 IE 的使用者導向下載和設定](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

深入瞭解：[如何設定密碼單一登入](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)。

不使用密碼 SSO 應用程式的使用者也可受益于此延伸模組。 這些優點包括從其搜尋列啟動任何應用程式、尋找最近使用之應用程式的存取權，以及具有我的應用程式頁面連結的功能。

以下是第一次啟動密碼型 SSO 應用程式時，使用者會看到的內容：

![我的應用程式瀏覽器延伸模組安裝畫面的螢幕擷取畫面 ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>規劃行動存取

以 Intune 原則（Microsoft Edge 或 Intune Managed Browser）保護的瀏覽器對於啟動密碼型 SSO 應用程式的行動使用者而言是必要的。 受原則保護的瀏覽器可讓您傳送為應用程式所儲存的密碼。 Microsoft Edge 或受管理的瀏覽器提供一組 web 資料保護功能。 您也可以在 iOS 和 Android 裝置上使用 Microsoft Edge 進行企業案例。 Microsoft Edge 支援與 Intune Managed Browser 相同的管理案例，並改善使用者體驗。 深入瞭解：[使用 Microsoft Intune 受原則保護的瀏覽器來管理 web 存取](https://docs.microsoft.com/intune/app-configuration-managed-browser)。

## <a name="plan-your-my-apps-deployment"></a>規劃您的我的應用程式部署

存取面板的基礎是應用程式啟動器我的應用程式，使用者可在[https://myapps.microsoft.com](https://myapps.microsoft.com/)存取。 我的應用程式頁面可讓使用者從單一位置開始工作，並取得其所需的應用程式。 在這裡，使用者會找到他們具有單一登入存取權的所有應用程式清單。 

![[應用程式] 面板的螢幕擷取畫面](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

當使用者使用 Office 365 入口網站時，相同的應用程式將會顯示在 Office 365 應用程式啟動器中。

規劃您要將應用程式新增至我的應用程式啟動器的順序，並決定是要一次或全部逐一地推出。 若要這麼做，請建立應用程式清查，列出每個應用程式的驗證類型和任何現有的 SSO 整合。

#### <a name="add-applications-to-the-my-apps-panel"></a>將應用程式新增至我的應用程式面板

任何 Azure AD SSO 功能的應用程式都可以新增至我的應用程式啟動器。 其他應用程式則是使用連結的 SSO 選項來新增。 您可以設定應用程式磚，以連結至現有 web 應用程式的 URL。 連結的 SSO 可讓您開始將使用者導向我的應用程式入口網站，而不需要將所有應用程式遷移至 Azure AD SSO。 您可以逐漸移至 Azure AD SSO 設定的應用程式，而不會中斷使用者的體驗。

#### <a name="use-my-apps-collections"></a>使用我的應用程式集合

根據預設，所有應用程式會同時列在單一頁面上。 但是，您可以使用集合將相關的應用程式群組在一起，並將它們顯示在不同的索引標籤上，使其更容易尋找。 例如，您可以使用集合，針對特定的作業角色、工作、專案等，建立應用程式的邏輯群組。 如需相關資訊，請參閱[如何使用我的應用程式集合自訂使用者存取面板](access-panel-collections.md)。 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>規劃是否要使用我的應用程式或現有的入口網站

您的使用者可能已經有我的應用程式以外的應用程式或入口網站。 若是如此，請決定是否同時支援這兩個入口網站，或僅使用其中一個。

如果現有的入口網站已用來作為使用者的起點，您可以使用使用者存取 Url 來整合我的應用程式功能。 使用者存取 Url 可作為我的應用程式入口網站中可用應用程式的直接連結。 這些 Url 可以內嵌在任何現有的網站中。 當使用者選取連結時，它會從我的應用程式入口網站開啟應用程式。

在 Azure 入口網站中，您可以在應用程式的 [**屬性**] 區域中找到 [使用者存取 URL] 屬性。

![[應用程式] 面板的螢幕擷取畫面](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>規劃自助應用程式探索和存取

將一組核心應用程式部署至使用者的我的應用程式頁面之後，您應該啟用自助式應用程式管理功能。 自助應用程式探索可讓使用者：

* 尋找要新增至其我的應用程式的新應用程式。 
* 新增在安裝期間可能不知道其所需的選用應用程式。

核准工作流程可供明確核准存取應用程式。 當有擱置的應用程式存取要求時，核准者的使用者將會在我的應用程式入口網站中收到通知。

## <a name="plan-self-service-group-membership"></a>規劃自助群組成員資格 

您可以讓使用者在 Azure AD 中建立及管理自己的安全性群組或 Office 365 群組。 群組的擁有者可以核准或拒絕成員資格要求，以及委派群組成員資格的控制權。 自助群組管理功能不適用於擁有郵件功能的安全性群組或通訊群組清單。

若要規劃自助群組成員資格，請判斷您是否允許組織中的所有使用者建立及管理群組，或只是使用者的子集。 如果您允許一部分的使用者，則必須設定要新增這些人員的群組。 如需有關啟用這些案例的詳細資訊，請參閱[在 Azure Active Directory 中設定自助式群組管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)。

## <a name="plan-reporting-and-auditing"></a>規劃報告和審核

Azure AD 提供可提供[技術和商務深入解析的報表](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)。 請與您的商務和技術應用程式擁有者合作，以取得這些報表的擁有權，並定期使用它們。 下表提供一般報告案例的一些範例。

|   | 管理風險| 提高生產力| 治理和合規性 |
|  - |- | - | - |
| 報表類型|  應用程式許可權和使用方式| 帳戶佈建活動| 審查誰正在存取應用程式 |
| 可能的動作| Audit access;撤銷許可權| 補救任何布建錯誤| 撤銷存取權 |

Azure AD 會將大部分的審核資料保留30天。 資料可透過 Azure 系統管理員入口網站或 API 取得，供您下載至您的分析系統。

#### <a name="auditing"></a>稽核

應用程式存取的審核記錄可供使用30天。 如果您企業內的安全性審核需要較長的保留期，則必須將記錄匯出到安全性資訊事件和管理（SIEM）工具，例如 Splunk 或 ArcSight。

若要進行審核、報告和嚴重損壞修復備份，請記錄所需的下載頻率、目標系統為何，以及負責管理每個備份的人員。 您可能不需要個別的審核和報告備份。 您的嚴重損壞修復備份應該是個別的實體。

## <a name="deploy-applications-to-users-my-apps-panel"></a>將應用程式部署至使用者的我的應用程式面板

針對 SSO 設定應用程式之後，系統會將存取權指派給群組。 指派群組中的使用者將擁有存取權，並會在其我的應用程式和 Office 365 應用程式啟動器中看到應用程式。

請參閱[在 Active Directory 中將使用者和群組指派給應用程式](methods-for-assigning-users-and-groups.md)。

如果您在測試或部署期間想要新增群組，但尚未允許應用程式顯示在我的應用程式中，請參閱[在 Azure Active Directory 中隱藏應用程式的使用者體驗](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>將 Microsoft Office 365 應用程式部署至我的應用程式

針對 Office 365 應用程式，使用者會根據指派給他們的授權，接收 Office 的複本。 存取 Office 應用程式的必要條件是讓使用者獲指派與 Office 應用程式相關聯的正確授權。 當您將授權指派給使用者時，他們會在其 [我的應用程式] 頁面和 Office 365 應用程式啟動器中，自動看到與授權相關聯的應用程式。

如果您想要隱藏一組使用者的 Office 應用程式，可以選擇從我的應用程式入口網站隱藏應用程式，同時仍允許從 Office 365 入口網站進行存取。 在應用程式的 [使用者設定] 部分中，尋找這些設定。 深入瞭解：[從 Azure Active Directory 中的使用者體驗中隱藏應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。

![設定如何隱藏應用程式的螢幕擷取畫面](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>部署應用程式自助功能

自助應用程式存取可讓使用者自行探索並要求存取應用程式。 使用者可以自由存取所需的應用程式，而不需要每次都透過 IT 群組來要求存取權。 當使用者要求存取權且已核准（由應用程式擁有者自動或手動）時，它們會新增至後端的群組。 系統會針對已要求、核准或移除存取權的使用者啟用報告功能，並可讓您控制管理指派的角色。

您可以將應用程式存取要求的核准委派給商務核准者。 商務核准者可以從商務核准者的我的應用程式頁面設定應用程式存取密碼。

深入瞭解：[如何使用自助應用程式存取](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)。

![設定自助式應用程式管理的螢幕擷取畫面](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>驗證您的部署

請確定您的存取面板部署已徹底測試，且已備妥復原計畫。

下列測試應在公司擁有的裝置和個人裝置上執行。 這些測試案例也應反映您的商務使用案例。 下列幾個案例是根據本檔中的範例商務需求，以及一般的技術案例。 新增其他特定于您的需求。

#### <a name="application-sso-access-test-case-examples"></a>應用程式 SSO 存取測試案例範例：


| 商業案例| 預期的結果 |
| - | -|
| 使用者登入我的應用程式入口網站| 使用者可以登入並查看其應用程式 |
| 使用者啟動同盟 SSO 應用程式| 使用者會自動登入應用程式 |
| 使用者第一次啟動密碼 SSO 應用程式| 使用者必須安裝我的應用程式擴充功能 |
| 使用者後續啟動密碼 SSO 應用程式| 使用者會自動登入應用程式 |
| 使用者從 Office 365 入口網站啟動應用程式| 使用者會自動登入應用程式 |
| 使用者從 Managed Browser 啟動應用程式| 使用者會自動登入應用程式 |


#### <a name="application-self-service-capabilities-test-case-examples"></a>應用程式自助功能測試案例範例


| 商業案例| 預期的結果 |
| - | - |
| 使用者可以管理應用程式的成員資格| 使用者可以新增/移除具有應用程式存取權的成員 |
| 使用者可以編輯應用程式| 使用者可以編輯密碼 SSO 應用程式的應用程式描述和認證 |

### <a name="rollback-steps"></a>復原步驟

請務必規劃無法如預期部署時的因應措施。 如果 SSO 設定在部署期間失敗，您必須瞭解如何針對[sso 問題進行疑難排解](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso)，並降低對使用者的影響。 在極端情況下，您可能需要[復原 SSO](../manage-apps/plan-sso-deployment.md)。


## <a name="manage-your-implementation"></a>管理您的執行

您應該使用最低許可權角色來完成 Azure Active Directory 中的必要工作。 [檢查可用的不同角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)，並選擇正確的角色來解決此應用程式每個角色的需求。 某些角色可能需要暫時套用，並在部署完成後移除。

| 角色| 角色| Azure AD 角色  |
| - | -| -|
| 技術服務管理員| 第1層支援| 無 |
| 身分識別管理員| 在問題影響時設定和調試 Azure AD| 全域管理員 |
| 應用程式管理員| 應用程式中的使用者證明，具有許可權的使用者設定| 無 |
| 基礎結構管理員| 憑證變換擁有者| 全域管理員 |
| 商務擁有者/專案關係人| 應用程式中的使用者證明，具有許可權的使用者設定| 無 |

您可以使用[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)來管理您的角色，為具有目錄許可權的使用者提供額外的審核、控制和存取權審查。

### <a name="troubleshoot-access-panel-issues"></a>針對存取面板問題進行疑難排解

使用常見案例為您的支援組織建立疑難排解指南，其解決方案中會指向 Microsoft 檔。 您可能會想要建立可將支援分解到貴組織所使用之層級的指南。

請參閱下列疑難排解指南以取得參考：

[應用程式未出現](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[出現未預期的應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[使用者無法登入存取面板](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[使用自助應用程式存取的問題](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[瀏覽器延伸模組的問題](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>後續步驟

[規劃 Azure 多重要素驗證的部署](https://aka.ms/deploymentplans/mfa)
