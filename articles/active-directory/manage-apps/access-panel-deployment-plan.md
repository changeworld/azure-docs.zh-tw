---
title: 規劃 Azure Active Directory 我的應用程式部署
description: 部署 Azure Active Directory 我的應用程式的指引
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.openlocfilehash: 009818c9b208f5f464949f5e3ffe1404e509ac4b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017714"
---
# <a name="plan-an-azure-active-directory-my-apps-deployment"></a>規劃 Azure Active Directory 我的應用程式部署

Azure Active Directory (Azure AD) 我的應用程式是網頁型入口網站，可協助降低支援成本、提高生產力和安全性，並減少使用者挫折。 系統包含詳細的報告，可在您存取系統時進行追蹤，並通知系統管理員誤用或濫用。 若要瞭解如何從終端使用者的觀點來使用我的應用程式，請參閱 [我的應用程式入口網站](../user-help/my-apps-portal-end-user-access.md)說明。

藉由使用 Azure AD 我的應用程式，您可以：

* 探索並存取其所有公司的 Azure AD 連線資源，例如應用程式
* 要求存取新的應用程式和群組
* 為其他人管理這些資源的存取權
* 管理自助式密碼重設和 Azure Multi-Factor Authentication 設定
* 管理其裝置

它也可讓系統管理員管理：

* 服務條款
* 組織
* 存取權檢閱


## <a name="benefits-of-azure-ad-my-apps-integration"></a>Azure AD 我的應用程式整合的優點

Azure AD 我的應用程式利用下列方式來受益于企業：

**提供直覺的使用者體驗**：我的應用程式為您的單一平臺提供適用于所有 Azure 單一登入 (SSO) 連線的應用程式。 您有一個整合的入口網站，可尋找現有的設定和新的功能，例如群組管理和自助式密碼重設，因為它們是新增的。 直覺的體驗可讓使用者更快速地恢復工作，並提高生產力，同時減少其挫折。

**提高生產力**：我的應用程式中的所有使用者應用程式都已啟用 SSO。 藉由減少或排除額外的登入提示，跨企業應用程式和 Microsoft 365 來啟用 SSO 會建立絕佳的登入體驗。 我的應用程式使用自助和動態成員資格，並提升您身分識別系統的整體安全性。 我的應用程式可確保適當的人員可以管理應用程式的存取權。 我的應用程式可作為一致的登陸頁面，讓您快速尋找資源並繼續工作。

**管理成本**：使用 Azure AD 啟用我的應用程式有助於 divestment 內部部署基礎結構。 它會提供您一致的入口網站來尋找您的所有應用程式、要求資源存取權，以及管理帳戶，進而降低支援成本。

**提高彈性和安全性**：我的應用程式可讓您存取雲端平臺所提供的安全性和彈性。 系統管理員可以輕鬆地將設定變更為應用程式和資源，並且可以容納新的安全性需求，而不會影響使用者。

**啟用健全的審核和使用方式追蹤**：所有使用者功能的審核和使用方式追蹤可讓您知道使用者何時使用其資源，並確保您可以評估安全性。

### <a name="licensing-considerations"></a>授權考量

