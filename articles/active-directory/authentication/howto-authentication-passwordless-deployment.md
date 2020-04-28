---
title: 使用 Azure AD 規劃無密碼 authentication 部署
description: 瞭解如何規劃和部署 Azure Active Directory 的無密碼 authentication 執行
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3ed549e51b911452bca7d4d4a16c7ef45594a8f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81451426"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>在 Azure Active Directory 中規劃無密碼 authentication 部署

> [!NOTE]
> 若要建立此部署計畫的離線版本，請使用瀏覽器的 [列印至 PDF] 功能。

大部分的網路攻擊都是以遭盜用的使用者名稱和密碼作為開頭。 組織會要求使用者使用下列其中一種方法，藉此來對抗威脅：

- 長密碼
- 複雜密碼
- 經常變更密碼
- 多重要素驗證 (MFA)

Microsoft 的[研究顯示](https://aka.ms/passwordguidance)，這些工作會對使用者帶來煩惱，並提升支援成本。 如需詳細資訊，請參閱[您的 Pa $ $word 不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)。

### <a name="benefits-of-passwordless-authentication"></a>無密碼 authentication 的優點

- **加強安全性**。 藉由移除密碼作為受攻擊面，降低網路釣魚和密碼噴灑攻擊的風險。
-  **更好的使用者體驗**。 提供使用者一個便利的方式，從任何地方存取資料。 讓您輕鬆存取應用程式和服務，例如行動裝置的 Outlook、OneDrive 或 Office。
-  **穩固的見解**。 透過健全的記錄和審核，取得使用者無密碼活動的深入解析。

使用無密碼時，密碼會取代為您所擁有的內容，以及您所知的東西。 例如，Windows Hello 企業版可以使用類似臉部或指紋的生物特徵辨識手勢，或不是透過網路傳輸的裝置特定 PIN。

## <a name="passwordless-authentication-methods"></a>無密碼驗證方法
Microsoft 提供三種無密碼的驗證選項，涵蓋許多案例。 這些方法可以串聯使用：

- [Windows Hello 企業版](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)最適合其專用 windows 電腦上的使用者。
- 具有[FIDO2 安全性](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)金鑰的安全性金鑰登入，特別適用于登入共用電腦（例如 kiosk）的使用者、限制使用電話的情況，以及高特殊許可權的身分識別。
- 使用[Microsoft Authenticator 應用程式](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)的手機登入，適用于為具有行動裝置的使用者提供無密碼選項。 驗證器應用程式可讓使用者登入任何平臺或瀏覽器，以將任何 iOS 或 Android 手機轉換成強式的無密碼認證。 使用者可以透過取得電話的通知來登入，將畫面上顯示的數位與電話上的號碼進行比對，然後使用其生物識別資料或 PIN 來確認。

### <a name="passwordless-authentication-scenarios"></a>無密碼驗證案例

Microsoft 的無密碼 authentication 方法可啟用不同的案例。 請考慮您的組織需求、必要條件，以及每個驗證方法的功能，以選取您的無密碼 authentication 策略。 我們建議使用 Windows 10 裝置的每個組織都使用 Windows Hello 企業版。 然後，為其他案例新增電話登入（使用 Microsoft Authenticator 應用程式）或安全性金鑰。

| 狀況 | 電話驗證 | 安全性金鑰 | Windows Hello 企業版 |
| --- | --- | --- | --- |
| **電腦登入**： <br> 從指派的 Windows 10 裝置 | **否** | **是** <br> 使用生物特徵辨識，PIN | **是**<br>使用生物識別辨識和或 PIN |
| **電腦登入**： <br> 從共用的 Windows 10 裝置 | **否** | **是** <br> 使用生物特徵辨識，PIN  | **否** |
| **Web 應用程式登入**： <br>從使用者專用的電腦 | **是** | **是** <br> 提供給應用程式的單一登入已由電腦登入啟用 | **是**<br> 提供給應用程式的單一登入已由電腦登入啟用 |
| **Web 應用程式登入**： <br> 從行動或非 windows 裝置 | **是** | **否** | **否** |
| **電腦登入**： <br> 非 Windows 電腦 | **否** | **否** | **否** |

如需為您的組織選取最佳方法的相關資訊，請參閱[決定無密碼方法](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#choose-a-passwordless-method)。

## <a name="prerequisites"></a>Prerequisites

在開始進行無密碼部署之前，組織必須符合下列必要條件：

| 必要條件 | 驗證器應用程式 | FIDO2 安全性金鑰 |
| --- | --- | --- |
| 已啟用[Azure 多重要素驗證和自助式密碼重設（SSPR）的合併註冊](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [使用者可以執行 Azure 多重要素驗證](howto-mfa-getstarted.md) | √ | √ |
| [使用者已註冊 Azure 多因素驗證和 SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [使用者已註冊其行動裝置以 Azure Active Directory](../devices/overview.md) | √ |   |
| 使用支援的瀏覽器（例如 Microsoft Edge 或 Mozilla Firefox）的 Windows 10 1809 版或更高版本 <br> （67版或更高版本）。 <br> *Microsoft 建議採用1903版或更高版本來提供原生支援*。 |   | √ |
| 相容的 FIDO2 安全性金鑰。 請確定您使用的是[Microsoft 測試和驗證](howto-authentication-passwordless-enable.md)的 FIDO2 安全性裝置，或其他相容的 FIDO2 安全性裝置。 |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Windows Hello 企業版的必要條件

Windows Hello 的必要條件非常依賴您是部署在內部部署、混合式或僅限雲端的設定中。 如需詳細資訊，請參閱[Windows Hello 企業版的必要條件完整清單](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)。

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

使用者會將其無密碼方法註冊為 Azure 多因素驗證註冊流程的一部分。 使用使用者名稱和密碼的多重要素驗證以及另一個已註冊的方法，可以在某些情況下使用其電話或安全性金鑰做為回復。

### <a name="licensing"></a>授權 
無密碼 authentication 不會產生額外費用，不過某些必要條件可能需要 premium 訂用帳戶。 如需[Azure Active Directory 授權] 頁面](https://azure.microsoft.com/pricing/details/active-directory/)中的詳細功能與授權資訊。 

## <a name="develop-a-plan"></a>開發方案

請考慮您的商務需求，以及每個驗證方法的使用案例。 然後選取最符合您需求的方法。

### <a name="use-cases"></a>使用案例

下表概述此專案期間要執行的使用案例。

| 區域 | 描述 |
| --- | --- |
| **存取** | 從公司網路內部或外部的公司或個人裝置，即可使用無密碼登入。 |
| **稽核** | 使用方式資料可供系統管理員近乎即時地進行審核。 <br> 使用量資料會至少每隔29天下載到公司系統，或使用 SIEM 工具。 |
| **控管** | 定義和監視適當驗證方法和相關聯群組之使用者指派的生命週期。 |
| **安全性** | 存取適當的驗證方法是透過使用者和群組指派來控制。 <br> 只有授權的使用者可以使用無密碼登入。 |
| **效能** | 已記載並監視存取指派傳播時間軸。 <br> 登入時間是為了方便使用而測量。 |
| **使用者體驗** | 使用者瞭解行動相容性。 <br> 使用者可以設定驗證器應用程式無密碼登入。 |
| **支援** | 使用者知道如何尋找無密碼登入問題的支援。 |

### <a name="engage-the-right-stakeholders"></a>參與適當的專案關係人

當技術專案失敗時，通常是因為對影響、結果和責任的預期不相符。 若要避免這些錯誤，請[確定您正在吸引正確的專案關係人](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)，而且已充分瞭解專案中的專案關係人角色。

### <a name="plan-communications"></a>規劃溝通

通訊對於任何新服務的成功非常重要。 主動溝通使用者的經驗如何改變、何時會變更，以及如何在遇到問題時取得支援。

您對終端使用者的通訊應該包含下列資訊：

- [啟用結合的安全性註冊體驗](howto-authentication-passwordless-phone.md)
- [下載 Microsoft Authenticator 應用程式](../user-help/user-help-auth-app-download-install.md)
- [在 Microsoft Authenticator 應用程式中註冊](howto-authentication-passwordless-phone.md)
- [使用您的電話登入](../user-help/user-help-auth-app-sign-in.md)

Microsoft 提供多重要素驗證[通訊範本](https://aka.ms/mfatemplates)、自助式密碼重設（SSPR）[通訊範本](https://www.microsoft.com/download/details.aspx?id=56768)，以及[使用者檔](../user-help/security-info-setup-signin.md)，以協助草擬您的通訊。 您可以透過選取該[https://myprofile.microsoft.com](https://myprofile.microsoft.com/)頁面上的**安全性資訊**連結，將使用者直接註冊。

### <a name="plan-to-pilot"></a>規劃試驗

當您部署無密碼 authentication 時，您應該先啟用一或多個試驗群組。 您可以特別針對此用途[建立群組](../fundamentals/active-directory-groups-create-azure-portal.md)。 將參與試驗的使用者新增至群組。 然後，針對選取的群組啟用新的無密碼驗證方法。

群組可以從內部部署目錄或從 Azure AD 進行同步處理。 當您滿意試驗結果之後，就可以針對所有使用者切換無密碼 authentication。

請參閱部署計畫頁面上[試驗的最佳做法](https://aka.ms/deploymentplans)。

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 應用程式規劃無密碼 authentication

Microsoft Authenticator 應用程式可從 Google Play 或 Apple App Store 免費下載。 [深入瞭解如何下載 Microsoft Authenticator 應用程式](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。 讓使用者下載 Microsoft Authenticator 應用程式。 並遵循指示來啟用手機登入。 

它會將任何 iOS 或 Android 手機轉換成強式的無密碼認證。 使用者可以藉由取得電話通知來登入任何平臺或瀏覽器，比對螢幕上顯示的數位與電話上的號碼，然後使用生物識別或 PIN 來確認。 [請參閱 Microsoft Authenticator 應用程式運作方式的詳細資料](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#microsoft-authenticator-app)。

![使用驗證器應用程式登入](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式的技術考慮

**AD FS 整合**-當使用者啟用 Microsoft Authenticator 無密碼認證時，該使用者的驗證會預設為傳送通知以供核准。 除非在混合式租使用者中選取 [改用您的密碼]，否則無法將其導向至 ADFS 進行登入。 此程式也會略過任何內部部署的條件式存取原則，以及傳遞驗證流程。 不過，如果指定了*login_hint* ，使用者會轉送到 ADFS，並略過使用無密碼認證的選項。

**Azure 多因素驗證服務器**-透過組織的內部部署 azure MFA Server 啟用多重要素驗證的使用者可以建立及使用單一無密碼電話登入認證。 如果使用者嘗試升級具有認證之 Microsoft Authenticator 的多個安裝（5個以上），這項變更可能會導致錯誤。

**裝置註冊**-若要使用驗證器應用程式進行無密碼驗證，裝置必須在 Azure AD 租使用者中註冊，而且不能是共用裝置。 裝置只能在單一租使用者中註冊。 此限制表示，只有一個公司或學校帳戶支援使用驗證器應用程式進行手機登入。

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>使用 FIDO2 安全性金鑰來規劃無密碼 authentication
有三種類型的無密碼登入部署可供安全性金鑰使用：

-    在支援的瀏覽器上 Azure Active Directory web 應用程式
-    Azure Active Directory 加入的 Windows 10 裝置
-    混合式 Azure Active Directory 加入的 Windows 10 裝置（預覽）
     -    提供雲端式和內部部署資源的存取權。 如需存取內部部署資源的詳細資訊，請參閱[使用 FIDOP2 金鑰的 SSO 至內部部署資源](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)

您必須啟用**相容的 FIDO2 安全性金鑰**。 Microsoft 宣佈[與 FIDO2 重要廠商合作的重要關係](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)。

**針對 Azure AD web 應用程式和 Azure AD 已加入 Windows 的裝置**：

-    Windows 10 1809 版或更高版本，使用支援的瀏覽器，例如 Microsoft Edge 或 Mozilla Firefox （67版或更新版本）。 
-    Windows 10 1809 版支援 FIDO2 登入，而且可能需要部署 FIDO2 金鑰製造商的軟體。 建議您使用1903版或更新版本。 

**針對已加入混合式 Azure Active Directory 網域的裝置**： 
-    Windows 10 Insider 組建18945或更新版本
-    已完整修補執行 Windows Server 2016 或2019的網域伺服器。
-    最新版本的 Azure AD Connect

如需完整的需求清單，請參閱[使用 Azure Active Directory 啟用無密碼安全性金鑰登入 Windows 10 裝置](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements)。


### <a name="security-key-life-cycle"></a>安全性金鑰生命週期

安全性金鑰可讓您存取您的資源，而且您應該規劃這些實體裝置的管理。

1. **金鑰散發**：規劃如何將金鑰布建到您的組織。 您可能有集中的布建程式，或允許終端使用者購買 FIDO 2.0 相容的金鑰。
1. **金鑰啟用**：終端使用者必須自行啟動安全性金鑰。 終端使用者在[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)註冊其安全性金鑰，並在第一次使用時啟用第二個要素（PIN 或生物識別）。
1. **停用金鑰**：雖然安全性金鑰功能是在預覽階段，但系統管理員無法從使用者帳戶中移除金鑰。 使用者必須將它移除。 如果遺失或遭竊金鑰：
   1. 從啟用無密碼 authentication 的任何群組中移除使用者。
   1. 確認他們已移除金鑰做為驗證方法。
   1. 發出新的金鑰。 **金鑰取代**：使用者可以同時啟用兩個安全性金鑰。 取代安全性金鑰時，請確定使用者也已移除要取代的金鑰。

### <a name="enable-windows-10-support"></a>啟用 Windows 10 支援

使用 FIDO2 安全性金鑰啟用 Windows 10 登入需要啟用 Windows 10 中的認證提供者功能。 選擇下列其中之一：

- [使用 Intune 啟用認證提供者](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Intune 部署是建議的選項。
- [啟用具有布建套件的認證提供者](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - 如果無法部署 Intune，系統管理員必須在每部電腦上部署套件，以啟用認證提供者功能。 您可以使用下列其中一個選項來執行封裝安裝：
      - 群組原則或 Configuration Manager
      - 在 Windows 10 電腦上進行本機安裝
- [啟用群組原則的認證提供者](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - 僅支援混合式 Azure AD 已加入裝置。

#### <a name="enable-on-premises-integration"></a>啟用內部部署整合

若要啟用內部部署資源的存取權，請遵循[啟用無密碼安全性金鑰登入內部部署資源（預覽）](howto-authentication-passwordless-security-key-on-premises.md)的步驟。

> [!IMPORTANT]
> 您也必須針對任何已加入混合式 Azure AD 的裝置完成這些步驟，才能使用 FIDO2 的安全性金鑰進行 Windows 10 登入。

### <a name="register-security-keys"></a>註冊安全性金鑰

使用者必須在每個已加入 Windows 10 電腦的 Azure Active Directory 上註冊其安全性金鑰。

如需詳細資訊，請參閱[FIDO2 安全性金鑰的使用者註冊與管理](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)。


## <a name="plan-auditing-security-and-testing"></a>規劃審核、安全性和測試
規劃符合您組織和合規性架構的審核是部署不可或缺的一部分。

### <a name="auditing-passwordless"></a>審核無密碼

Azure AD 具有可提供技術和商務深入解析的報表。 讓您的商務和技術應用程式擁有者取得的擁有權，並根據貴組織的需求來取用這些報告。

Azure Active Directory 入口網站中的 [**驗證**方法] 區段，系統管理員可以在其中啟用和管理無密碼認證的設定。

Azure AD 會在下列情況將專案新增至 audit 記錄檔：

- 系統管理員會在 [驗證方法] 區段中進行變更。
- 使用者會對其在 Azure Active Directory 內的認證進行任何變更。

下表提供一般報告案例的一些範例：

|   | 管理風險 | 提高生產力 | 治理和合規性 |
| --- | --- | --- | --- |
| **報表類型** | 驗證方法-已註冊結合安全性註冊的使用者 | 驗證方法–已註冊代理程式更新的使用者 | 登入：審查誰正在存取租使用者，以及如何 |
| **可能的動作** | 目標使用者尚未註冊 | 推動採用 Microsoft Authenticator 應用程式或安全性金鑰 | 撤銷存取權或強制執行系統管理員的其他安全性原則 |

**Azure AD 會將大部分的審核資料保留30天**，並透過 Azure 系統管理員入口網站或 API 提供資料，以供您下載至您的分析系統。 如果您需要較長的保留期，請在 SIEM 工具（例如[Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk 或 Sumo 邏輯）中匯出和取用記錄。 [深入瞭解如何查看您的存取和使用方式報表](../reports-monitoring/overview-reports.md)。

使用者可以藉由流覽至來[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)註冊和管理其認證。 此連結會將使用者引導至透過結合的 SSPR/多重要素驗證註冊體驗來啟用的使用者認證管理體驗。 Azure AD 記錄 FIDO2 安全性裝置的註冊，以及使用者對驗證方法所做的變更。

### <a name="plan-security"></a>規劃安全性
在此首度發行計畫中，Microsoft 建議針對所有特殊許可權的系統管理員帳戶啟用無密碼 authentication。

當使用者啟用或停用安全性金鑰上的帳戶，或在其 Windows 10 電腦上重設安全性金鑰的第二個因素時，會將專案新增至安全性記錄檔，並在下列事件識別碼底下： *4670*和*5382*。

### <a name="plan-testing"></a>規劃測試

當您測試案例和採用時，在部署的每個階段中，請確定結果如預期般運作。

#### <a name="testing-the-microsoft-authenticator-app"></a>測試 Microsoft Authenticator 應用程式

以下是使用 Microsoft Authenticator 應用程式進行無密碼驗證的範例測試案例：

| 狀況 | 預期的結果 |
| --- | --- |
| 使用者可以註冊 Microsoft Authenticator 應用程式 | 使用者可以從 aka.ms/mysecurityinfo 註冊應用程式 |
| 使用者可以啟用手機登入 | 已針對公司帳戶設定電話登入 |
| 使用者可以使用手機登入來存取應用程式 | 使用者經歷了電話登入流程，並觸及應用程式。 |
| 在 Azure Active Directory 入口網站的 [驗證方法] 畫面中關閉 Microsoft Authenticator 無密碼登入，以測試復原電話登入註冊 | 先前啟用的使用者無法從 Microsoft Authenticator 使用無密碼登入。 |
| 正在從 Microsoft Authenticator 應用程式移除手機登入 | Microsoft Authenticator 上不再提供工作帳戶 |

#### <a name="testing-security-keys"></a>測試安全性金鑰

以下是使用安全性金鑰進行無密碼驗證的範例測試案例。

**無密碼 FIDO 登入 Azure Active Directory 聯結的 Windows 10 裝置**

| 狀況 | 預期的結果 |
| --- | --- |
| 使用者可以註冊 FIDO2 裝置（1809） | 使用者可以在 [設定] > 帳戶] > 登入選項 > 安全性金鑰）註冊 FIDO2 裝置 |
| 使用者可以重設 FIDO2 裝置（1809） | 使用者可以使用製造商軟體重設 FIDO2 裝置 |
| 使用者可以使用 FIDO2 裝置登入（1809） | 使用者可以從登入視窗中選取安全性金鑰，並成功登入。 |
| 使用者可以註冊 FIDO2 裝置（1903） | 使用者可以在設定 > 帳戶 > 登入選項 > 安全性金鑰）註冊 FIDO2 裝置 |
| 使用者可以重設 FIDO2 裝置（1903） | 使用者可以在設定 > 帳戶 > 登入選項 > 安全性金鑰）重設 FIDO2 裝置 |
| 使用者可以使用 FIDO2 裝置登入（1903） | 使用者可以從登入視窗中選取安全性金鑰，並成功登入。 |

**無密碼 FIDO 登入以 Azure AD web 應用程式**

| 狀況 | 預期的結果 |
| --- | --- |
| 使用者可以使用 Microsoft Edge 在 aka.ms/mysecurityinfo 上註冊 FIDO2 裝置 | 註冊應會成功 |
| 使用者可以使用 Firefox 在 aka.ms/mysecurityinfo 上註冊 FIDO2 裝置 | 註冊應會成功 |
| 使用者可以使用 Microsoft Edge，透過 FIDO2 裝置登入 OneDrive online | 登入應會成功 |
| 使用者可以使用 Firefox 以 FIDO2 裝置登入 OneDrive online | 登入應會成功 |
| 在 Azure Active Directory 入口網站的 [驗證方法] 視窗中關閉 FIDO2 安全性金鑰，以測試回復 FIDO2 裝置註冊 | 系統會提示使用者使用其安全性金鑰登入。 使用者將成功登入，而且會顯示錯誤：「您的公司原則要求您使用不同的方法來登入」。 然後，使用者應該能夠選取不同的方法並成功登入。 關閉視窗並再次登入，以確認它們沒有看到相同的錯誤訊息。 |

### <a name="plan-for-rollback"></a>規劃復原作業

雖然無密碼 authentication 是輕量的功能，對終端使用者的影響最小，但可能需要復原。

復原時，系統管理員必須登入 Azure Active Directory 入口網站、選取所需的增強式驗證方法，並將 [啟用] 選項變更為 [**否**]。 此程式會關閉所有使用者的無密碼功能。

已註冊 FIDO2 安全性裝置的使用者，會在下次登入時提示他們使用安全性裝置，然後看到下列錯誤：

![選擇不同的登入方式](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>部署和疑難排解無密碼 authentication

遵循符合您選擇之方法的步驟。

### <a name="required-administrative-roles"></a>必要的系統管理角色

| Azure AD 角色 | 描述 |
| --- | --- |
| 全域管理員|最低特殊許可權角色能夠執行合併的註冊體驗。 |
| 驗證系統管理員 | 最低特殊許可權角色能夠執行和管理驗證方法。 |
| User | 在裝置上設定驗證器應用程式，或註冊 web 或 Windows 10 登入的安全性金鑰裝置的最低特殊許可權角色。 |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 應用程式部署手機登入

遵循使用[Microsoft Authenticator 應用程式啟用無密碼登入](howto-authentication-passwordless-phone.md)一文中的步驟，讓 Microsoft Authenticator 應用程式成為貴組織中的無密碼驗證方法。

### <a name="deploy-fido2-security-key-sign-in"></a>部署 FIDO2 安全性金鑰登入

依照[Azure AD 的啟用無密碼安全性金鑰登入](howto-authentication-passwordless-security-key.md)一文中的步驟，啟用 FIDO2 安全性金鑰做為無密碼驗證方法。

### <a name="troubleshoot-phone-sign-in"></a>針對手機登入進行疑難排解

| 狀況 | 解決方法 |
| --- | --- |
| 使用者無法執行合併的註冊。 | 請確定已啟用[合併註冊](concept-registration-mfa-sspr-combined.md)。 |
| 使用者無法啟用手機登入驗證器應用程式。 | 請確定使用者在部署範圍內。 |
| 使用者不在無密碼 authentication 的範圍內，而是以無密碼登入選項呈現，其無法完成。 | 當使用者在建立原則之前于應用程式中啟用手機登入時，就會發生此情況。 <br> *若要啟用登入*：將使用者新增至已啟用無密碼登入的使用者範圍。 <br> *若要封鎖登入*：讓使用者從該應用程式移除其認證。 |

### <a name="troubleshoot-security-key-sign-in"></a>對安全性金鑰登入進行疑難排解

| 狀況 | 解決方法 |
| --- | --- |
| 使用者無法執行合併的註冊。 | 請確定已啟用[合併註冊](concept-registration-mfa-sspr-combined.md)。 |
| 使用者無法在其[安全性設定](https://aka.ms/mysecurityinfo)中新增安全性金鑰。 | 請確定已啟用[安全性金鑰](howto-authentication-passwordless-security-key.md)。 |
| 使用者無法在 Windows 10 登入選項中新增安全性金鑰。 | [確定 Windows 登入的安全性金鑰](howto-authentication-passwordless-enable.md) |
| **錯誤訊息**：我們偵測到此瀏覽器或作業系統不支援 FIDO2 安全性金鑰。 | 無密碼 FIDO2 安全性裝置只能在 Windows 10 1809 版或更新版本上的支援瀏覽器（Microsoft Edge、Firefox 67 版）中註冊。 |
| **錯誤訊息**：您的公司原則要求您使用不同的方法來登入。 | 不確定已在租使用者中啟用安全性金鑰。 |
| 使用者無法在 Windows 10 版本1809上管理我的安全性金鑰 | 1809版需要您使用 FIDO2 金鑰廠商所提供的安全性金鑰管理軟體。 請洽詢廠商尋求支援。 |
| 我認為我的 FIDO2 安全性金鑰可能會有問題，我該如何進行測試。 | 流覽至[https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)，輸入測試帳戶的認證，插入可疑的安全性金鑰，選取畫面右上角**+** 的按鈕，按一下 [建立]，然後完成建立程式。 如果此案例失敗，您的裝置可能會有問題。 |

## <a name="next-steps"></a>後續步驟

- [啟用 Azure AD 登入的無密碼安全性金鑰](howto-authentication-passwordless-security-key.md)
- [啟用使用 Microsoft Authenticator 應用程式的無密碼登入](howto-authentication-passwordless-phone.md)
- [深入瞭解如何使用 & 深入解析的驗證方法](howto-authentication-methods-usage-insights.md)

