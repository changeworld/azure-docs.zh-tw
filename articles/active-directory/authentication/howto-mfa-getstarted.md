---
title: Azure Multi-Factor Authentication 部署考量
description: 了解成功實作 Azure Multi-Factor Authentication 的部署考量和策略
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: da9a0fe30721a2f55db2c4771f54716868f9972f
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055068"
---
# <a name="plan-an-azure-multi-factor-authentication-deployment"></a>規劃 Azure Multi-Factor Authentication 部署

人們與組織資源連線的情況日益複雜。 大家會使用智慧型手機、平板電腦、個人電腦和筆記型電腦，以組織擁有的裝置、個人裝置和公用裝置連線到公司網路來來去去，而且通常會連上多個平台。 在這種時時保持連線，使用多重裝置、多種平台的世界裡，使用者帳戶的安全性更勝以往。 在多種裝置、網路和平台上使用的密碼，無論複雜程度有多高，都不再足以確保使用者帳戶的安全性。若使用者傾向多個帳戶使用同組密碼，更是如此。 複雜巧妙的網路釣魚和其他社交工程攻擊，可能會導致使用者名稱和密碼遭放上暗網銷售。

[Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) 有助於保護資料與應用程式的存取權限， 提供第二種形式的驗證，能再增加一層安全防護。 組織可以使用[條件式存取](../conditional-access/overview.md)，打造符合自身特定需求的解決方案。

此部署指南會說明如何規劃並測試 Azure Multi-Factor Authentication 的新服務。

若要先快速認識 Azure Multi-Factor Authentication 的運作方式，再回來了解更多部署上的考量：

> [!div class="nextstepaction"]
> [啟用 Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Prerequisites

開始部署 Azure Multi-Factor Authentication 之前，需先考慮一些必要條件。

| 狀況 | 必要條件 |
| --- | --- |
| **僅限雲端**的身分識別環境 (含新式驗證) | **無需額外必要工作** |
| **混合式**身分識別案例 | 部署 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)，且使用者身分識別會與內部部署的 Active Directory Domain Services (含 Azure Active Directory) 同步處理或建立同盟。 |
| 發佈供雲端存取的內部部署舊版應用程式 | 部署 Azure AD [應用程式 Proxy](../manage-apps/application-proxy.md)。 |
| 搭配使用 Azure MFA 與 RADIUS 驗證 | 部署[網路原則伺服器 (NPS)](howto-mfa-nps-extension.md)。 |
| 使用者具有 Microsoft Office 2010 或更早版本，或適用於 iOS 11 或更早版本的 Apple Mail | 升級至 [Microsoft Office 2013 或以後版本](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o)和 iOS 12 或以後版本的 Apple Mail。 舊版驗證通訊協定目前不支援條件式存取。 |

## <a name="plan-user-rollout"></a>規劃使用者推出

您的 MFA 首度發行計劃應該包含試驗部署，隨後迎接支援能力可容許的部署潮。 開始推出時，請將條件式存取原則套用到一小組試驗使用者上。 評估了對試驗使用者、使用流程和註冊行為的效果之後，就可以將更多群組新增至原則，或將更多使用者新增至現有群組。

### <a name="user-communications"></a>與使用者溝通

請務必在規劃的溝通訊息中，通知使用者即將進行的變更、Azure MFA 註冊需求，以及任何必要的使用者動作。 建議您與來自組織的代表共同開發溝通訊息，例如通訊部門、變更管理部門或人力資源部門。