我的應用程式是免費的，而且不需要在基本層級使用任何授權。 不過，您目錄中的物件數目和您想要部署的其他功能可能需要額外的授權。 某些具有授權需求的常見 Azure AD 案例包含下列安全性功能：

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [以群組為基礎的成員資格](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

如需 Azure AD，請參閱 [完整的授權指南](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites-for-deploying-azure-ad-my-apps"></a>部署 Azure AD 我的應用程式的必要條件

開始此專案之前，請先完成下列必要條件：

* [整合應用程式 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [管理 Azure AD 使用者和群組基礎結構](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-azure-ad-my-apps-deployment"></a>規劃 Azure AD 我的應用程式部署

下表概述我的應用程式部署的主要使用案例：

| 區域| 描述 |
| - | - |
| Access| 您可以從公司網路內的公司和個人裝置存取我的應用程式入口網站。 |
|Access | 您可以從公司網路外部的公司裝置存取我的應用程式入口網站。 |
| 稽核| 使用量資料會每隔29天下載到公司系統。 |
| 控管| 定義和監視使用者指派給 Azure AD 連線應用程式和群組的生命週期。 |
| 安全性| 資源的存取權是透過使用者和群組指派來控制。 只有經過授權的使用者可以管理資源存取權。 |
| 效能| 存取指派傳播時間軸記載和監視。 |
| 使用者體驗| 使用者知道我的應用程式的功能以及其使用方式。|
| 使用者體驗| 使用者可以管理其對應用程式和群組的存取權。|
| 使用者體驗| 使用者可以管理他們的帳戶。 |
| 使用者體驗| 使用者知道瀏覽器相容性。 |
| 支援| 使用者可以找到我的應用程式問題的支援。 |


> [!TIP]
> 我的應用程式可以在遠端使用應用程式 Proxy 時，與內部公司 Url 搭配使用。 若要深入瞭解，請參閱 [教學課程：新增內部部署應用程式，以透過應用程式 Proxy 在 Azure Active Directory 中進行遠端存取](application-proxy-add-on-premises-application.md)。

### <a name="best-practices-for-deploying-azure-ad-my-apps"></a>部署 Azure AD 我的應用程式的最佳作法

我的應用程式的功能可以逐漸啟用。 建議您遵循下列部署順序：

1. 我的應用程式
   * 應用程式啟動程式
   * 自助應用程式管理
   * Microsoft 365 整合

1. 自助應用程式探索
   * 自助式密碼重設
   * Multi-Factor Authentication 設定
   * 裝置管理
   * 使用規定
   * 管理組織

1. 我的群組
   * 自助式群組管理
1. 存取權檢閱
   * 存取權審核管理

從我的應用程式開始，會將使用者導入入口網站，作為存取資源的常用位置。 新增自助應用程式探索是以我的應用程式經驗為基礎。 我的群組和存取權評論建基於自助功能。

### <a name="plan-configurations-for-azure-my-apps"></a>Azure 我的應用程式的規劃設定

下表列出幾個重要的我的應用程式設定，以及您可能使用的一般值：

| 組態| 一般值 |
| - | - |
| 判斷試驗群組| 識別要使用的 Azure AD 安全性群組，並確定所有試驗成員都是群組的一部分。 |
| 判斷要為生產環境啟用的群組或群組。| 識別要使用的 Azure AD 安全性群組或已同步處理至 Azure AD 的 Active Directory 群組。 確定所有試驗成員都是群組的一部分。 |
| 允許使用者對特定類型的應用程式使用 SSO| 同盟 SSO、OAuth、密碼 SSO、應用程式 Proxy |
| 允許使用者使用自助式密碼重設 | 是 |
| 允許使用者使用 Multi-Factor Authentication| 是 |
| 允許使用者針對特定類型的群組使用自助群組管理| 安全性群組，Microsoft 365 群組 |
| 允許使用者使用自助應用程式管理| 是 |
| 允許使用者使用存取權評論| 是 |

### <a name="plan-consent-strategy"></a>規劃同意策略

使用者或系統管理員必須同意任何應用程式的使用規定和隱私權原則。 可能的話，請根據您的商務規則，使用系統管理員同意，讓使用者擁有更好的體驗。

若要使用系統管理員同意，您必須是組織的全域管理員，而且應用程式必須是：

* 在您的組織中註冊

* 已在另一個 Azure AD 組織註冊，且先前至少有一位使用者同意

如需詳細資訊，請參閱 [設定終端使用者在 Azure Active Directory 中同意應用程式的方式](configure-user-consent.md)。

### <a name="engage-the-right-stakeholders"></a>包含正確的專案關係人

當技術專案失敗時，通常會因為對影響、結果和責任不相符的預期而這麼做。 若要避免這些問題，請 [確定您已吸引適當的專案關係人](../fundamentals/active-directory-deployment-plans.md) ，並且能瞭解專案中的專案關係人角色。

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功都非常重要。 主動通知使用者其體驗將會變更的方式和時間，以及在必要時如何獲得支援。

雖然我的應用程式通常不會造成使用者的問題，但請務必做好準備。 開始之前，請先建立支援人員的指南和所有資源的清單。

#### <a name="communications-templates"></a>通訊範本

Microsoft 會為 [電子郵件提供可自訂的範本，以及我的應用程式的其他通訊](https://aka.ms/APTemplates) 。 您可以根據您公司的文化特性，調整這些資產以在其他通道中使用。

## <a name="plan-your-sso-configuration"></a>規劃您的 SSO 設定

當使用者登入應用程式時，他們會經歷驗證程式，並需要證明他們的身分。 如果沒有 SSO，密碼會儲存在應用程式中，而使用者必須知道此密碼。 使用 SSO 時，使用者的認證會傳遞至應用程式，因此不需要重新輸入每個應用程式的密碼。

若要在我的應用程式中啟動應用程式，必須啟用 SSO。 Azure AD 支援多個 SSO 選項。 若要深入瞭解，請參閱 [Azure AD 中的單一登入選項](sso-options.md)。

> [!NOTE]
> 若要深入瞭解如何使用 Azure AD 作為應用程式的身分識別提供者，請參閱 [應用程式管理的快速入門系列](view-applications-portal.md)。

若要獲得我的應用程式頁面的最佳體驗，請從可用於同盟 SSO 的雲端應用程式整合開始。 同盟 SSO 可讓使用者在其應用程式啟動表面上擁有一致的單鍵體驗，而且在設定控制方面更健全。

當應用程式支援時，使用同盟 SSO 搭配 Azure AD (OpenID Connect/SAML) ，而不是以密碼為基礎的 SSO 和 ADFS。

如需如何部署及設定 SaaS 應用程式的詳細資訊，請參閱 [SAAS SSO 部署計畫](https://aka.ms/deploymentplans/sso)。

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>規劃部署我的應用程式瀏覽器擴充功能

當使用者登入密碼型 SSO 應用程式時，他們必須安裝並使用我的應用程式安全登入延伸模組。 延伸模組會執行腳本，將密碼傳輸至應用程式的登入表單。 當使用者第一次啟動密碼型 SSO 應用程式時，系統會提示他們安裝此延伸模組。 有關延伸模組的詳細資訊，請參閱本檔中有關 [安裝我的應用程式瀏覽器延伸](access-panel-extension-problem-installing.md)模組的資訊。

如果您必須整合密碼型 SSO 應用程式，您應該定義一種機制，以 [支援的瀏覽器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)大規模部署擴充功能。 這些選項包括：

* [Internet Explorer 的群組原則](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Internet Explorer 的 Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [適用于 Chrome、Firefox、Microsoft Edge 或 IE 的使用者導向下載和設定](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

不使用密碼型 SSO 應用程式的使用者也可受益于擴充功能。 這些優點包括從其搜尋列啟動任何應用程式、尋找最近使用的應用程式存取權，以及有我的應用程式頁面連結的功能。

#### <a name="plan-for-mobile-access"></a>行動裝置存取規劃

使用 Intune 原則保護的瀏覽器 (Microsoft Edge 或 Intune Managed Browser) 對於啟動密碼型 SSO 應用程式的行動使用者而言是必要的。 受原則保護的瀏覽器可讓您傳送針對應用程式所儲存的密碼。 Microsoft Edge 或受管理的瀏覽器會提供一組 web 資料保護功能。 您也可以在 iOS 和 Android 裝置上使用適用于企業案例的 Microsoft Edge。 Microsoft Edge 支援與 Intune Managed Browser 相同的管理案例，並改善使用者體驗。 深入瞭解： [使用 Microsoft Intune 受原則保護的瀏覽器管理 web 存取](https://docs.microsoft.com/intune/app-configuration-managed-browser)。

## <a name="plan-your-my-apps-deployment"></a>規劃您的我的應用程式部署

我的應用程式的基礎是可供使用者存取的應用程式啟動器入口網站 [https://myapps.microsoft.com](https://myapps.microsoft.com/) 。 [我的應用程式] 頁面可讓使用者在單一位置開始工作，並取得其必要的應用程式。 在這裡，使用者可以找到他們具有單一登入存取權的所有應用程式清單。 

> [!NOTE]
> 相同的應用程式將會顯示在 Microsoft 365 應用程式啟動器中。

規劃將應用程式新增至我的應用程式啟動器的順序，並決定是否要以逐漸或全一次的方式將它們推出。 若要這樣做，請建立應用程式清查，以列出每個應用程式的驗證類型和任何現有的 SSO 整合。

#### <a name="add-applications-to-the-my-apps-panel"></a>將應用程式新增至我的應用程式面板

任何 Azure AD SSO 功能的應用程式都可以新增至我的應用程式啟動器。 您可以使用連結的 SSO 選項來加入其他應用程式。 您可以設定應用程式圖格，以連結至現有 web 應用程式的 URL。 連結的 SSO 可讓您開始將使用者導向我的應用程式入口網站，而不需要將所有應用程式遷移至 Azure AD SSO。 您可以逐漸移至 Azure AD SSO 設定的應用程式，而不會中斷使用者的體驗。

#### <a name="use-my-apps-collections"></a>使用我的應用程式集合

依預設，所有應用程式都會在單一頁面上一起列出。 但是，您可以使用集合將相關的應用程式群組在一起，並將它們顯示在不同的索引標籤上，使其更容易尋找。 例如，您可以使用集合來建立特定工作角色、工作、專案等的應用程式邏輯群組。 如需詳細資訊，請參閱 [如何使用我的應用程式集合](access-panel-collections.md)。 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>規劃是否要使用我的應用程式或現有的入口網站

您的使用者可能已經有我的應用程式以外的應用程式或入口網站。 如果是的話，請決定是否同時支援這兩個入口網站或只使用一個。

如果現有的入口網站已作為使用者的起點，您可以使用使用者存取 Url 來整合我的應用程式功能。 使用者存取 Url 可作為我的應用程式入口網站中可用應用程式的直接連結。 這些 Url 可以內嵌在任何現有的網站中。 當使用者選取連結時，它會從我的應用程式入口網站開啟應用程式。

在 Azure 入口網站中，您可以在應用程式的 [ **屬性** ] 區域中找到 [使用者存取 URL] 屬性。

![[應用程式] 面板的螢幕擷取畫面](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>規劃自助應用程式探索和存取

一旦將核心應用程式集部署到使用者的我的應用程式頁面，您應該啟用自助應用程式管理功能。 自助應用程式探索可讓使用者：

* 尋找要新增至其我的應用程式的新應用程式。 
* 在安裝期間新增可能不知道它們所需的選用應用程式。

核准工作流程可供明確核准以存取應用程式。 當有暫止的應用程式存取要求時，身為核准者的使用者將會在我的應用程式入口網站中收到通知。

## <a name="plan-self-service-group-membership"></a>規劃自助群組成員資格 

您可以讓使用者在 Azure AD 中建立及管理自己的安全性群組或 Microsoft 365 群組。 群組擁有者可以核准或拒絕成員資格要求，以及委派群組成員資格的控制權。 自助群組管理功能不適用於擁有郵件功能的安全性群組或通訊群組清單。

若要規劃自助群組成員資格，請判斷您是否允許組織中的所有使用者建立和管理群組，或只允許一部分的使用者。 如果您允許一部分的使用者，您必須設定要新增這些人員的群組。 如需啟用這些案例的詳細資訊，請參閱 [Azure Active Directory 中的「設定自助群組管理](../users-groups-roles/groups-self-service-management.md) 」。

## <a name="plan-reporting-and-auditing"></a>規劃報告和審核

Azure AD 提供可提供 [技術和商業見解的報表](../reports-monitoring/overview-reports.md)。 與您的業務和技術應用程式擁有者合作，以取得這些報表的擁有權，並定期取用這些報表。 下表提供一般報表案例的一些範例。

| 範例 | 管理風險| 提高生產力| 治理和合規性 |
|  - |- | - | - |
| 報表類型|  應用程式許可權和使用方式| 帳戶佈建活動| 查看誰正在存取應用程式 |
| 可能的動作| 審核存取;撤銷許可權| 補救任何布建錯誤| [撤銷存取權] |

Azure AD 會將大部分的審核資料保留30天。 您可以透過 Azure 系統管理員入口網站或 API 取得資料，以供您下載至分析系統。

#### <a name="auditing"></a>稽核

應用程式存取的審核記錄可供使用30天。 如果您企業內的安全性審核需要較長的保留期，則必須將記錄匯出到安全性資訊事件和管理 (SIEM) 工具，例如 Splunk 或 ArcSight。

針對「審核」、「報告」和「嚴重損壞修復」備份，記錄所需的下載頻率、目標系統是什麼，以及負責管理每個備份的人員。 您可能不需要個別的審核和報告備份。 您的嚴重損壞修復備份應該是個別的實體。

## <a name="deploy-applications-to-users-my-apps-panel"></a>將應用程式部署至使用者的我的應用程式面板

設定 SSO 的應用程式之後，會將存取權指派給群組。 指派的群組中的使用者將擁有存取權，並會在其我的應用程式中看到應用程式，以及 Microsoft 365 應用程式啟動器。

請參閱 [Active Directory 中的將使用者和群組指派給應用程式](methods-for-assigning-users-and-groups.md)。

如果您要在測試或部署期間新增群組，但尚未允許應用程式在我的應用程式中顯示，請參閱 [Azure Active Directory 中的使用者體驗中隱藏應用程式](hide-application-from-user-portal.md)。

### <a name="deploy-microsoft-365-applications-to-my-apps"></a>將 Microsoft 365 應用程式部署至我的應用程式

針對 Microsoft 365 的應用程式，使用者會根據指派給他們的授權，接收 Office 的複本。 存取 Office 應用程式的必要條件是讓使用者指派與 Office 應用程式相關聯的正確授權。 當您為使用者指派授權時，他們會在我的應用程式頁面和 Microsoft 365 應用程式啟動器中，自動看到與授權相關聯的應用程式。

如果您想要隱藏一組使用者的 Office 應用程式，有一個選項可讓您從我的應用程式入口網站中隱藏應用程式，同時仍然允許從 Microsoft 365 入口網站存取。 深入瞭解： [在 Azure Active Directory 的使用者體驗中隱藏應用程式](hide-application-from-user-portal.md)。

### <a name="deploy-application-self-service-capabilities"></a>部署應用程式自助功能

自助應用程式存取可讓使用者自行探索和要求應用程式的存取權。 使用者可以自由存取他們所需的應用程式，而不需要在每次要求存取時都經過 IT 群組。 當使用者要求存取權，並由應用程式擁有者自動或手動核准時，他們就會新增至後端的群組。 報表會在已要求、已核准或已移除存取權的人員上啟用，並且可讓您控制管理指派的角色。

您可以將應用程式存取要求的核准委派給商務核准者。 商務核准者可以從商務核准者的我的應用程式頁面設定應用程式存取密碼。

深入瞭解： [如何使用自助應用程式存取](access-panel-manage-self-service-access.md)。

## <a name="validate-your-deployment"></a>驗證您的部署

確定您的我的應用程式部署已徹底測試，且已備妥復原方案。

您應在公司擁有的裝置和個人裝置上執行下列測試。 這些測試案例也應反映您的商務使用案例。 以下是以本檔中的範例商務需求和一般技術案例為基礎的一些案例。 新增其他特定需求。

#### <a name="application-sso-access-test-case-examples"></a>應用程式 SSO 存取測試案例範例：


| 商務案例| 預期的結果 |
| - | -|
| 使用者登入我的應用程式入口網站| 使用者可以登入並查看其應用程式 |
| 使用者啟動同盟 SSO 應用程式| 使用者會自動登入應用程式 |
| 使用者第一次啟動密碼 SSO 應用程式| 使用者需要安裝我的應用程式擴充功能 |
| 使用者會在後續時間啟動密碼 SSO 應用程式| 使用者會自動登入應用程式 |
| 使用者從 Microsoft 365 入口網站啟動應用程式| 使用者會自動登入應用程式 |
| 使用者從 Managed Browser 啟動應用程式| 使用者會自動登入應用程式 |


#### <a name="application-self-service-capabilities-test-case-examples"></a>應用程式自助功能測試案例範例


| 商務案例| 預期的結果 |
| - | - |
| 使用者可以管理應用程式的成員資格| 使用者可以新增/移除具有應用程式存取權的成員 |
| 使用者可以編輯應用程式| 使用者可以編輯密碼 SSO 應用程式的應用程式描述和認證 |

### <a name="rollback-steps"></a>復原步驟

請務必規劃無法如預期部署時的因應措施。 如果 SSO 設定在部署期間失敗，您必須瞭解如何針對 [sso 問題進行疑難排解](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) ，並降低對使用者的影響。 在極端情況下，您可能需要 [復原 SSO](../manage-apps/plan-sso-deployment.md#rollback-process)。


## <a name="manage-your-implementation"></a>管理您的實作為

使用最低特殊許可權角色來完成 Azure Active Directory 內的必要工作。 請[參閱可用的不同角色](../users-groups-roles/directory-assign-admin-roles.md)，並選擇正確的角色，為此應用程式的每個角色解決您的需求。 某些角色可能需要暫時套用，並在部署完成後移除。

| 角色| 角色| Azure AD 角色  |
| - | -| -|
| 技術服務人員管理| 第1層支援| 無 |
| 身分識別管理員| 在問題影響時進行設定和調試 Azure AD| 全域系統管理員 |
| 應用程式管理員| 應用程式中的使用者證明，具有許可權的使用者設定| 無 |
| 基礎結構系統管理員| 憑證變換擁有者| 全域系統管理員 |
| 商務擁有者/專案關係人| 應用程式中的使用者證明，具有許可權的使用者設定| 無 |

您可以使用 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 管理您的角色，為具有目錄許可權的使用者提供額外的審核、控制和存取權審核。

### <a name="troubleshoot-my-apps-issues"></a>針對我的應用程式問題進行疑難排解

使用常見案例為您的支援組織建立疑難排解指南，這些案例會指向 Microsoft 檔中的解決方案。 您可能會想要建立可將支援細分至組織所使用之階層的指南。

請參閱這些疑難排解指南以取得參考：

[應用程式未出現](access-panel-troubleshoot-application-not-appearing.md)

[出現非預期的應用程式](access-panel-troubleshoot-unexpected-application.md)

[使用者無法登入我的應用程式](access-panel-troubleshoot-web-sign-in-problem.md)

[使用自助應用程式存取的問題](access-panel-troubleshoot-self-service-access.md)

[瀏覽器擴充功能的問題](manage-access-panel-browser-extension.md)

## <a name="next-steps"></a>後續步驟

[規劃 Azure Multi-Factor Authentication 的部署](https://aka.ms/deploymentplans/mfa)
