---
title: 使用 Azure AD 規劃無密碼身份驗證部署
description: 瞭解如何規劃和部署 Azure 活動目錄無密碼身份驗證實現
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
ms.openlocfilehash: 6c9933e75a39af43af9e2745d5f7732d40027b34
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582481"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>在 Azure 活動目錄中規劃無密碼身份驗證部署

> [!NOTE]
> 要創建此部署計畫的離線版本,請使用瀏覽器的「列印到 PDF」功能。

大多數網路攻擊都從受攻擊的使用者名和密碼開始。 組織試圖通過要求使用者使用以下方法之一來應對威脅:

- 長密碼
- 複雜密碼
- 頻繁變更密碼
- 多重要素驗證 (MFA)

微軟[的研究表明,](https://aka.ms/passwordguidance)這些努力惹惱了使用者,並推高了支援成本。 有關詳細資訊,請參閱您的[Pa$$word並不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)。

### <a name="benefits-of-passwordless-authentication"></a>沒有密碼認證的好處

- **加強安全**。 刪除密碼作為攻擊面,從而降低網路釣魚和密碼噴射攻擊的風險。
-  **更好的使用者體驗**。 為使用者提供從任何位置訪問數據的便捷方式。 在移動時輕鬆存取應用程式和服務,如Outlook、OneDrive或Office。
-  **可靠的見解**。 通過強大的日誌記錄和審核,深入瞭解使用者無密碼活動。

密碼後,密碼將被您擁有的東西以及您知道的東西替換。 例如,適用於企業的 Windows Hello 可以使用面部或指紋等生物辨識手勢,或者不使用網路傳輸的設備特定 PIN。

## <a name="passwordless-authentication-methods"></a>沒有密碼認證方法
Microsoft 提供了三個無密碼身份驗證選項,涵蓋許多方案。 這些方法可以串聯使用:

- [適用於「企業」的 Windows](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)最適合其專用 Windows 電腦上的使用者。
- 使用[FIDO2 安全金鑰](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)的安全金鑰登錄對於登入到共享電腦(如自助服務終端)的使用者、在電話使用受限的情況下以及高度特權標識的使用者特別有用。
- 使用[Microsoft 身份驗證器應用](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)輸入電話對於向使用行動裝置的使用者提供無密碼選項非常有用。 身份驗證器應用允許用戶登錄到任何平臺或瀏覽器,將任何 iOS 或 Android 手機轉換為強大的無密碼認證。 使用者登錄時,會收到手機的通知,將螢幕上顯示的號碼與手機上的號碼匹配,然後使用其生物識別數據或 PIN 進行確認。

### <a name="passwordless-authentication-scenarios"></a>沒有密碼身份驗證方案

微軟的無密碼身份驗證方法支援不同的方案。 考慮組織需求、先決條件以及每個身份驗證方法的功能,以選擇無密碼身份驗證策略。 我們建議每個使用 Windows 10 設備的組織都使用適用於企業的 Windows Hello。 然後,添加電話登錄(使用Microsoft身份驗證器應用)或安全密鑰以用於其他方案。

| 狀況 | 電話身份驗證 | 安全金鑰 | Windows Hello 企業版 |
| --- | --- | --- | --- |
| **電腦登入**: <br> 從配置的 Windows 10 裝置 | **否** | **是** <br> 使用生物識別、PIN | **是**<br>帶生物辨識與/或 PIN |
| **電腦登入**: <br> 從分享 Windows 10 裝置 | **否** | **是** <br> 使用生物識別、PIN  | **否** |
| **Web 應用登入**: <br>來自使用者專用電腦 | **是** | **是** <br> 電腦登入啟用應用的單一登入 | **是**<br> 電腦登入啟用應用的單一登入 |
| **Web 應用登入**: <br> 從移動或非視窗裝置 | **是** | **否** | **否** |
| **電腦登入**: <br> 非視窗電腦 | **否** | **否** | **否** |

有關為組織選擇最佳方法的資訊,請參閱[決定無密碼方法](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#choose-a-passwordless-method)。

## <a name="prerequisites"></a>Prerequisites

在開始無密碼部署之前,組織必須滿足以下先決條件:

| 必要條件 | 驗證器應用程式 | FIDO2 安全金鑰 |
| --- | --- | --- |
| 開啟[Azure 多重身份驗證與自助服務密碼重置 (SSPR) 的組合註冊](howto-registration-mfa-sspr-combined.md)(預覽功能) | √ | √ |
| [使用者可以執行 Azure 多重身份驗證](howto-mfa-getstarted.md) | √ | √ |
| [使用者已註冊 Azure 多重身份驗證與 SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [使用者已將行動裝置註冊到 Azure 的動作目錄](../devices/overview.md) | √ |   |
| Windows 10 版本 1809 或更高版本使用支援的瀏覽器,如微軟邊緣或 Mozilla Firefox <br> (版本 67 或更高)。 <br> *微軟推薦版本1903或更高版本為本機支援*。 |   | √ |
| 相容的 FIDO2 安全金鑰。 確保您使用的是經過 Microsoft[測試和驗證的](howto-authentication-passwordless-enable.md)FIDO2 安全設備或其他相容的 FIDO2 安全設備。 |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>企業 Windows Hello 的先決條件

Windows Hello 的先決條件在很大程度上取決於您是在本地配置、混合配置還是僅雲配置中部署。 有關詳細資訊,請參閱[適用於企業的 Windows Hello 的先決條件的完整清單](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)。

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

使用者將其無密碼方法註冊為 Azure 多重身份驗證註冊流的一部分。 在某些情況下,使用使用者名和密碼以及另一個已註冊方法的多重身份驗證可用作回退,以防在某些情況下無法使用其電話或安全密鑰。

### <a name="licensing"></a>授權 
無密碼身份驗證沒有額外費用,儘管某些先決條件可能需要高級訂閱。 有關[Azure 活動目錄許可頁](https://azure.microsoft.com/pricing/details/active-directory/)中的詳細資訊和許可資訊。 

## <a name="develop-a-plan"></a>制定計劃

考慮您的業務需要和每種身份驗證方法的用例。 然後選擇最適合您需求的方法。

### <a name="use-cases"></a>使用案例

下表概述了在此專案期間要實現的用例。

| 區域 | 描述 |
| --- | --- |
| **存取** | 無密碼登錄可從公司網路內外的公司或個人設備獲得。 |
| **稽核** | 使用數據可供管理員近乎實時審核。 <br> 使用數據至少每 29 天下載一次到公司系統中,或者使用 SIEM 工具。 |
| **控管** | 定義和監視使用者分配到適當身份驗證方法和相關組的生命週期。 |
| **安全性** | 通過使用者和組分配控制對適當身份驗證方法的訪問。 <br> 只有經過授權的使用者才能使用無密碼登錄。 |
| **效能** | 記錄和監視訪問分配傳播時間線。 <br> 為了便於使用,測量登錄時間。 |
| **使用者體驗** | 使用者知道移動相容性。 <br> 用戶可以配置身份驗證器應用無密碼登錄。 |
| **支援** | 使用者知道如何查找對無密碼登錄問題的支援。 |

### <a name="engage-the-right-stakeholders"></a>讓合適的利益相關者參與進來

當技術項目失敗時,通常是由於對影響、結果和責任的期望不匹配。 為了避免這些陷阱,[請確保您與合適的利益相關者接洽](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders),並確保專案中的利益相關者角色得到充分理解。

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功都至關重要。 主動溝通用戶體驗將如何變化、何時更改,以及遇到問題時如何獲得支援。

您與最終使用者的通訊應包括以下資訊:

- [開啟群組安全註冊體驗](howto-authentication-passwordless-phone.md)
- [下載微軟身分驗證器應用程式](../user-help/user-help-auth-app-download-install.md)
- [在 Microsoft 身份驗證器應用程式中註冊](howto-authentication-passwordless-phone.md)
- [使用手機登入](../user-help/user-help-auth-app-sign-in.md)

Microsoft 提供多重身份驗證[通訊範本](https://aka.ms/mfatemplates)、自助服務密碼重置 (SSPR)[通信範本](https://www.microsoft.com/download/details.aspx?id=56768)和[最終使用者文檔](../user-help/security-info-setup-signin.md),以説明起草通信。 您可以通過選擇該頁面[https://myprofile.microsoft.com](https://myprofile.microsoft.com/)上**的安全資訊**連結將使用者發送到直接註冊。

### <a name="plan-to-pilot"></a>排程進行試點

部署無密碼身份驗證時,應首先啟用一個或多個試驗組。 您可以在此建立[群組](../fundamentals/active-directory-groups-create-azure-portal.md)。 將將參加試驗的使用者添加到組中。 然後,為所選組啟用新的無密碼身份驗證方法。

組可以從本地目錄或 Azure AD 同步。 對試用結果感到滿意后,即可為所有用戶打開無密碼身份驗證。

請參閱部署計畫頁面上[的試用](https://aka.ms/deploymentplans)版最佳實踐。

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>使用 Microsoft 身份驗證器應用規劃無密碼身份驗證

微軟身份驗證器應用程式是從谷歌播放或蘋果應用商店免費下載。 [瞭解有關下載微軟身份驗證器應用程式的更多內容](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。 讓用戶下載 Microsoft 身份驗證器應用。 並按照說明啟用電話登錄。 

它將任何 iOS 或 Android 手機轉換為強大的無密碼認證。 使用者登錄到任何平台或瀏覽器時,將通知通知到他們的手機,將螢幕上顯示的號碼與手機上的數位匹配,然後使用生物識別或 PIN 進行確認。 [檢視有關 Microsoft 認證器套用如何工作的詳細資訊](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#microsoft-authenticator-app)。

![使用認證器套用登入](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>微軟驗證器應用的技術注意事項

**AD FS 整合**- 當使用者啟用 Microsoft 身份驗證器無密碼認證時,該使用者的身份驗證預設發送通知以進行審批。 混合租戶中的使用者將被禁止被定向到 ADFS 進行登錄,除非他們選擇"改用您的密碼"。 此過程還繞過任何本地條件訪問策略和傳遞身份驗證流。 但是,如果指定*了login_hint,* 則使用者將轉發到 ADFS 並繞過使用無密碼認證的選項。

**Azure 多重身份驗證伺服器**- 透過組織的本地 Azure MFA 伺服器啟用多重身份驗證的最終用戶可以創建和使用單個無密碼電話登入認證。 如果用戶嘗試使用憑據升級 Microsoft 身份驗證器的多個安裝(5 個或更多),則此更改可能會導致錯誤。

**設備註冊**- 要使用身份驗證器應用進行無密碼身份驗證,設備必須在 Azure AD 租戶中註冊,並且不能是共享設備。 設備只能在單個租戶中註冊。 此限制意味著使用身份驗證器應用的電話登錄僅支援一個工作或學校帳戶。

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>使用 FIDO2 安全金鑰規劃無密碼身份驗證
有三種類型的無密碼登入部署可用於安全金鑰:

-    受支援的瀏覽器上的 Azure 活動目錄 Web 應用
-    Azure 活動目錄加入 Windows 10 裝置
-    混合 Azure 活動目錄加入 Windows 10 裝置(預覽)
     -    提供對基於雲的和內部資源的訪問。 有關存取本地資源的詳細資訊,請參考[使用 FIDOP2 金鑰存取本地資源的 SSO](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)

您必須啟用**相容的 FIDO2 安全金鑰**。 微軟宣佈[與FIDO2主要供應商建立關鍵合作關係](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)。

**對 Azure AD Web 應用與 Azure AD Windows 聯接的裝置**:

-    Windows 10 版本 1809 或更高版本使用受支援的瀏覽器,如 Microsoft 邊緣或 Mozilla Firefox(版本 67 或更高版本)。 
-    Windows 10 版本 1809 支援 FIDO2 登入,可能需要部署 FIDO2 金鑰製造商的軟體。 我們建議您使用版本 1903 或更高版本。 

**對混合 Azure 的目錄網域加入裝置**: 
-    Windows 10 內部資訊內部版本 18945 或更高版本
-    運行 Windows Server 2016 或 2019 的完全修補域伺服器。
-    最新版本的 Azure AD 連線

有關要求的完整清單,請參閱使用[Azure 活動目錄將無密碼安全金鑰登入 Windows 10 裝置](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements)。


### <a name="security-key-life-cycle"></a>安全金鑰生命週期

安全密鑰允許訪問您的資源,並且應規劃這些實體設備的管理。

1. **金鑰分發**:計劃如何為組織預配密鑰。 您可能有一個集中式預配過程,或者允許最終用戶購買 FIDO 2.0 相容金鑰。
1. **金鑰啟動**:最終用戶必須自行啟動安全密鑰。 最終使用者在首次使用時[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)註冊其安全密鑰並啟用第二個因素(PIN 或生物識別)。
1. **禁用密鑰**:雖然安全密鑰功能處於預覽階段,但管理員無法從使用者帳戶中刪除密鑰。 用戶必須將其刪除。 如果鑰匙丟失或被盜:
   1. 從啟用無密碼身份驗證的任何組中刪除使用者。
   1. 驗證他們已刪除金鑰作為身份驗證方法。
   1. 頒發新密鑰。 **金鑰替換**:使用者可以同時啟用兩個安全密鑰。 更換安全密鑰時,請確保使用者也刪除了要更換的密鑰。

### <a name="enable-windows-10-support"></a>開啟 Windows 10 支援

使用 FIDO2 安全金鑰啟用 Windows 10 登入需要啟用 Windows 10 中的認證提供程式功能。 選擇下列其中之一：

- [使用 Intune 啟用認證提供者](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - 建議使用 Intune 部署。
- [使用預先配套件開啟認證提供者](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - 如果無法進行 Intune 部署,管理員必須在每台電腦上部署一個包以啟用認證提供提供程式功能。 套件安裝可以通過以下選項之一執行:
      - 群組原則或設定管理員
      - 在 Windows 10 電腦上安裝本地端
- [使用群組策略啟用認證提供者](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - 僅適用於混合 Azure AD 聯接設備。

#### <a name="enable-on-premises-integration"></a>開啟本地端

要啟用對本地資源的訪問,請按照步驟[將無密碼安全密鑰登錄到本地資源(預覽)。](howto-authentication-passwordless-security-key-on-premises.md)

> [!IMPORTANT]
> 對於任何混合的 Azure AD 加入設備,還必須完成這些步驟,以便使用 FIDO2 安全金鑰進行 Windows 10 登錄。

### <a name="register-security-keys"></a>註冊安全金鑰

用戶必須在其 Azure 活動目錄聯接的 Windows 10 計算機上註冊其安全密鑰。

關於詳細資訊,請參閱[FIDO2 安全金鑰的使用者註冊和管理](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)。


## <a name="plan-auditing-security-and-testing"></a>規劃稽核、安全性和測試
規劃符合組織和合規性框架的審核是部署的重要組成部分。

### <a name="auditing-passwordless"></a>稽核無密碼

Azure AD 具有提供技術和業務見解的報告。 讓您的業務和技術應用程式擁有者根據組織的要求對這些報告擁有和使用。

Azure 活動目錄門戶中的**身份驗證**方法部分是管理員可以啟用和管理無密碼認證設定的位置。

Azure AD 在以下時間將項目加入到稽核紀錄:

- 管理員在"身份驗證方法"部分進行更改。
- 使用者對 Azure 活動目錄中的認證進行任何類型的更改。

下表提供典型報告機制的一些範例:

|   | 管理風險 | 提高生產率 | 治理和合規性 |
| --- | --- | --- | --- |
| **報表類型** | 認證方法 - 註冊為聯合安全註冊的使用者 | 認證方法 ─ 註冊應用程式通知的使用者 | 登錄:查看訪問租戶的人員以及如何存取租戶 |
| **潛在行動** | 目標使用者尚未註冊 | 以使用 Microsoft 認證器應用或安全金鑰 | 復原對管理員的存取或強制實施其他安全原則 |

**Azure AD 將大多數審核數據保留 30 天**,並通過 Azure 管理門戶或 API 提供數據,以便下載到分析系統中。 如果需要更長的保留時間,則匯出和使用 SIEM 工具中的日誌(如 Azure Sentinel、Splunk 或[Azure Sentinel](../../sentinel/connect-azure-active-directory.md)相撲邏輯)。 [詳細瞭解檢視存取及使用報告](../reports-monitoring/overview-reports.md)。

用戶可以通過導航[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)到 來註冊和管理其憑據。 此連結將使用者定向到通過組合 SSPR/多重身份驗證註冊體驗啟用的最終使用者憑據管理體驗。 Azure AD 記錄 FIDO2 安全設備的註冊,以及使用者對身份驗證方法的更改。

### <a name="plan-security"></a>規劃安全性
作為此推出計劃的一部分,Microsoft 建議為擁有特權管理員帳戶啟用無密碼身份驗證。

當使用者在安全金鑰上啟用或禁用帳戶,或重置其 Windows 10 電腦上安全金鑰的第二個因素時,將一個條目添加到安全日誌中,並在以下事件 ID 下 *:4670*和*5382*。

### <a name="plan-testing"></a>排建測試

在部署的每個階段測試方案和採用時,確保結果按預期進行。

#### <a name="testing-the-microsoft-authenticator-app"></a>測試微軟身分驗證器應用

以下是使用 Microsoft 認證器應用進行無密碼身份驗證的範例測試用例:

| 狀況 | 預期的結果 |
| --- | --- |
| 使用者可以註冊微軟身份驗證器應用程式 | 使用者可以從aka.ms/mysecurityinfo註冊應用 |
| 使用者可以啟用電話登入 | 為工作帳戶設定的電話登入 |
| 使用者可以存取電話登入的應用程式 | 用戶通過電話登錄流到達應用程式。 |
| 通過在 Azure 活動目錄門戶中的身份驗證方法螢幕中關閉 Microsoft 身份驗證器無密碼登入,測試回滾電話登入註冊 | 以前啟用的用戶無法使用來自 Microsoft 身份驗證器的無密碼登錄。 |
| 從 Microsoft 認證器套用中移除手機登入 | 工作帳號在 Microsoft 認證器已不再可用 |

#### <a name="testing-security-keys"></a>測試安全金鑰

以下是使用安全金鑰進行無密碼身份驗證的範例測試用例。

**沒有密碼 FIDO 登入 Azure 的 Sabs s 目錄加入的 Windows 10 裝置**

| 狀況 | 預期的結果 |
| --- | --- |
| 使用者可以註冊 FIDO2 裝置 (1809) | 使用者可以在「設定>帳戶註冊 FIDO2 設備>登錄選項>安全金鑰 |
| 使用者可以重置 FIDO2 裝置 (1809) | 使用者可以使用製造商軟體重置 FIDO2 裝置 |
| 使用者可以使用 FIDO2 裝置登入 (1809) | 用戶可以從登錄視窗中選擇安全密鑰,並成功登錄。 |
| 使用者可以註冊FIDO2設備 (1903) | 使用者可以在「設定>帳戶註冊 FIDO2 設備>登錄選項>安全金鑰 |
| 使用者可以重置 FIDO2 裝置 (1903) | 使用者可以在設定>帳戶重置 FIDO2 裝置>登入選項>安全金鑰 |
| 使用者可以使用 FIDO2 裝置登入 (1903) | 用戶可以從登錄視窗中選擇安全密鑰,並成功登錄。 |

**沒有密碼 FIDO 登入 Azure AD Web 應用**

| 狀況 | 預期的結果 |
| --- | --- |
| 使用者可以使用微軟邊緣在aka.ms/mysecurityinfo註冊FIDO2設備 | 註冊應成功 |
| 使用者可以在aka.ms/mysecurityinfo使用火狐註冊FIDO2設備 | 註冊應成功 |
| 使用者可以使用 Microsoft 邊緣使用 FIDO2 裝置線上登入 OneDrive | 登入應成功 |
| 使用者可以使用 Firefox 使用 FIDO2 裝置線上登入 OneDrive | 登入應成功 |
| 通過在 Azure 活動目錄門戶中的身份驗證方法視窗中關閉 FIDO2 安全金鑰,測試回滾 FIDO2 裝置註冊 | 系統將提示使用者使用安全金鑰登錄。 使用者將成功登錄,並顯示錯誤:「您的公司策略要求您使用其他方法登錄」。。 然後,用戶應該能夠選擇其他方法並成功登錄。 關閉視窗並再次登錄以驗證它們未看到相同的錯誤消息。 |

### <a name="plan-for-rollback"></a>規劃復原作業

儘管無密碼身份驗證是一項輕量級功能,對最終用戶的影響最小,但可能需要回滾。

回滾需要管理員登錄到 Azure 活動目錄門戶,選擇所需的強身份驗證方法,並將啟用選項更改為 **"否**"。 此過程將關閉所有使用者的無密碼功能。

已註冊 FIDO2 安全設備的使用者在下次登錄時將提示他們使用安全設備,然後看到以下錯誤:

![選擇其他登入方法](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>部署與無密碼識別並排除故障故障

按照以下步驟與所選方法對齊。

### <a name="required-administrative-roles"></a>所需的管理角色

| Azure AD 角色 | 描述 |
| --- | --- |
| 全域管理員|能夠實現合併註冊體驗的最小特權角色。 |
| 驗證系統管理員 | 能夠實現和管理身份驗證方法的最小特權角色。 |
| User | 在設備上配置身份驗證器應用或為 Web 或 Windows 10 登入註冊安全密鑰設備的特權角色。 |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>使用 Microsoft 身份驗證器應用部署電話登入

按照本文中的步驟,使用[Microsoft 身份驗證器應用啟用無密碼登錄](howto-authentication-passwordless-phone.md),使 Microsoft 身份驗證器應用成為組織中的無密碼身份驗證方法。

### <a name="deploy-fido2-security-key-sign-in"></a>部署 FIDO2 安全金鑰登入

按照本文中的步驟,為[Azure AD 啟用無密碼安全密鑰登錄](howto-authentication-passwordless-security-key.md),將 FIDO2 安全金鑰作為無密碼身份驗證方法啟用。

### <a name="troubleshoot-phone-sign-in"></a>排除電話登入故障

| 狀況 | 解決方法 |
| --- | --- |
| 使用者無法執行合併註冊。 | 確保啟用[合併註冊](concept-registration-mfa-sspr-combined.md)。 |
| 使用者無法啟用電話登錄身份驗證器應用。 | 確保用戶處於部署範圍。 |
| 使用者不在無密碼身份驗證範圍內,但顯示無密碼登錄選項,他們無法完成。 | 當使用者在創建策略之前在應用程式中啟用了電話登錄時,將發生此情況。 <br> *要啟用登錄*:將使用者添加到啟用無密碼登錄的用戶範圍。 <br> *阻止登錄*:讓使用者從該應用程式中刪除其憑據。 |

### <a name="troubleshoot-security-key-sign-in"></a>排除安全金鑰登入的故障

| 狀況 | 解決方法 |
| --- | --- |
| 使用者無法執行合併註冊。 | 確保啟用[合併註冊](concept-registration-mfa-sspr-combined.md)。 |
| 使用者無法在其[安全設置](https://aka.ms/mysecurityinfo)中添加安全密鑰。 | 確保啟用[安全性金鑰](howto-authentication-passwordless-security-key.md)。 |
| 使用者無法在 Windows 10 登錄選項中添加安全金鑰。 | [確保 Windows 登入的安全金鑰](howto-authentication-passwordless-enable.md) |
| **錯誤訊息**:我們發現此瀏覽器或操作系統不支援 FIDO2 安全密鑰。 | 無密碼的 FIDO2 安全設備只能在 Windows 10 版本 1809 或更高版本的受支援的瀏覽器(微軟邊緣、Firefox 版本 67)中註冊。 |
| **錯誤訊息**:您的公司策略要求您使用其他方法登錄。 | 租戶中啟用了不確定的安全密鑰。 |
| 使用者無法在 Windows 10 版本 1809 上管理我的安全金鑰 | 版本 1809 要求您使用 FIDO2 金鑰供應商提供的安全密鑰管理軟體。 請與供應商聯繫以獲取支援。 |
| 我認為我的 FIDO2 安全金鑰可能有缺陷 - 如何測試它。 | 導航到[https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/),輸入測試帳戶的憑據,插入可疑安全密鑰,選擇螢幕右上角的**+** 按鈕,按一下"創建",然後完成創建過程。 如果此方案失敗,您的設備可能有故障。 |

## <a name="next-steps"></a>後續步驟

- [為 Azure AD 的登入無密碼安全金鑰](howto-authentication-passwordless-security-key.md)
- [使用 Microsoft 認證器應用程式啟用無密碼登入](howto-authentication-passwordless-phone.md)
- [瞭解有關身份驗證方法使用方式&見解詳細資訊](howto-authentication-methods-usage-insights.md)

