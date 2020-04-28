---
title: Azure Active Directory 自助式密碼重設的部署考慮
description: 瞭解成功執行 Azure AD 自助式密碼重設的部署考慮和策略
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
ms.openlocfilehash: 8bec28b98a8d2640b5a8034569d49077ce6b4177
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81450984"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>規劃 Azure Active Directory 的自助式密碼重設部署

> [!IMPORTANT]
> 此部署計畫提供部署 Azure AD 自助式密碼重設（SSPR）的指引和最佳作法。
>
> **如果您是和使用者，而且需要取回您的帳戶，請移至[https://aka.ms/sspr](https://aka.ms/sspr) **。

[自助式密碼重設（SSPR）](https://www.youtube.com/watch?v=tnb2Qf4hTP8)是一項 AZURE ACTIVE DIRECTORY （AD）功能，可讓使用者重設其密碼，而不需要聯絡 IT 人員尋求協助。 使用者可以快速地解除封鎖自己，不論是在何處或每天的時間，都能繼續運作。 藉由允許員工解除封鎖自己，您的組織可以減少非生產力的時間，以及最常見的密碼相關問題的高支援成本。

SSPR 具有下列主要功能：

* 自助服務可讓使用者重設已過期或未過期的密碼，而不需聯絡系統管理員或技術服務人員以取得支援。
* [密碼回寫](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)可讓您管理內部部署密碼，並透過雲端來解析帳戶鎖定。
* 密碼管理活動報告讓系統管理員得以深入瞭解其組織內發生的密碼重設和註冊活動。

此部署指南說明如何規劃並測試 SSPR 推出。

若要快速查看 SSPR 的運作，然後返回以瞭解其他部署考慮：

> [!div class="nextstepaction"]
> [啟用自助式密碼重設（SSPR）](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>深入瞭解 SSPR

深入瞭解 SSPR。 瞭解[其運作方式： Azure AD 自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)。

### <a name="key-benefits"></a>主要權益

啟用 SSPR 的主要優點如下：

* **管理成本**。 SSPR 可讓使用者自行重設密碼，以降低 IT 支援成本。 它也可減少因密碼遺失和鎖定而遺失的時間成本。 

* **直覺的使用者體驗**。 它提供了直覺的一次性使用者註冊程式，可讓使用者從任何裝置或位置，視需要重設密碼和解除封鎖帳戶。 SSPR 可讓使用者更快速地恢復工作，並提高生產力。

* **彈性和安全性**。 SSPR 可讓企業存取雲端平臺所提供的安全性和彈性。 系統管理員可以變更設定以配合新的安全性需求，並將這些變更變換給使用者，而不會中斷其登入。

* **健全的審核和使用方式追蹤**。 組織可以確保商務系統在其使用者重設自己的密碼時保持安全。 健全的 audit 記錄包含密碼重設程式的每個步驟的資訊。 這些記錄可從 API 取得，並且可讓使用者將資料匯入至所選擇的安全性事件和事件監視（SIEM）系統。

### <a name="licensing"></a>授權

Azure Active Directory 是依使用者授權，表示每位使用者都需要適當的授權，才能使用他們所用的功能。 我們建議 SSPR 以群組為基礎的授權。 

若要比較版本和功能，以及啟用群組或以使用者為基礎的授權，請參閱[Azure AD 自助式密碼重設的授權需求](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing)。

如需價格的詳細資訊，請參閱[Azure Active Directory 定價](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites"></a>Prerequisites

* 可運作且至少已啟用試用版授權的 Azure AD 租用戶。 如有需要，[請建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 具有全域系統管理員權限的帳戶。


### <a name="training-resources"></a>訓練資源

| 資源| 連結和描述 |
| - | - |
| 影片| [為使用者提供更佳的 IT 擴充能力](https://youtu.be/g9RpRnylxS8) 
| |[什麼是自助式密碼重設？](https://youtu.be/hc97Yx5PJiM)|
| |[部署自助式密碼重設](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[如何為 Azure AD 中的使用者設定自助式密碼重設？](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[如何 [準備使用者] 註冊 [其] Azure Active Directory 的安全性資訊](https://youtu.be/gXuh0XS18wA) |
| 線上課程|[管理 Microsoft Azure Active Directory 中的](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities)身分識別使用 SSPR 為您的使用者提供現代化、受保護的體驗。 請特別參閱「[管理 Azure Active Directory 使用者和群組](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)」課程模組。 |
|Pluralsight 付費課程 |身分[識別與存取管理的問題](https://www.pluralsight.com/courses/identity-access-management-issues)瞭解您組織中要注意的 IAM 和安全性問題。 請特別參閱「其他驗證方法」課程模組。|
| |[Microsoft 企業行動化套件的消費者入門](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started)瞭解將內部部署資產擴充至雲端的最佳作法，以允許進行驗證、授權、加密及安全的行動裝置體驗。 特別參閱 < 設定 Microsoft Azure Active Directory Premium 的先進功能」課程模組。
|教學課程 |[完成 Azure AD 的自助式密碼重設試驗推出](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[啟用密碼回寫](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[從 Windows 10 的登入畫面 Azure AD 密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| 常見問題集|[密碼管理的常見問題](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>方案架構

下列範例說明一般混合式環境的密碼重設解決方案架構。

![解決方案架構的圖表](./media/howto-sspr-deployment//solutions-architecture.png)

工作流程的描述

若要重設密碼，使用者可以前往[密碼重設入口網站](https://aka.ms/sspr)。 他們必須確認先前註冊的驗證方法或方法，以證明其身分識別。 如果他們成功重設密碼，就會開始重設程式。

* 對於僅限雲端的使用者，SSPR 會將新密碼儲存在 Azure AD 中。 

* 針對混合式使用者，SSPR 會透過 Azure AD Connect 服務，將密碼寫回內部內部部署的 Active Directory。 

注意：如果使用者已停用[密碼雜湊同步處理（PHS）](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) ，SSPR 只會將密碼儲存在內部部署中的 Active Directory。

### <a name="best-practices"></a>最佳作法

您可以透過將 SSPR 與組織中另一個熱門的應用程式或服務一起部署，協助使用者快速註冊。 此動作會產生大量的登入，並將會驅動註冊。

部署 SSPR 之前，您可以選擇判斷每個密碼重設呼叫的數目和平均成本。 您可以使用此資料部署後，顯示 SSPR 會帶入組織的值。

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>啟用 SSPR 和 MFA 的合併註冊

Microsoft 建議組織啟用 SSPR 和多重要素驗證的結合註冊體驗。 當您啟用此結合的註冊體驗時，使用者只需要選取其註冊資訊一次，即可啟用這兩項功能。

結合的註冊體驗不需要組織同時啟用 SSPR 和 Azure 多重要素驗證。 結合註冊可為組織提供更好的使用者體驗。 如需詳細資訊，請參閱[結合的安全性資訊註冊](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>規劃部署專案

當您在環境中判斷此部署的策略時，請考慮您的組織需求。

### <a name="engage-the-right-stakeholders"></a>參與適當的專案關係人

當技術專案失敗時，通常是因為對影響、結果和責任的預期不相符所造成。 若要避免這些錯誤，請[確定您參與的是正確的專案關係人](https://aka.ms/deploymentplans)，而且可以記錄專案關係人和其專案輸入和標準責任，以充分瞭解專案中的專案關係人角色。

#### <a name="required-administrator-roles"></a>必要系統管理員角色


| 商務角色/角色| Azure AD 角色（如有必要） |
| - | - |
| 層級1技術服務人員| 密碼管理員 |
| 層級2技術服務人員| 使用者管理員 |
| SSPR 系統管理員| 全域管理員 |


### <a name="plan-communications"></a>規劃溝通

通訊對於任何新服務的成功非常重要。 您應該主動與使用者溝通他們的經驗如何改變、何時會變更，以及如何在遇到問題時取得支援。 請參閱[Microsoft 下載中心的自助式密碼重設首](https://www.microsoft.com/download/details.aspx?id=56768)度發行資料，以瞭解如何規劃您的終端使用者通訊策略。

### <a name="plan-a-pilot"></a>規劃試驗

我們建議您在測試環境中 SSPR 的初始設定。 從試驗群組開始，請為組織中的一部分使用者啟用 SSPR。 請參閱[試驗的最佳做法](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)。

若要建立群組，請參閱如何[建立群組和在 Azure Active Directory 中新增成員](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal)。 

## <a name="plan-configuration"></a>規劃設定

若要啟用 SSPR 以及建議的值，需要下列設定。

| 區域 | 設定 | 值 |
| --- | --- | --- |
| **SSPR 屬性** | 已啟用自助式密碼重設 | 針對生產環境**選取**的試驗/ **All**群組 |
| **驗證方法** | 註冊所需的驗證方法 | 重設所需的一律為1 |
|   | 重設所需的驗證方法 | 一個或兩個 |
| **註冊** | 登入時要求使用者註冊 | 是 |
|   | 要求使用者重新確認其驗證資訊的等候天數 | 90–180天 |
| **通知** | 通知使用者密碼重設 | 是 |
|   | 當其他系統管理員重設其密碼時通知所有系統管理員 | 是 |
| **自訂** | 自訂技術服務人員連結 | 是 |
|   | 自訂技術服務人員電子郵件或 URL | 支援網站或電子郵件地址 |
| **內部部署整合** | 將密碼寫回內部部署 AD | 是 |
|   | 允許使用者在不重設密碼的情況下解除鎖定帳戶 | 是 |

### <a name="sspr-properties"></a>SSPR 屬性

啟用 SSPR 時，請在試驗環境中選擇適當的安全性群組。

* 若要為每個人強制執行 SSPR 註冊，我們建議使用**All**選項。
* 否則，請選取適當的 Azure AD 或 AD 安全性群組。

### <a name="authentication-methods"></a>驗證方法

啟用 SSPR 時，只有在系統管理員已啟用的驗證方法中有資料存在時，使用者才能重設其密碼。 方法包括電話、驗證器代理程式更新、安全性問題等。如需詳細資訊，請參閱[什麼是驗證方法？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)。

我們建議您採用下列驗證方法設定：

* 將**所需的驗證方法設定為至少註冊**一次，而不是重設所需的數目。 允許多重驗證可讓使用者在需要重設時擁有彈性。

* 設定**必要的方法數，以重設**為適合您組織的層級。 其中一個需要最少的摩擦，而兩者可能會增加您的安全性狀態。 

注意：使用者必須在[Azure Active Directory 的密碼原則和限制](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)中設定驗證方法。

### <a name="registration-settings"></a>註冊設定

將 [**要求使用者在登入時註冊**] 設定為 **[是]**。 此設定需要使用者在登入時註冊，以確保所有使用者都受到保護。

設定**要求使用者重新確認其驗證資訊**在**90**到**180**天之間的天數，除非您的組織有較短時間範圍的業務需求。

### <a name="notifications-settings"></a>通知設定

設定 [**通知使用者密碼重設**] 和 [**當其他系統管理員將其密碼重設**為 **[是]** 時通知所有系統管理員]。 選取 **[是]** ，可確保使用者在密碼重設時知道，以提高安全性。 它也可確保所有系統管理員在管理員變更密碼時都能感知。 如果使用者或系統管理員收到通知，而他們尚未起始變更，他們可以立即報告潛在的安全性問題。

### <a name="customization-settings"></a>自訂設定

請務必自訂技術服務人員電子郵件或 URL，以確保遇到問題的使用者可以立即取得協助。 將此選項設定為您的使用者熟悉的一般技術支援電子郵件地址或網頁。 

如需詳細資訊，請參閱[自訂自助式密碼重設的 Azure AD 功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)。

### <a name="password-writeback"></a>密碼回寫

**密碼回寫**會以[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)啟用，並將雲端中的密碼重設回寫到現有的內部部署目錄。 如需詳細資訊，請參閱[什麼是密碼回寫？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

我們建議您進行下列設定：

* 確定 [將**密碼寫回至內部部署 AD** ] 已設定為 **[是]**。 
* 將 [**允許使用者解除鎖定帳戶但不重設密碼**] 設定為 **[是]**。

根據預設，Azure AD 可以在執行密碼重設時解除鎖定帳戶。

### <a name="administrator-password-setting"></a>系統管理員密碼設定

系統管理員帳戶具有更高的許可權。 內部部署企業或網域系統管理員無法透過 SSPR 重設其密碼。 內部部署系統管理員帳戶具有下列限制：

* 只能在其內部內部部署環境中變更其密碼。
* 絕對不能使用秘密問題和答案作為重設密碼的方法。

我們建議您不要將內部內部部署 Active Directory 管理帳戶與 Azure AD 同步。

### <a name="environments-with-multiple-identity-management-systems"></a>具有多個身分識別管理系統的環境

某些環境具有多個身分識別管理系統。 內部部署身分識別管理員（例如 Oracle AM 和 SiteMinder）需要與 AD 進行同步處理以取得密碼。 您可以使用像是密碼變更通知服務（PCNS）的工具，搭配 Microsoft Identity Manager （MIM）來執行此動作。 若要尋找更複雜案例的相關資訊，請參閱在[網域控制站上部署 MIM 密碼變更通知服務一](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)文。

## <a name="plan-testing-and-support"></a>規劃測試與支援

在您部署的每個階段，透過整個組織的初始試驗群組，確保結果如預期般運作。

### <a name="plan-testing"></a>規劃測試

若要確保您的部署如預期般運作，請規劃一組測試案例來驗證執行。 若要評估測試案例，您需要具有密碼的非系統管理員測試使用者。 如果您需要建立使用者，請參閱[將新的使用者新增至 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)。

下表包含有用的測試案例，您可以用來根據您的原則記錄組織預期的結果。
<br>


| 商業案例| 預期的結果 |
| - | - |
| SSPR 入口網站可從公司網路記憶體取| 由您的組織決定 |
| SSPR 入口網站可從公司網路外部存取| 由您的組織決定 |
| 當使用者未啟用密碼重設時，從瀏覽器重設使用者密碼| 使用者無法存取密碼重設流程 |
| 當使用者未註冊密碼重設時，從瀏覽器重設使用者密碼| 使用者無法存取密碼重設流程 |
| 使用者在強制執行密碼重設註冊時登入| 提示使用者註冊安全性資訊 |
| 當密碼重設註冊完成時，使用者登入| 提示使用者註冊安全性資訊 |
| 當使用者沒有授權時，可以存取 SSPR 入口網站| 可存取 |
| 從 Windows 10 Azure AD 加入或混合式 Azure AD 加入的裝置鎖定畫面重設使用者密碼| 使用者可以重設密碼 |
| SSPR 註冊和使用方式資料可供系統管理員近乎即時地使用| 可透過 audit 記錄取得 |

您也可以參考[完整的 Azure AD 自助式密碼重設試驗](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)。 在本教學課程中，您將在組織中啟用 SSPR 的試驗，並使用非系統管理員帳戶進行測試。

### <a name="plan-support"></a>規劃支援

雖然 SSPR 通常不會造成使用者的問題，但請務必準備支援人員來處理可能發生的問題。 雖然系統管理員可以透過 Azure AD 入口網站重設使用者的密碼，但最好是藉由自助支援程式來協助解決此問題。

若要讓支援小組成功，您可以根據您從使用者收到的問題來建立常見問題。 以下是一些範例：

| 案例| 描述 |
| - | - |
| 使用者未提供任何已註冊的驗證方法| 使用者嘗試重設其密碼，但沒有任何可供使用的驗證方法（例如：他們離開家裡的行動電話，且無法存取電子郵件） |
| 使用者未在其辦公室或行動電話上收到文字或呼叫| 使用者嘗試透過文字或呼叫來驗證其身分識別，但未收到文字/呼叫。 |
| 使用者無法存取密碼重設入口網站| 使用者想要重設其密碼，但未啟用密碼重設，且無法存取頁面來更新密碼。 |
| 使用者無法設定新密碼| 使用者在密碼重設流程期間完成驗證，但無法設定新密碼。 |
| 使用者在 Windows 10 裝置上看不到 [重設密碼] 連結| 使用者嘗試從 Windows 10 鎖定畫面重設密碼，但裝置未加入 Azure AD，或未啟用 Intune 裝置原則 |

### <a name="plan-rollback"></a>計畫復原

若要復原部署：

* 若為單一使用者，請從安全性群組中移除使用者 

* 若為群組，請從 SSPR 設定中移除群組

* 針對每個人，請停用 Azure AD 租使用者的 SSPR

## <a name="deploy-sspr"></a>部署 SSPR

部署之前，請確定您已完成下列作業：

1. 已建立並開始執行您的[通訊計畫](#plan-communications)。

1. 判斷適當的[設定](#plan-configuration)。

1. 已識別[試驗](#plan-a-pilot)和生產環境的使用者和群組。

1. [已決定](#plan-configuration)註冊和自助的設定。

1. 如果您有混合式環境，請[設定密碼回寫](#password-writeback)。


**您現在已經準備好部署 SSPR！**

如需設定下列領域的完整逐步指示，請參閱[啟用自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset)。

1. [驗證方法](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [註冊設定](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [通知設定](#notifications-settings)

1. [自訂設定](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [內部部署整合](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>在 Windows 中啟用 SSPR
對於執行 Windows 7、8、8.1 和10的電腦，您可以[讓使用者在 Windows 登入畫面重設其密碼](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>管理 SSPR

Azure AD 可以透過審核和報告，提供有關 SSPR 效能的其他資訊。

### <a name="password-management-activity-reports"></a>密碼管理活動報告 

您可以在 Azure 入口網站上使用預先建立的報告來測量 SSPR 效能。 如果您已適當地取得授權，則也可以建立自訂查詢。 如需詳細資訊，請參閱[Azure AD 密碼管理的報告選項](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  您必須是[全域管理員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)，而且必須加入宣告此資料才能收集給您的組織。 若要加入宣告，您必須至少造訪一次 Azure 入口網站上的 [報表] 索引標籤或 audit 記錄。 在那之前，不會為您的組織收集資料。

註冊和密碼重設的審查記錄可供使用30天。 如果您公司內的安全性審核需要較長的保留期，則需要將記錄匯出並取用至 SIEM 工具，例如[Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory)、Splunk 或 ArcSight。

![SSPR 報告螢幕擷取畫面](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>驗證方法-使用方式和見解

[使用方式和深入](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)解析可讓您瞭解 Azure MFA 和 SSPR 等功能的驗證方法如何在您的組織中運作。 這項報告功能可讓您的組織瞭解要註冊的方法，以及如何使用它們。

### <a name="troubleshoot"></a>疑難排解

* 請參閱針對[自助式密碼重設進行疑難排解](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* 遵循[密碼管理](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq)的常見問題 

### <a name="helpful-documentation"></a>有用的檔

* [驗證方法有哪些？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [運作方式： Azure AD 自助式密碼重設？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [自訂 Azure AD 的自助式密碼重設功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Azure Active Directory 中的密碼原則和限制](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [什麼是密碼回寫？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>後續步驟

* 若要開始部署 SSPR，請參閱[啟用 Azure AD 自助式密碼重設](tutorial-enable-sspr.md)

* [請考慮執行 Azure AD 密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [請考慮執行 Azure AD 智慧鎖定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)