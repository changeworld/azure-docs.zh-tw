---
title: 使用 Azure AD 規劃無密碼 authentication 部署
description: 瞭解如何規劃和部署 Azure Active Directory 無密碼 authentication 的執行
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4c06e53d3ddfb5f344a759e31d83cb22ce6d4d3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174131"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>規劃 Azure Active Directory 中的無密碼 authentication 部署

> [!NOTE]
> 若要建立此部署計畫的離線版本，請使用瀏覽器的 [列印至 PDF] 功能。

大部分的網路攻擊都是從遭入侵的使用者名稱和密碼開始。 組織會要求使用者使用下列其中一種方法，以嘗試對威脅進行計數器：

- 長密碼
- 複雜密碼
- 頻繁的密碼變更
- 多重要素驗證 (MFA)

Microsoft 的 [研究顯示](https://aka.ms/passwordguidance) ，這些工作會讓使用者煩惱並提高支援成本。 如需詳細資訊，請參閱 [您的 Pa $ $word 並不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)。

### <a name="benefits-of-passwordless-authentication"></a>無密碼 authentication 的優點

- **提高安全性**。 移除攻擊面的密碼，以降低網路釣魚和密碼噴灑攻擊的風險。
-  **更好的使用者體驗**。 讓使用者有便利的方式可以從任何地方存取資料。 提供在行動裝置上輕鬆存取應用程式和服務，例如 Outlook、OneDrive 或 Office。
-  **穩固的見解**。 利用健全的記錄和審核功能，深入瞭解使用者無密碼活動。

使用無密碼時，密碼會取代為您所擁有的內容，以及您所知的東西。 例如，Windows Hello 企業版可以使用生物特徵辨識手勢，例如臉部或指紋，或裝置特定的 PIN 不會透過網路傳輸。

## <a name="passwordless-authentication-methods"></a>無密碼驗證方法
Microsoft 提供三個無密碼驗證選項來涵蓋許多案例。 您可以使用這些方法來串聯：

- [Windows Hello 企業版](./concept-authentication-passwordless.md) 最適合其專用 Windows 電腦上的使用者。
- 使用 [FIDO2 安全性金鑰](./concept-authentication-passwordless.md) 登入的安全性金鑰特別適用于登入共用電腦的使用者，例如 kiosk 的使用方式，以及高度特殊許可權的身分識別。
- 使用 [Microsoft Authenticator 應用程式](./concept-authentication-passwordless.md) 的手機登入，對使用行動裝置的使用者提供無密碼選項很有用。 驗證器應用程式可讓使用者登入任何平臺或瀏覽器，將任何 iOS 或 Android 手機變成強式無密碼認證。 使用者可以藉由取得電話通知來登入、將畫面上顯示的數位與手機上的數位相符，然後使用其生物特徵辨識資料或 PIN 來確認。

### <a name="passwordless-authentication-scenarios"></a>無密碼驗證案例

Microsoft 的無密碼 authentication 方法可實現不同的案例。 請考慮您的組織需求、必要條件，以及每種驗證方法的功能，以選取您的無密碼 authentication 策略。 建議使用 Windows 10 裝置的每個組織都使用 Windows Hello 企業版。 然後，在其他案例中，使用 Microsoft Authenticator 應用程式) 或安全性金鑰來新增手機登入 (。

| 案例 | 電話驗證 | 安全性金鑰 | Windows Hello 企業版 |
| --- | --- | --- | --- |
| **電腦登入**： <br> 從指派的 Windows 10 裝置 | **否** | **是** <br> 使用生物特徵辨識，PIN | **是**<br>使用生物識別辨識和或 PIN |
| **電腦登入**： <br> 從共用 Windows 10 裝置 | **否** | **是** <br> 使用生物特徵辨識，PIN  | **否** |
| **Web 應用程式登入**： <br>從使用者專用的電腦 | **是** | **是** <br> 電腦登入會啟用對應用程式提供的單一登入 | **是**<br> 電腦登入會啟用對應用程式提供的單一登入 |
| **Web 應用程式登入**： <br> 從行動裝置或非 windows 裝置 | **是** | **否** | **否** |
| **電腦登入**： <br> 非 Windows 電腦 | **否** | **否** | **否** |

如需為您的組織選取最佳方法的詳細資訊，請參閱 [決定無密碼方法](./concept-authentication-passwordless.md#choose-a-passwordless-method)。

## <a name="prerequisites"></a>Prerequisites

在開始進行無密碼部署之前，組織必須符合下列必要條件：

| 必要條件 | 驗證器應用程式 | FIDO2 安全性金鑰 |
| --- | --- | --- |
| 已啟用[Azure AD Multi-Factor Authentication 和自助式密碼重設的組合註冊 (SSPR) ](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [使用者可以執行 Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md) | √ | √ |
| [使用者已註冊 Azure AD Multi-Factor Authentication 和 SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [使用者已註冊其行動裝置以 Azure Active Directory](../devices/overview.md) | √ |   |
| 使用支援的瀏覽器（例如 Microsoft Edge 或 Mozilla Firefox） Windows 10 1809 版或更高版本 <br>  (67 版或更高版本的) 。 <br> *Microsoft 建議的版本為1903或更高版本，以提供原生支援*。 |   | √ |
| 相容的 FIDO2 安全性金鑰。 確定您使用的是 [Microsoft 測試及驗證](./concept-authentication-passwordless.md) 的 FIDO2 安全性裝置，或其他相容的 FIDO2 安全性裝置。 |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Windows Hello 企業版的必要條件

Windows Hello 的必要條件高度取決於您要部署的是內部部署、混合式或僅限雲端的設定。 如需詳細資訊，請參閱 [Windows Hello 企業版的必要條件完整清單](/windows/security/identity-protection/hello-for-business/hello-identity-verification)。

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication

使用者會在 Azure AD Multi-Factor Authentication 註冊流程中註冊其無密碼方法。 具有使用者名稱和密碼的多重要素驗證以及另一個已註冊的方法，在某些案例中無法使用其電話或安全性金鑰時，可以用來作為備用。

### <a name="licensing"></a>授權 
無密碼 authentication 沒有額外的成本，不過某些必要條件可能需要 premium 訂用帳戶。 如需 [Azure Active Directory 授權頁面](https://azure.microsoft.com/pricing/details/active-directory/)中的詳細功能和授權資訊。 

## <a name="develop-a-plan"></a>開發方案

請考慮您的商務需求，以及每個驗證方法的使用案例。 然後選取最符合您需求的方法。

### <a name="use-cases"></a>使用案例

下表列出此專案期間要執行的使用案例。

| 區域 | 描述 |
| --- | --- |
| **存取** | 您可以從公司網路內部或外部的公司或個人裝置，使用無密碼登入。 |
| **稽核** | 系統管理員可使用資料，以近乎即時的方式進行審查。 <br> 使用量資料會每隔29天下載到公司系統，或使用 SIEM 工具。 |
| **監管** | 定義和監視使用者指派至適當驗證方法和相關聯群組的生命週期。 |
| **安全性** | 適當驗證方法的存取權是透過使用者和群組指派來控制。 <br> 只有授權的使用者可以使用無密碼登入。 |
| **效能** | 存取指派傳播時間軸記載和監視。 <br> 登入時間的測量是為了方便使用。 |
| **使用者體驗** | 使用者知道行動相容性。 <br> 使用者可以設定驗證器應用程式無密碼登入。 |
| **支援** | 使用者知道如何尋找無密碼登入問題的支援。 |

### <a name="engage-the-right-stakeholders"></a>包含正確的專案關係人

當技術專案失敗時，通常是因為對影響、結果和責任的預期不相符。 若要避免這些問題，請 [確定您已吸引適當的專案關係人](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) ，並且能瞭解專案中的專案關係人角色。

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功都非常重要。 主動溝通使用者的體驗將如何變更、何時會變更，以及如何在遇到問題時獲得支援。

您與終端使用者的通訊應該包含下列資訊：

- [啟用結合的安全性註冊體驗](howto-authentication-passwordless-phone.md)
- [下載 Microsoft Authenticator 應用程式](../user-help/user-help-auth-app-download-install.md)
- [在 Microsoft Authenticator 應用程式中註冊](howto-authentication-passwordless-phone.md)
- [使用您的電話登入](../user-help/user-help-auth-app-sign-in.md)

Microsoft 提供多重要素驗證 [通訊範本](https://aka.ms/mfatemplates)、Self-Service 密碼重設 (SSPR) [通訊範本](https://www.microsoft.com/download/details.aspx?id=56768)，以及可協助您進行通訊的 [使用者檔](../user-help/security-info-setup-signin.md) 。 選取該頁面的 **安全性資訊** 連結，即可將使用者傳送至 [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) 直接註冊。

### <a name="plan-to-pilot"></a>規劃試驗

當您部署無密碼 authentication 時，您應該先啟用一或多個試驗群組。 您可以特別為此用途 [建立群組](../fundamentals/active-directory-groups-create-azure-portal.md) 。 將參與試驗的使用者新增至群組。 然後，為選取的群組啟用新的無密碼驗證方法。

群組可以從內部部署目錄或 Azure AD 進行同步處理。 當您滿意試驗的結果之後，就可以為所有使用者切換無密碼 authentication。

請參閱 [部署計畫] 頁面上的 [試驗最佳做法](../fundamentals/active-directory-deployment-plans.md) 。

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 應用程式規劃無密碼 authentication

Microsoft Authenticator 的應用程式可從 Google Play 或 Apple App Store 免費下載。 [深入瞭解下載 Microsoft Authenticator 應用程式](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。 讓使用者下載 Microsoft Authenticator 應用程式。 並遵循指示來啟用手機登入。 

它會將任何 iOS 或 Android 手機變成強式無密碼認證。 使用者可以透過取得電話的通知、將畫面上顯示的數位與手機上的數位比對，然後使用生物特徵辨識或 PIN 來確認，以登入任何平臺或瀏覽器。 [查看 Microsoft Authenticator 應用程式運作方式的詳細資料](./concept-authentication-passwordless.md#microsoft-authenticator-app)。

![使用驗證器應用程式登入](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式的技術考慮

**AD FS 整合** -當使用者啟用 Microsoft Authenticator 無密碼認證時，該使用者的驗證會預設為傳送通知以供核准。 混合式租使用者中的使用者除非選取 [改為使用您的密碼]，否則無法將其導向至 ADFS 進行登入。 此程式也會略過任何內部部署的條件式存取原則和傳遞驗證流程。 但是，如果指定了 *login_hint* ，則會將使用者轉送到 ADFS，並略過使用無密碼認證的選項。

Azure AD 透過組織內部部署 Azure MFA 伺服器啟用多重要素驗證的 **Multi-Factor Authentication 伺服器** 端使用者，可以建立並使用單一無密碼手機登入認證。 如果使用者嘗試使用認證升級多個安裝 (5 個或更多) Microsoft Authenticator，這項變更可能會導致錯誤。

**裝置註冊** ：若要使用驗證器應用程式進行無密碼 authentication，裝置必須在 Azure AD 租使用者中註冊，且不能是共用裝置。 裝置只能在單一租使用者中註冊。 這項限制表示只有一個工作或學校帳戶支援使用驗證器應用程式進行手機登入。

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>使用 FIDO2 安全性金鑰來規劃無密碼 authentication
有三種類型的無密碼登入部署可用於安全性金鑰：

-    在支援的瀏覽器上 Azure Active Directory web 應用程式
-    Azure Active Directory 聯結 Windows 10 裝置
-    混合式 Azure Active Directory 已加入 Windows 10 裝置 (預覽) 
     -    提供雲端式和內部部署資源的存取權。 如需存取內部部署資源的詳細資訊，請參閱 [使用 FIDOP2 金鑰的 SSO 至內部部署資源](./howto-authentication-passwordless-security-key-on-premises.md)

您必須啟用 **相容的 FIDO2 安全性金鑰**。 Microsoft 宣佈 [與 FIDO2 金鑰廠商合作](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)。

**針對 Azure AD web 應用程式和已加入網域的 Windows Azure AD 裝置**：

-    使用支援的瀏覽器（例如 Microsoft Edge 或 Mozilla Firefox (67 版或更高版本的) ）來 Windows 10 1809 版或更高版本。 
-    Windows 10 版本1809支援 FIDO2 登入，而且可能需要部署 FIDO2 金鑰製造商的軟體。 建議您使用1903版或更新版本。 

**針對已加入混合式 Azure Active Directory 網域的裝置**： 
-    Windows 10 2004 版或更新版本
-    執行 Windows Server 2016 或2019的完整修補網域伺服器。
-    Azure AD Connect 的最新版本

如需完整的需求清單，請參閱 [使用 Azure Active Directory 啟用無密碼安全性金鑰登入 Windows 10 裝置](./howto-authentication-passwordless-security-key-windows.md#requirements)。


### <a name="security-key-life-cycle"></a>安全性金鑰生命週期

安全性金鑰可以存取您的資源，您應該規劃這些實體裝置的管理。

1. **金鑰散發**：規劃如何將金鑰布建到您的組織。 您可能會有集中布建程式，或允許終端使用者購買 FIDO 2.0 相容的金鑰。
1. **金鑰啟用**：終端使用者必須自行啟用安全性金鑰。 終端使用者會在中註冊其安全性金鑰 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) ，並在第一次使用時 (PIN 或生物特徵辨識) 啟用第二個因素。
1. **停用金鑰**：當安全性金鑰功能在預覽階段時，系統管理員無法從使用者帳戶移除金鑰。 使用者必須將它移除。 如果金鑰遺失或遭竊：
   1. 從已啟用無密碼 authentication 的任何群組移除使用者。
   1. 確認它們已移除金鑰做為驗證方法。
   1. 發出新的金鑰。 **金鑰取代**：使用者可以同時啟用兩個安全性金鑰。 取代安全性金鑰時，請確定使用者也已移除正在取代的金鑰。

### <a name="enable-windows-10-support"></a>啟用 Windows 10 支援

若要啟用 Windows 10 使用 FIDO2 安全性金鑰登入，必須啟用 Windows 10 中的認證提供者功能。 選擇下列其中之一：

- [使用 Intune 啟用認證提供者](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - 建議選擇 Intune 部署。
- [使用布建套件啟用認證提供者](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - 如果無法部署 Intune，系統管理員必須在每部機器上部署套件，以啟用認證提供者功能。 您可以使用下列其中一個選項來執行套件安裝：
      - 群組原則或 Configuration Manager
      - Windows 10 電腦上的本機安裝
- [使用群組原則啟用認證提供者](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - 僅支援已加入混合式 Azure AD 的裝置。

#### <a name="enable-on-premises-integration"></a>啟用內部部署整合

若要啟用內部部署資源的存取權，請遵循下列步驟來 [啟用無密碼安全性金鑰登入內部部署資源 (preview) ](howto-authentication-passwordless-security-key-on-premises.md)。

> [!IMPORTANT]
> 針對任何已加入混合式 Azure AD 的裝置，也必須完成這些步驟，才能使用 FIDO2 安全性金鑰進行 Windows 10 登入。

### <a name="register-security-keys"></a>註冊安全性金鑰

使用者必須在每個聯結的 Azure Active Directory Windows 10 機上註冊其安全性金鑰。

如需詳細資訊，請參閱 [使用者註冊及管理 FIDO2 安全性金鑰](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)。


## <a name="plan-auditing-security-and-testing"></a>規劃審核、安全性和測試
規劃符合您組織和合規性架構的審核是部署中不可或缺的一部分。

### <a name="auditing-passwordless"></a>審核無密碼

Azure AD 具有可提供技術和商業見解的報表。 讓您的商務和技術應用程式擁有者假設擁有權，並根據您組織的需求來取用這些報表。

Azure Active Directory 入口網站中的 [ **驗證** 方法] 區段可讓系統管理員啟用和管理無密碼認證的設定。

Azure AD 會在下列情況將專案新增至 audit 記錄：

- 系統管理員會在 [驗證方法] 區段中進行變更。
- 使用者會在 Azure Active Directory 內對其認證進行任何變更。

下表提供一般報表案例的一些範例：

|   | 管理風險 | 提高生產力 | 治理和合規性 |
| --- | --- | --- | --- |
| **報表類型** | 驗證方法-為合併安全性註冊註冊的使用者 | 驗證方法–註冊代理程式更新的使用者 | 登入：檢查誰正在存取租使用者，以及如何 |
| **可能的動作** | 尚未註冊的目標使用者 | 推動 Microsoft Authenticator 應用程式或安全性金鑰的採用 | 撤銷存取權或為系統管理員強制執行額外的安全性原則 |

**Azure AD 會保留30天的大部分審核資料** ，並透過 Azure 系統管理員入口網站或 API 提供資料供您下載到您的分析系統。 如果您需要較長的保留期，請在 SIEM 工具中匯出和取用記錄，例如 [Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk 或 Sumo 邏輯。 [深入瞭解如何查看您的存取和使用方式報表](../reports-monitoring/overview-reports.md)。

使用者可以藉由流覽至來註冊及管理其認證 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 。 此連結會將使用者導向透過合併的 SSPR/多重要素驗證註冊體驗啟用的使用者認證管理體驗。 Azure AD 記錄 FIDO2 安全性裝置的註冊，以及使用者的驗證方法變更。

### <a name="plan-security"></a>規劃安全性
在此首度發行計畫中，Microsoft 建議針對所有具特殊許可權的系統管理員帳戶啟用無密碼 authentication。

當使用者啟用或停用安全性金鑰上的帳戶，或重設其 Windows 10 電腦上安全性金鑰的第二個因素時，系統會將專案新增至安全性記錄檔，並在下列事件識別碼底下： *4670* 和 *5382*。

### <a name="plan-testing"></a>規劃測試

當您測試案例和採用時，在部署的每個階段，請確定結果如預期般運作。

#### <a name="testing-the-microsoft-authenticator-app"></a>測試 Microsoft Authenticator 應用程式

以下是使用 Microsoft Authenticator 應用程式進行無密碼驗證的範例測試案例：

| 案例 | 預期的結果 |
| --- | --- |
| 使用者可以註冊 Microsoft Authenticator 應用程式 | 使用者可以從 aka.ms/mysecurityinfo 註冊應用程式 |
| 使用者可以啟用手機登入 | 為工作帳戶設定的手機登入 |
| 使用者可以使用手機登入來存取應用程式 | 使用者會經歷手機登入流程，並到達應用程式。 |
| 在 Azure Active Directory 入口網站的 [驗證方法] 畫面中關閉 Microsoft Authenticator 無密碼登入，以測試回復手機登入註冊 | 先前啟用的使用者無法從 Microsoft Authenticator 使用無密碼登入。 |
| 從 Microsoft Authenticator 應用程式移除手機登入 | Microsoft Authenticator 的公司帳戶已無法再使用 |

#### <a name="testing-security-keys"></a>測試安全性金鑰

以下是使用安全性金鑰進行無密碼 authentication 的範例測試案例。

**無密碼 FIDO 登入已加入 Azure Active Directory 的 Windows 10 裝置**

| 案例 | 預期的結果 |
| --- | --- |
| 使用者可以註冊 FIDO2 裝置 (1809)  | 使用者可以在設定 > 帳戶 > 登入選項 > 安全性金鑰）註冊 FIDO2 裝置 |
| 使用者可以重設 FIDO2 裝置 (1809)  | 使用者可以使用製造商軟體重設 FIDO2 裝置 |
| 使用者可以使用 FIDO2 裝置登入 (1809)  | 使用者可以從登入視窗中選取安全性金鑰，並成功登入。 |
| 使用者可以註冊 FIDO2 裝置 (1903)  | 使用者可以在設定 > 帳戶 > 登入選項 > 安全性金鑰）中註冊 FIDO2 裝置 |
| 使用者可以重設 FIDO2 裝置 (1903)  | 使用者可以在設定 > 帳戶 > 登入選項 > 安全性金鑰）重設 FIDO2 裝置 |
| 使用者可以使用 FIDO2 裝置登入 (1903)  | 使用者可以從登入視窗中選取安全性金鑰，並成功登入。 |

**無密碼 FIDO 登入 Azure AD web 應用程式**

| 案例 | 預期的結果 |
| --- | --- |
| 使用者可以使用 Microsoft Edge 在 aka.ms/mysecurityinfo 註冊 FIDO2 裝置 | 註冊應該會成功 |
| 使用者可以使用 Firefox 在 aka.ms/mysecurityinfo 註冊 FIDO2 裝置 | 註冊應該會成功 |
| 使用者可以使用 FIDO2 裝置，使用 Microsoft Edge 登入 OneDrive online | 登入應該會成功 |
| 使用者可以使用 FIDO2 裝置，以 Firefox 登入 OneDrive online | 登入應該會成功 |
| 在 Azure Active Directory 入口網站的 [驗證方法] 視窗中關閉 FIDO2 安全性金鑰，以測試回復 FIDO2 裝置註冊 | 系統會提示使用者使用其安全性金鑰登入。 使用者將成功登入，並會顯示錯誤：「您的公司原則要求您使用不同的方法來登入」。 使用者接著應該可以選取不同的方法並成功登入。 關閉視窗並重新登入，確認它們沒有看到相同的錯誤訊息。 |

### <a name="plan-for-rollback"></a>規劃復原作業

雖然無密碼 authentication 是一項輕量的功能，對終端使用者的影響不大，但可能需要復原。

復原需要系統管理員登入 Azure Active Directory 入口網站，選取想要的強式驗證方法，並將 [啟用] 選項變更為 [ **否**]。 此程式會為所有使用者關閉無密碼功能。

已註冊 FIDO2 安全性裝置的使用者會在下一次登入時提示他們使用安全性裝置，然後看到下列錯誤：

![選擇不同的登入方式](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>部署無密碼 authentication 並進行疑難排解

遵循符合您所選方法的步驟。

### <a name="required-administrative-roles"></a>必要的系統管理角色

| Azure AD 角色 | 描述 |
| --- | --- |
| 全域管理員|具有最小許可權的角色，可執行結合的註冊體驗。 |
| 驗證系統管理員 | 具有最小許可權的角色可以執行和管理驗證方法。 |
| User | 在裝置上設定驗證器應用程式，或為 web 或 Windows 10 登入註冊安全性金鑰裝置的最低特殊許可權角色。 |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 應用程式部署手機登入

遵循本文中的步驟， [使用 Microsoft Authenticator 應用程式啟用無密碼登入](howto-authentication-passwordless-phone.md) ，以啟用 Microsoft Authenticator 應用程式作為您組織中的無密碼 authentication 方法。

### <a name="deploy-fido2-security-key-sign-in"></a>部署 FIDO2 安全性金鑰登入

遵循本文中的步驟，為 [Azure AD 啟用無密碼安全性金鑰登入](howto-authentication-passwordless-security-key.md) ，以啟用 FIDO2 安全性金鑰作為無密碼驗證方法。

### <a name="troubleshoot-phone-sign-in"></a>針對手機登入進行疑難排解

| 案例 | 解決方法 |
| --- | --- |
| 使用者無法執行合併的註冊。 | 確定已啟用 [合併註冊](concept-registration-mfa-sspr-combined.md) 。 |
| 使用者無法啟用手機登入驗證器應用程式。 | 確定使用者已在部署範圍內。 |
| 使用者不在無密碼 authentication 的範圍內，但會顯示為無法完成的無密碼登入選項。 | 當使用者在建立原則之前，在應用程式中啟用手機登入，就會發生這種情況。 <br> *若要啟用登入*：將使用者新增至已啟用無密碼登入的使用者範圍。 <br> *封鎖登入*：讓使用者從該應用程式移除其認證。 |

### <a name="troubleshoot-security-key-sign-in"></a>對安全性金鑰登入進行疑難排解

| 案例 | 解決方法 |
| --- | --- |
| 使用者無法執行合併的註冊。 | 確定已啟用 [合併註冊](concept-registration-mfa-sspr-combined.md) 。 |
| 使用者無法在其 [安全性設定](https://aka.ms/mysecurityinfo)中新增安全性金鑰。 | 確定已啟用 [安全性金鑰](howto-authentication-passwordless-security-key.md) 。 |
| 使用者無法在 Windows 10 登入選項中新增安全性金鑰。 | [確定 Windows 登入的安全性金鑰](./concept-authentication-passwordless.md) |
| **錯誤訊息**：我們偵測到此瀏覽器或作業系統不支援 FIDO2 安全性金鑰。 | 無密碼 FIDO2 安全性裝置只能在 Windows 10 1809 版或更高版本上，于支援的瀏覽器 (Microsoft Edge、Firefox 67 版) 中註冊。 |
| **錯誤訊息**：您的公司原則要求您使用不同的方法來登入。 | 不確定租使用者中是否已啟用安全性金鑰。 |
| 使用者無法在 Windows 10 版本1809上管理我的安全性金鑰 | 1809版要求您使用 FIDO2 金鑰廠商所提供的安全性金鑰管理軟體。 請洽詢廠商以尋求支援。 |
| 我覺得我的 FIDO2 安全性金鑰可能會有問題，我該如何進行測試。 | 流覽至 [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/) ，輸入測試帳戶的認證，插入可疑的安全性金鑰，選取 **+** 畫面右上方的按鈕，按一下 [建立]，然後完成建立程式。 如果此案例失敗，您的裝置可能會有問題。 |

## <a name="next-steps"></a>後續步驟

- [啟用無密碼安全性金鑰以登入 Azure AD](howto-authentication-passwordless-security-key.md)
- [使用 Microsoft Authenticator 應用程式啟用無密碼登入](howto-authentication-passwordless-phone.md)
- [深入瞭解 & insights 的驗證方法使用方式](howto-authentication-methods-usage-insights.md)