Microsoft 提供[通訊範本](https://aka.ms/mfatemplates)和[終端使用者文件](../user-help/security-info-setup-signin.md)，可協助您草擬溝通訊息。 選取該頁面的**安全性資訊**連結，即可將使用者傳送至 [https://myprofile.microsoft.com](https://myprofile.microsoft.com) 直接註冊。

## <a name="deployment-considerations"></a>部署考量因素

Azure Multi-factor Authentication 會強制執行具有條件式存取的原則，以此方式進行部署。 條件式存取原則可能會要求使用者在符合特定條件時執行多重要素驗證，例如：

* 所有使用者、特定使用者、群組成員或指派的角色
* 目前存取特定的雲端應用程式
* 裝置平台
* 裝置的狀態
* 網路位置或地理位置的 IP 位址
* 用戶端應用程式
* 登入風險 (需要身分識別保護)
* 符合規範的裝置
* 已加入混合式 Azure AD 的裝置
* 已核准的用戶端應用程式

歡迎使用[多重要素驗證推出素材](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)中可自訂的海報和電子郵件範本，在您的組織推出多重要素驗證。

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>透過條件式存取啟用 Multi-Factor Authentication

條件式存取原則會強制註冊，要求在第一次登入時取消註冊的使用者完成註冊，這是重要的安全性考量。

[Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) 會同時為 Azure Multi-Factor Authentication 案例提供註冊原則，並將風險偵測和補救原則自動化。 當身分識別遭受入侵威脅時，可建立原則來強制變更密碼，或在登入視為有所風險時要求進行 MFA，如下列[事件](../identity-protection/overview-identity-protection.md)：

* 認證外洩
* 從匿名 IP 位址登入
* 不可能到達非典型位置的移動
* 從不熟悉的位置登入
* 從受感染的裝置登入
* 從具有可疑活動的 IP 位址登入

部分由 Azure Active Directory Identity Protection 偵測到的風險偵測為即時發生，有些則需要離線處理。 系統管理員可以選擇封鎖展現出風險行為的使用者，並手動修復、要求變更密碼，或要求多重要素驗證做為條件式存取原則的一環。

## <a name="define-network-locations"></a>定義網路位置

建議組織使用條件式存取來定義其使用[具名位置](../conditional-access/location-condition.md#named-locations)的網路。 如果您的組織目前使用的是 Identity Protection，請考慮使用風險型的原則，而非具名位置。

### <a name="configuring-a-named-location"></a>設定具名位置

1. 開啟 Azure 入口網站中的 [Azure Active Directory]
2. 選取 [安全性]
3. 在 [管理] 下，選擇 [具名位置]
4. 選取 [新增位置]
5. 在 [名稱] 欄位中，提供有意義的名稱
6. 選取您要使用 [IP 範圍]或 [國家/地區] 來定義位置
   1. 如果使用 IP 範圍
      1. 決定是否標示為信任位置。 從受信任位置登入可降低使用者的登入風險。 只有在您知道輸入的 IP 範圍是於組織中建立且可靠時，再將此位置標示為信任。
      2. 指定 IP 範圍。
   2. 如果使用國家/地區
      1. 展開下拉式功能表，然後選取您想要為此具名位置定義的國家/地區。
      2. 決定是否包含未知區域。 未知區域是無法對應至國家/地區的 IP 位址。
7. 選取 [建立] 

## <a name="plan-authentication-methods"></a>規劃驗證方法

系統管理員可以選擇可供使用者使用的[驗證方法](../authentication/concept-authentication-methods.md)。 請務必允許使用一種以上的驗證方法，如此無法使用在主要方法的情況下，使用者才有備份方法可供使用。 系統管理員可以透過下列方法啟用：

> [!TIP]
> Microsoft 建議使用 Microsoft Authenticator (行動應用程式) 作為 Azure Multi-Factor Authentication 的主要方法，以獲得更安全且更佳的使用者體驗。 Microsoft Authenticator 應用程式也 [符合](https://azure.microsoft.com/resources/microsoft-nist/) 美國國家標準和技術驗證器保證等級。 

### <a name="notification-through-mobile-app"></a>行動應用程式的通知

此時會有推播通知傳送到您行動裝置上的 Microsoft Authenticator 應用程式。 使用者會看到通知，然後選取 [核准] 完成驗證。 透過行動應用程式的推播通知，能夠為使用者提供干擾度最小的選項。 而且也最為安全可靠，因為其使用的是資料連線而非電話語音。

> [!NOTE]
> 如果您的組織有員工在中國工作或旅行，在 **Android 裝置**上**透過行動應用程式通知**的方法無法在該國家/地區中使用。 請為這些使用者提供替代方法。

### <a name="verification-code-from-mobile-app"></a>行動應用程式傳回的驗證碼

像是 Microsoft Authenticator 應用程式的行動裝置應用程式每隔 30 秒會產生新的 OATH 驗證碼。 使用者會在登入介面中輸入此驗證碼。 無論電話是否有資料連線或行動電話訊號，都可以選擇使用行動應用程式。

### <a name="call-to-phone"></a>電話通話

會撥打自動語音電話給使用者。 使用者可接聽電話並按下電話鍵盤上的 **#** 來核准驗證。 撥打電話是取得行動應用程式通知或驗證碼的絕佳備用方式。

### <a name="text-message-to-phone"></a>電話簡訊

系統會傳送給使用者內有驗證碼的簡訊，並提示使用者在登入介面中輸入驗證碼。

### <a name="choose-verification-options"></a>選擇驗證選項

1. 瀏覽至 **Azure Active Directory**、**使用者**、**Multi-Factor Authentication**。

   ![從 Azure 入口網站中的 [Azure AD 使用者] 刀鋒視窗存取 Multi-Factor Authentication 入口網站](media/howto-mfa-getstarted/users-mfa.png)

1. 在開啟的新索引標籤中，瀏覽至 [服務設定]。
1. 在 [驗證選項] 底下，核取使用者可用方法的所有方塊。

   ![在 [Multi-Factor Authentication 服務設定] 索引標籤中設定驗證方法](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. 按一下 [ **儲存**]。
1. 關閉 [服務設定] 索引標籤。

## <a name="plan-registration-policy"></a>規劃註冊原則

系統管理員必須判斷使用者會如何註冊其方法。 建議組織啟用適用於 Azure MFA 和自助式密碼重設 (SSPR) 的[全新合併式註冊體驗](howto-registration-mfa-sspr-combined.md)。 SSPR 可讓使用者以安全的方式，透過與多重要素驗證相同的方法重設密碼。 我們建議使用這種合併的註冊，是因為能提供良好的使用體驗，且兩種服務只須註冊一次即可。 在 SSPR 和 Azure MFA 啟用相同的方法，即可讓您的使用者同時註冊使用這兩項功能。

### <a name="registration-with-identity-protection"></a>使用 Identity Protection 註冊

如果您的組織使用的是 Azure Active Directory Identity Protection，請[設定 MFA 註冊原則](../identity-protection/howto-identity-protection-configure-mfa-policy.md)，提示使用者在下一次以互動方式登入時註冊。

### <a name="registration-without-identity-protection"></a>不使用 Identity Protection 註冊

如果您的組織並無可供啟用 Identity Protection 的授權，在下次登入需進行 MFA 時，系統會提示使用者註冊。 如果使用者並未使用受到 MFA 保護的應用程式，則可能不會註冊 MFA。 請務必讓所有使用者確實註冊，才能讓惡意執行者無法猜測使用者密碼，並假冒他們的身份註冊 MFA 及實際控制帳戶。

#### <a name="enforcing-registration"></a>強制註冊

使用下列步驟，條件式存取原則就能強制使用者註冊 Multi-Factor Authentication

1. 建立群組，加入目前未註冊的所有使用者。
2. 使用條件式存取，對此群組強制執行多重要素驗證以存取所有資源。
3. 定期重新評估群組成員資格，並移除已從群組註冊的使用者。

您可以使用依賴 [MSOnline PowerShell 模組](/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0) (英文) 的 PowerShell 命令，識別已註冊和未註冊的 Azure MFA 使用者。

#### <a name="identify-registered-users"></a>識別已註冊的使用者

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>識別未註冊的使用者

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>將使用者從單使用者 MFA 轉換成條件式存取形式的 MFA

如果您的使用者是透過單使用者啟用，且強制執行 Azure 多重要素驗證，下列 PowerShell 可以協助您轉換成條件式存取形式的 Azure Multi-Factor Authentication。

在 ISE 視窗中執行此 PowerShell，或儲存為 `.PS1` 檔案在本機執行。

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> 我們最近據此變更了上述行為和 PowerShell 指令碼。 先前指令碼會儲存在 MFA 方法中再停用 MFA，並還原方法。 現在已不再需要，因為停用的預設行為並不會清除方法。

## <a name="plan-conditional-access-policies"></a>規劃條件式存取原則

若要規劃您的條件式存取原則策略，以此判斷何時需要 MFA 和其他控制項，請參閱[常見的條件式存取原則](../conditional-access/concept-conditional-access-policy-common.md)。

請務必避免不小心鎖定 Azure AD 的租用戶。 您可以藉由[在租用戶中建立兩個或多個緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)，來減緩不慎失去系統管理存取權的影響，並將其排除在條件式存取原則之外。

### <a name="create-conditional-access-policy"></a>建立條件式存取原則

1. 使用全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
   ![建立條件式存取原則，以對試驗群組中的 Azure 入口網站使用者啟用 MFA](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. 為原則提供有意義的名稱。
1. 在 [使用者和群組] 底下：
   * 在 [包含] 索引標籤上，選取 [所有使用者] 選項按鈕
   * 在 [排除] 索引標籤上，選取 [使用者和群組] 的方塊，然後選擇您的緊急存取帳戶。
   * 按一下 [完成] 。
1. 在 [雲端應用程式] 下方，選取 [所有雲端應用程式] 選項按鈕。
   * 選擇性：在 [排除] 索引標籤上，選擇組織不要要求使用 MFA 的雲端應用程式。
   * 按一下 [完成] 。
1. 在 [條件] 區段底下：
   * 選擇性：如果您已啟用 Azure Identity Protection，您可以選擇要在原則中評估登入風險。
   * 選擇性：如果您已設定信任的位置或具名位置，您可以指定要在原則中包含或排除這些位置。
1. 在 [授與] 下方，確定已選取 [授與存取權] 選項按鈕。
    * 核取 [需要多重要素驗證] 的方塊。
    * 按一下 [選取]。
1. 略過 [工作階段] 區段。
1. 將 [啟用原則] 切換為 [開啟]。
1. 按一下頁面底部的 [新增] 。

## <a name="plan-integration-with-on-premises-systems"></a>規劃內部部署系統整合

某些不會直接向 Azure AD 驗證的舊版和內部部署應用程式，需要額外步驟才能使用 MFA，包括：

* 舊版內部部署應用程式 (需要使用應用程式 Proxy)。
* 內部部署 RADIUS 應用程式 (需要搭配使用 MFA 配接器與 NPS 伺服器)。
* 內部部署 AD FS 應用程式 (需要使用具有 AD FS 2016 或更新版本的 MFA 配接器)。

直接使用 Azure AD 驗證並具有新式驗證 (WS-Fed、SAML、OAuth、OpenID Connect) 的應用程式，可以直接使用條件式存取原則。

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>搭配使用 Azure MFA 和 Azure AD 應用程式 Proxy

位於內部部署的應用程式可以透過 [Azure AD 應用程式 Proxy](../manage-apps/application-proxy.md) 發佈至您的 Azure AD 租用戶，且如果設定為使用 Azure AD 預先驗證，則可以利用 Azure Multi-Factor Authentication。

這些應用程式受限於強制執行 Azure Multi-Factor Authentication 的條件式存取原則，如同其他任何 Azure AD 整合式應用程式。

同樣地，如果在所有使用者登入時強制執行 Azure Multi-Factor Authentication，使用 Azure AD 應用程式 Proxy 發佈的內部部署應用程式將會受到保護。

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>整合適用於網路原則伺服器的 Azure Multi-Factor Authentication

Azure MFA 的網路原則伺服器 (NPS) 擴充功能可使用現有伺服器將雲端式 MFA 功能新增至驗證基礎結構。 有了 NPS 擴充功能，您便可以在現有的驗證流程中新增通話、簡訊或電話應用程式驗證。 此項整合具有下列限制：

* 使用 CHAPv2 通訊協定時，只支援驗證器應用程式推播通知和語音通話。
* 無法套用條件式存取原則。

NPS 擴充功能可作為 RADIUS 與雲端式 Azure MFA 之間的配接器，以提供第二種驗證因素來保護 [VPN](howto-mfa-nps-extension-vpn.md)、[遠端桌面閘道連線](howto-mfa-nps-extension-rdg.md)，或其他可使用 RADIUS 的應用程式。 在此環境中註冊 Azure MFA 的使用者將會面臨所有驗證嘗試的查問，缺少條件式存取原則表示一律需要 MFA。

#### <a name="implementing-your-nps-server"></a>實作 NPS 伺服器

如果您有已部署且已在使用的 NPS 執行個體，請[將現有的 NPS 基礎結構與 Azure Multi-Factor Authentication 整合](howto-mfa-nps-extension.md)。 如果您是第一次設定 NPS，請參閱[網路原則伺服器 (NPS)](/windows-server/networking/technologies/nps/nps-top) 以取得相關指示。 如需疑難排解指導方針，請參閱[解決 Azure Multi-Factor Authentication NPS 擴充功能的錯誤訊息](howto-mfa-nps-extension-errors.md)一文。

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>為未註冊 MFA 的使用者準備 NPS

選擇當未向 MFA 註冊的使用者嘗試驗證時，會出現什麼情形。 使用登錄路徑 `HKLM\Software\Microsoft\AzureMFA` 中的登錄設定 `REQUIRE_USER_MATCH` 控制功能行為。 此設定具有單一組態選項。

| Key | 值 | 預設 |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE/FALSE | 未設定 (相當於 TRUE) |

此設定的目的是要決定當使用者未註冊 MFA 時的行為。 變更此設定的效果會列在下表中。

| 設定 | 使用者 MFA 狀態 | 效果 |
| --- | --- | --- |
| 機碼不存在 | 未註冊 | MFA 挑戰失敗 |
| 設定為 True/未設定的值 | 未註冊 | MFA 挑戰失敗 |
| 機碼設為 False | 未註冊 | 不使用 MFA 的驗證 |
| 機碼設為 False 或 True | 已註冊 | 必須使用 MFA 驗證 |

### <a name="integrate-with-active-directory-federation-services"></a>整合 Active Directory 同盟服務

如果您的組織已和 Azure AD 建立同盟，即可使用 [Azure Multi-Factor Authentication 保護 AD FS 資源](multi-factor-authentication-get-started-adfs.md)，雲端和內部部署均包含在內。 Azure MFA 可讓您減少密碼用量，提供更安全的驗證方式。 從 Windows Server 2016 開始，現在可設定 Azure MFA 進行主要驗證。

不同於 Windows Server 2012 R2 中的 AD FS，AD FS 2016 Azure MFA 配接器會直接與 Azure AD 整合，不需要內部部署 Azure MFA 伺服器。 Azure MFA 配接器內建於 Windows Server 2016，不需要額外的安裝。

搭配使用 Azure MFA 與 AD FS 2016，且目標應用程式受條件式存取原則制約時，則有其他考量：

* 當應用程式是 Azure AD 的信賴憑證者，並以 AD FS 2016 或更新版本同盟時，就可以使用條件式存取。
* 當應用程式是 AD FS 2016 或 AD FS 2019 的信賴憑證者，且為受控或與 AD FS 2016 或 AD FS 2019 同盟時，就無法使用條件式存取。
* 當 AD FS 2016 或 AD FS 2019 設定為使用 Azure MFA 做為主要驗證方法時，也無法使用條件式存取。

#### <a name="ad-fs-logging"></a>AD FS 記錄

Windows 安全性記錄和 AD FS 系統管理員記錄中的標準 AD FS 2016 和 2019 記錄，包含了驗證要求及其成功或失敗的相關資訊。 這些事件內的事件記錄檔資料會指出是否已使用 Azure MFA。 例如，AD FS 的稽核事件識別碼 1200 可能包含：

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>更新及管理憑證

在每部 AD FS 伺服器上的 [本機電腦] [我的存放區] 中，會有名為 OU = Microsoft AD FS Azure MFA 的自我簽署 Azure MFA 憑證，其中包含憑證到期日。 請檢查每個 AD FS 伺服器上此憑證的有效期間，以判斷到期日。

如果憑證的有效期間即將到期，[在每個 AD FS 伺服器](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)上產生並驗證新的 MFA 憑證。

下列指引會詳細說明如何在您的 AD FS 伺服器上管理 Azure MFA 憑證。 當您使用 Azure MFA 設定 AD FS 時，透過 `New-AdfsAzureMfaTenantCertificate` PowerShell Cmdlet 產生的憑證有效期間為兩年。 請在到期前更新並安裝已更新的憑證，以避免 MFA 服務中斷。

## <a name="implement-your-plan"></a>實作您的方案

現在您已規劃好解決方案，接下來您可以遵循下列步驟實作：

1. 符合任何所需的必要條件
   1. 針對任何混合式案例部署 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
   1. 在針對雲端存取所發佈的任何內部部署應用程式上，部署 [Azure AD 應用程式 Proxy](../manage-apps/application-proxy.md)
   1. 針對任何 RADIUS 驗證部署 [NPS](/windows-server/networking/technologies/nps/nps-top)
   1. 確保使用者已升級至支援的 Microsoft Office 版本，且已啟用新式驗證
1. 設定所選的[驗證方法](#choose-verification-options)
1. 定義[具名網路位置](../conditional-access/location-condition.md#named-locations)
1. 選取群組以開始推出 MFA。
1. 設定[設定條件式存取原則](#create-conditional-access-policy)
1. 設定 MFA 註冊原則
   1. [合併 MFA 和 SSPR](howto-registration-mfa-sspr-combined.md)
   1. 使用 [Identity Protection](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
1. 傳送使用者通訊並讓使用者在此註冊：[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [追蹤已註冊的人員](#identify-non-registered-users)

> [!TIP]
> 政府雲端使用者可以在此註冊：[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>管理您的解決方案

Azure MFA 的報告

Azure Multi-Factor Authentication 透過 Azure 入口網站提供報告：

| Report | Location | 描述 |
| --- | --- | --- |
| 使用方式和詐騙警示 | Azure AD > 登入 | 提供整體使用量、使用者摘要和使用者詳細資料的相關資訊；以及在指定的日期範圍期間所提交的詐騙警示歷程記錄。 |

## <a name="troubleshoot-mfa-issues"></a>針對 MFA 問題進行疑難排解

如需 Azure MFA 常見問題的解決方案，請參閱 Microsoft 支援服務中心的[對 Azure 多重要素驗證問題進行故障排除](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)一文。

## <a name="next-steps"></a>後續步驟

若要查看作用中的 Azure Multi-Factor Authentication，請完成下列教學課程：

> [!div class="nextstepaction"]
> [啟用 Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)
