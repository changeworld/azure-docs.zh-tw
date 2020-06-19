---
title: 適用於 Azure Active Directory 自助式密碼重設的部署考量
description: 了解成功實作 Azure AD 自助式密碼重設的部署考量和策略
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6151bbd9f056ef95c0875b83c7b6fccb732aebfe
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870966"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>規劃 Azure Active Directory 自助式密碼重設部署

> [!IMPORTANT]
> 此部署計劃提供部署 Azure AD 自助式密碼重設 (SSPR) 的指導方針與最佳做法。
>
> **如果您是使用者且需要重新進入您的帳戶，請移至 [https://aka.ms/sspr](https://aka.ms/sspr)** 。

[自助式密碼重設 (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) \(英文\) 是 Azure Active Directory (AD) 功能，可讓使用者無需連絡 IT 人員以尋求協助，便能重設其密碼。 無論何時何處，使用者都可以快速將自己解除封鎖並繼續工作。 透過讓員工自行將自己解除封鎖，您的組織便能針對最常見的密碼相關問題減少無生產力的時間，以及高昂的支援成本。

SSPR 具有下列重要功能：

* 自助式服務可讓使用者重設其過期或未過期的密碼，而不需要連絡系統管理員或技術服務人員以尋求支援。
* [密碼回寫](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) \(部分機器翻譯\) 能允許透過雲端管理內部部署密碼及解決帳戶鎖定。
* 密碼管理活動報告能讓系統管理員針對其組織中所發生的密碼重設和註冊活動獲得見解。

此部署指南會說明如何規劃及測試 SSPR 推出。

若要先快速認識 SSPR 的運作方式，再回來了解更多部署上的考量：

> [!div class="nextstepaction"]
> [啟用自助式密碼重設 (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>了解 SSPR

深入了解 SSPR。 請參閱[運作方式：Azure AD 自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)。

### <a name="key-benefits"></a>主要權益

啟用 SSPR 的主要優點為：

* **管理成本**。 SSPR 能透過讓使用者自行重設密碼來降低 IT 支援成本。 其也能降低因遺失密碼及鎖定所產生的時間成本。 

* **直覺的使用者體驗**。 其能提供直覺的一次性使用者註冊程序，可讓使用者視需要從任何裝置或位置重設密碼及將帳戶解除封鎖。 SSPR 可讓使用者更快速地重新開始工作並提高生產力。

* **彈性和安全性**。 SSPR 可讓企業存取雲端平台提供的安全性和彈性。 系統管理員可以變更設定以配合新的安全性需求，並在不中斷使用者登入的情況下將這些變更推出給他們。

* **強固的稽核和使用情況追蹤**。 組織可以確保在使用者重設其密碼時，商務系統仍然能保持安全。 強固的稽核記錄會包含密碼重設程序之每個步驟的資訊。 這些記錄可以透過 API 提供，並讓使用者將資料匯入所選擇的安全性事件與事件監視 (SIEM) 系統。

### <a name="licensing"></a>授權

Azure Active Directory 是依使用者授權，這表示每個使用者針對其使用的功能都需要適當的授權。 我們建議針對 SSPR 使用以群組為基礎的授權。 

若要比較版本和功能，以及啟用以群組或使用者為基礎的授權，請參閱[Azure AD 自助式密碼重設的授權需求](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing) \(部分機器翻譯\)。

如需定價的詳細資訊，請參閱 [Azure Active Directory 定價](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites"></a>Prerequisites

* 可運作且至少已啟用試用版授權的 Azure AD 租用戶。 如有需要，[請建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 具有全域系統管理員權限的帳戶。


### <a name="training-resources"></a>訓練資源

| 資源| 連結與描述 |
| - | - |
| 影片| [透過更佳的 IT 可擴縮性賦予使用者能力](https://youtu.be/g9RpRnylxS8) \(英文\) 
| |[什麼是自助式密碼重設？](https://youtu.be/hc97Yx5PJiM) \(英文\)|
| |[部署自助式密碼重設](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0) \(英文\)|
| |[如何在 Azure AD 中為使用者設定自助式密碼重設？](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) \(英文\) |
| |[如何針對 Azure Active Directory [讓使用者準備好]註冊[其]安全性資訊](https://youtu.be/gXuh0XS18wA) \(英文\) |
| 線上課程|[在 Microsoft Azure Active Directory 中管理身分識別](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) \(英文\) 使用 SSPR 來賦予使用者現代化的受保護體驗。 請特別參閱＜[管理 Azure Active Directory 使用者和群組](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)＞課程模組。 |
|Pluralsight 付費課程 |[身分識別與存取管理的問題](https://www.pluralsight.com/courses/identity-access-management-issues) \(英文\) 深入了解您組織中應該注意的 IAM 與全性問題。 請特別參閱＜其他驗證方法＞課程模組。|
| |[開始使用 Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) \(英文\) 了解以能提供驗證、授權、加密及安全行動裝置體驗的方式，將內部部署資產擴充到雲端的最佳做法。 請特別參閱＜設定 Microsoft Azure Active Directory Premium 的進階功能＞課程模組。
|教學課程 |[完成 Azure AD 自助式密碼重設試驗推出](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[啟用密碼回寫](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[從 Windows 10 的登入畫面重設 Azure AD 密碼](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) \(部分機器翻譯\) |
| 常見問題集|[密碼管理常見問題集](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) \(部分機器翻譯\) |


### <a name="solution-architecture"></a>方案架構

下列範例會說明適用於常見混合式環境的密碼重設方案架構。

![方案架構的圖表](./media/howto-sspr-deployment//solutions-architecture.png)

工作流程的描述

若要重設密碼，使用者須移至[密碼重設入口網站](https://aka.ms/sspr)。 他們必須驗證先前所註冊的單一或多個驗證方法，以證明其身分識別。 如果他們成功重設密碼，便會開始重設程序。

* 針對僅限雲端的使用者，SSPR 會將新密碼儲存在 Azure AD 中。 

* 針對混合式使用者，SSPR 會透過 Azure AD Connect 服務將密碼回寫至內部部署 Active Directory。 

注意:針對已停用[密碼雜湊同步處理 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) 的使用者，SSPR 僅會將密碼儲存在內部部署 Active Directory。

### <a name="best-practices"></a>最佳作法

您可以透過將 SSPR 和組織中另一個熱門的應用程式或服務一起部署，來協助使用者快速註冊。 此動作將會產生大量登入並驅動註冊。

在部署 SSPR 之前，您可以選擇判斷密碼重設呼叫的數目和平均成本。 您可以在部署後使用此資料來顯示 SSPR 為組織所帶來的價值。

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>啟用 SSPR 與 MFA 的合併註冊

Microsoft 建議組織針對 SSPR 和多重要素驗證啟用合併註冊體驗。 當您啟用此合併註冊體驗時，使用者只需要選取其註冊資訊一次便能啟用這兩個功能。

合併註冊體驗並不需要組織同時啟用 SSPR 和 Azure Multi-Factor Authentication。 合併註冊能為組織提供更佳的使用者體驗。 如需詳細資訊，請參閱[合併的安全性資訊註冊](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>規劃部署專案

在決定環境中此部署的策略時，請考慮您的組織需求。

### <a name="engage-the-right-stakeholders"></a>包含正確的專案關係人

當技術專案失敗時，其通常是因為人員對影響、結果與責任抱持不相符的預期而造成。 若要避免這些錯誤，[請務必包含正確的專案關係人](https://aka.ms/deploymentplans) \(部分機器翻譯\)，並透過記錄專案關係人及其專案投入和責任，使該專案關係人清楚了解其在專案中的角色。

#### <a name="required-administrator-roles"></a>必要系統管理員角色


| 商務角色 (Role)/角色 (Persona)| Azure AD 角色 (若有必要) |
| - | - |
| 第 1 層技術服務人員| 密碼管理員 |
| 第 2 層技術服務人員| 使用者管理員 |
| SSPR 系統管理員| 全域管理員 |


### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功都非常重要。 您應該主動與您的使用者溝通其體驗將如何改變、何時會改變，以及如何在遇到問題時取得支援。 請檢閱 [Microsoft 下載中心上的自助式密碼重設推出材料](https://www.microsoft.com/download/details.aspx?id=56768) \(英文\)，以取得規劃使用者溝通策略的靈感。

### <a name="plan-a-pilot"></a>規劃試驗

我們建議在測試環境中進行 SSPR 的初始設定。 為組織中的使用者子集啟用 SSPR 來先以試驗群組執行。 請參閱[試驗的最佳做法](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) \(部分機器翻譯\)。

若要建立群組，請參閱如何[在 Azure Active Directory 中建立群組和新增成員](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal)。 

## <a name="plan-configuration"></a>規劃設定

需要下列設定以搭配建議值啟用 SSPR。

| 區域 | 設定 | 值 |
| --- | --- | --- |
| **SSPR 屬性** | 已啟用自助式密碼重設 | 針對要進行試驗的群組使用 [已選取] / 針對生產環境使用 [全部] |
| **驗證方法** | 註冊所需的驗證方法數 | 一律比重設所需多出 1 種 |
|   | 重設所需的驗證方法數 | 一個或兩個 |
| **註冊** | 登入時要求使用者註冊 | 是 |
|   | 要求使用者重新確認其驗證資訊的等候天數 | 90 - 180 天 |
| **通知** | 通知使用者密碼重設 | 是 |
|   | 當其他系統管理員重設其密碼時通知所有系統管理員 | 是 |
| **自訂** | 自訂技術服務人員連結 | 是 |
|   | 自訂的技術服務人員電子郵件或 URL | 支援網站或電子郵件地址 |
| **內部部署整合** | 將密碼回寫至內部部署 AD | 是 |
|   | 允許使用者在不重設密碼的情況下解除鎖定帳戶 | 是 |

### <a name="sspr-properties"></a>SSPR 屬性

啟用 SSPR 時，請在試驗環境中選擇適當的安全性群組。

* 若要針對所有人強制執行 SSPR 註冊，建議使用 [全部] 選項。
* 否則，請選取適當的 Azure AD 或 AD 安全性群組。

### <a name="authentication-methods"></a>驗證方法

啟用 SSPR 時，使用者只有在於系統管理員已啟用的驗證方法中有資料存在時，才能夠重設其密碼。 方法包括電話、Authenticator 應用程式通知、安全性問題等。如需詳細資訊，請參閱[什麼是驗證方法？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods) \(部分機器翻譯\)。

建議使用下列驗證方法設定：

* 將 [註冊所需的驗證方法數] 設定為比重設所需的數目至少多出一種。 允許多重驗證可為使用者在需要重設時提供彈性。

* 將 [需要重設的方法數] 設定為適合您組織的層級。 設定為一種會產生最少的摩擦，而兩種則可能會增加您的安全性狀態。 

注意:使用者必須具有在 [Azure Active Directory 中的密碼原則和限制](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) \(部分機器翻譯\) 中所設定的驗證方法。

### <a name="registration-settings"></a>註冊設定

將 [登入時要求使用者註冊] 設定為 [是]。 此設定會要求使用者在登入時註冊，確保所有使用者都會受到保護。

將 [要求使用者重新確認其驗證資訊的等候天數] 設定為介於 **90** 到 **180** 天之間，除非您的組織具有較短時間範圍的業務需求。

### <a name="notifications-settings"></a>通知設定

將 [通知使用者密碼重設] 和 [當其他系統管理員重設其密碼時通知所有系統管理員] 設定為 [是]。 針對這兩個設定都選取 [是]，能透過讓使用者知道其密碼已重設來提高安全性。 其也能確保所有系統管理員都會知道某個系統管理員已變更密碼。 如果有使用者或系統管理員收到通知，且其並未起使該變更，他們便可以立即回報潛在的安全性問題。

### <a name="customization-settings"></a>自訂設定

請務必自訂技術服務人員電子郵件或 URL，以確保遇到問題的使用者可以立即取得協助。 將此選項設定為您使用者熟悉的常用技術服務人員電子郵件地址或網頁。 

如需詳細資訊，請參閱[自訂 Azure AD 的自助式密碼重設功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization) \(部分機器翻譯\)。

### <a name="password-writeback"></a>密碼回寫

**密碼回寫**是透過 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) 啟用，並會將雲端中的密碼重設即時回寫至現有內部部署目錄。 如需詳細資訊，請參閱[什麼是密碼回寫？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) \(部分機器翻譯\)

建議使用下列設定：

* 確定 [將密碼回寫至內部部署 AD] 已設定為 [是]。 
* 將 [允許使用者在不重設密碼的情況下解除鎖定帳戶] 設定為 [是]。

根據預設，Azure AD 可以在執行密碼重設時解除鎖定帳戶。

### <a name="administrator-password-setting"></a>系統管理員密碼設定

系統管理員帳戶具有更高的權限。 內部部署企業或網域系統管理員無法透過 SSPR 重設其密碼。 內部部署系統管理員帳戶具有下列限制：

* 只能在其內部部署環境中變更其密碼。
* 永遠無法使用祕密問題與答案作為重設其密碼的方法。

建議您不要與 Azure AD 同步您的內部部署 Active Directory 系統管理員帳戶。

### <a name="environments-with-multiple-identity-management-systems"></a>具有多個身分識別管理系統的環境

某些環境具有多個身分識別管理系統。 內部部署身分識別管理員 (例如 Oracle AM 和 SiteMinder) 需要與 AD 同步以取得密碼。 您可以搭配 Microsoft Identity Manager (MIM) 使用如密碼變更通知服務 (PCNS) 的工具來執行此動作。 若要尋找這種更加複雜案例的相關資訊，請參閱[在網域控制站上部署 MIM 密碼變更通知服務](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)一文。

## <a name="plan-testing-and-support"></a>規劃測試與支援

在您部署的每一個階段 (從一開始的試驗群組到部署到整個組織)，請確保結果皆如您所預期。

### <a name="plan-testing"></a>規劃測試

若要確保您的部署會如預期般運作，請規劃一組測試案例以驗證實作。 若要評定測試案例，您需要具有密碼的非系統管理員測試使用者。 如果您需要建立使用者，請參閱[將新的使用者新增至 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory) \(部分機器翻譯\)。

下表包含實用的測試案例，可讓您用來根據您的原則記錄組織預期的結果。
<br>


| 商務案例| 預期的結果 |
| - | - |
| SSPR 入口網站可從公司網路內部存取| 由您的組織決定 |
| SSPR 入口網站可從公司網路外部存取| 由您的組織決定 |
| 在使用者未啟用密碼重設的情況下從瀏覽器重設使用者密碼| 使用者無法存取密碼重設流程 |
| 在使用者未註冊密碼重設的情況下從瀏覽器重設使用者密碼| 使用者無法存取密碼重設流程 |
| 使用者在系統強制執行密碼重設註冊時登入| 系統提示使用者註冊安全性資訊 |
| 使用者在密碼重設註冊完成時登入| 系統提示使用者註冊安全性資訊 |
| 使用者在沒有授權時可以存取 SSPR 入口網站| 可以存取 |
| 從 Windows 10 已使用 Azure AD 或混合式 Azure AD 而聯結的裝置鎖定畫面重設使用者密碼| 使用者可以重設密碼 |
| SSPR 註冊和使用狀況資料會以近乎即時的方式提供給系統管理員| 會透過稽核記錄提供 |

您也可以參閱[完成 Azure AD 自助式密碼重設試驗推出](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)。 在此教學課程中，您將在組織中啟用 SSPR 的試驗推出，並使用非系統管理員帳戶進行測試。

### <a name="plan-support"></a>規劃支援

雖然 SSPR 通常不會產生使用者問題，請務必讓支援人員準備好處理可能發生的問題。 雖然系統管理員可以透過 Azure AD 入口網站為使用者重設密碼，最好還是協助透過自助式支援程序來解決問題。

若要確保支援小組的成功，您可以根據來自您使用者的問題建立常見問題集。 以下是一些範例：

| 案例| 描述 |
| - | - |
| 使用者沒有任何可用的已註冊驗證方法| 使用者正在嘗試重設其密碼，但是其所註冊的驗證方法並沒有任何一個是可用的 (例如：他們將其手機留在家裡，且無法存取電子郵件) |
| 使用者無法透過其辦公室電話或手機接收簡訊或來電| 使用者正在嘗試透過簡訊或來電以驗證其身分識別，但並未收到簡訊/來電。 |
| 使用者無法存取密碼重設入口網站| 使用者想要重設其密碼，但並未啟用密碼重設，且無法存取該頁面以更新密碼。 |
| 使用者無法設定新密碼| 使用者已在密碼重設流程中完成驗證，但無法設定新密碼。 |
| 使用者在 Windows 10 裝置上看不到 [重設密碼] 連結| 使用者正在嘗試從 Windows 10 鎖定畫面重設密碼，但是該裝置尚未與 Azure AD 聯結，或是 Intune 裝置原則尚未啟用 |

### <a name="plan-rollback"></a>規劃復原

若要復原部署：

* 針對單一使用者，請將該使用者從安全性群組中移除 

* 針對群組，請將該群組從 SSPR 設定中移除

* 針對所有人，請針對 Azure AD 租用戶停用 SSPR

## <a name="deploy-sspr"></a>部署 SSPR

在部署之前，請確定您已完成下列操作：

1. 已建立並開始執行您的[溝通計劃](#plan-communications)。

1. 已決定適當的[組態設定](#plan-configuration)。

1. 已識別出要用於[試驗](#plan-a-pilot)及生產環境的使用者和群組。

1. [已決定註冊和自助式服務的組態設定](#plan-configuration)。

1. [已設定密碼回寫](#password-writeback) (如果您有混合式環境的話)。


**您現在已經準備好部署 SSPR！**

請參閱[啟用自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset)以取得設定下列領域的完整逐步指示。

1. [驗證方法](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [註冊設定](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) \(部分機器翻譯\)

1. [通知設定](#notifications-settings)

1. [自訂設定](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization) \(部分機器翻譯\)

1. [內部部署整合](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>在 Windows 中啟用 SSPR
針對執行 Windows 7、8、8.1 及 10 的電腦，您可以[讓使用者在 Windows 登入畫面重設其密碼](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows) \(部分機器翻譯\)

## <a name="manage-sspr"></a>管理 SSPR

Azure AD 可以透過稽核和報告來提供 SSPR 效能的額外資訊。

### <a name="password-management-activity-reports"></a>密碼管理活動報告 

您可以在 Azure 入口網站上使用預先建置的報告來測量 SSPR 效能。 如果您已適當地取得授權，則也可以建立自訂查詢。 如需詳細資訊，請參閱[Azure AD 密碼管理的報告選項](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) \(部分機器翻譯\)

> [!NOTE]
>  您必須是[全域管理員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) \(部分機器翻譯\)，且您必須選擇加入以讓系統針對您的組織收集此資料。 若要選擇加入，您必須瀏覽 Azure 入口網站上的 [報告] 索引標籤或稽核記錄至少一次。 在那之前，系統並不會針對您的組織收集該資料。

針對註冊和密碼重設的稽核記錄會可供使用 30 天。 如果您公司內的安全性稽核要求更長的保留期，便必須將該記錄匯出並使用 [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory)、Splunk 或 ArcSight 之類的 SIEM 工具加以取用。

![SSPR 報告螢幕擷取畫面](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>驗證方法 - 使用量和見解

[使用量和見解](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) \(部分機器翻譯\) 可讓您了解 Azure MFA 和 SSPR 等功能的驗證方法在您組織中的運作情況。 此報告功能可讓您的組織了解要註冊的方法及其使用方式。

### <a name="troubleshoot"></a>疑難排解

* 請參閱[針對自助式密碼重設進行疑難排解](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) \(部分機器翻譯\) 

* 請遵循[密碼管理常見問題集](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) \(部分機器翻譯\) 

### <a name="helpful-documentation"></a>實用的文件

* [驗證方法有哪些？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [運作方式：Azure AD 自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) \(部分機器翻譯\)

* [自訂 Azure AD 的自助式密碼重設功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization) \(部分機器翻譯\)

* [Azure Active Directory 中的密碼原則和限制](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [什麼是密碼回寫？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>後續步驟

* 若要開始部署 SSPR，請參閱[啟用 Azure AD 自助式密碼重設](tutorial-enable-sspr.md)

* [考慮實作 Azure AD 密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) \(部分機器翻譯\)

* [考慮實作 Azure AD 智慧鎖定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) \(部分機器翻譯\)
