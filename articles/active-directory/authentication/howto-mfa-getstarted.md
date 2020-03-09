---
title: 部署 Azure 多重要素驗證-Azure Active Directory
description: Microsoft Azure 多重要素驗證部署規劃
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6da67589b15b4ab043510c0375c26c12f645adb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378096"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>規劃以雲端為基礎的 Azure 多因素驗證部署

在日益複雜的案例中，人們會連接到組織資源。 人們會使用智慧型手機、平板電腦、Pc 和膝上型電腦，從組織擁有、個人和公用裝置連線到公司網路，通常是在多個平臺上。 在這種 always 連線、多裝置和多平臺的世界中，使用者帳戶的安全性比以往更重要。 無論使用者的複雜性為何，在裝置、網路和平臺上使用的密碼，都不再足以確保使用者帳戶的安全性，特別是當使用者傾向于跨帳戶重複使用密碼時。 複雜的網路釣魚和其他社交工程攻擊可能會導致使用者名稱和密碼在深的 web 上張貼及銷售。

[Azure 多重要素驗證（MFA）](concept-mfa-howitworks.md)可協助保護資料和應用程式的存取。 它使用第二種形式的驗證來提供一層額外的安全性。 組織可以使用[條件式存取](../conditional-access/overview.md)，讓解決方案符合他們的特定需求。

## <a name="prerequisites"></a>Prerequisites

開始部署 Azure 多重要素驗證之前，必須考慮一些必要專案。

| 狀況 | 必要條件 |
| --- | --- |
| 具有新式驗證的**僅限雲端**身分識別環境 | **無額外的先決條件工作** |
| **混合**式身分識別案例 | 已部署[Azure AD Connect](../hybrid/whatis-hybrid-identity.md) ，且使用者身分識別會與內部部署 Active Directory Domain Services 進行同步處理，或與 Azure Active Directory 進行同盟。 |
| 針對雲端存取發佈的內部部署繼承應用程式 | 已部署 Azure AD[應用程式 Proxy](../manage-apps/application-proxy.md) 。 |
| 搭配使用 Azure MFA 與 RADIUS 驗證 | 已部署[網路原則伺服器（NPS）](howto-mfa-nps-extension.md) 。 |
| 使用者具有 Microsoft Office 2010 或更早版本，或適用于 iOS 11 或更早版本的 Apple Mail | 升級至[Microsoft Office 2013 或更新版本](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o)，以及適用于 iOS 12 或更新版本的 Apple mail。 舊版驗證通訊協定不支援條件式存取。 |

## <a name="plan-user-rollout"></a>規劃使用者首度發行

您的 MFA 首度發行計畫應該包含試驗部署，後面接著支援容量中的部署波。 將條件式存取原則套用到一小組試驗使用者，開始您的首度發行。 評估試驗使用者的效果、使用的程式和註冊行為之後，您可以將更多群組新增至原則，或將更多使用者新增至現有的群組。

### <a name="user-communications"></a>使用者通訊

請務必在規劃的通訊中，通知使用者有關即將進行的變更、Azure MFA 註冊需求，以及任何必要的使用者動作。 我們建議您與組織內的代表共同開發通訊，例如通訊、變更管理或人力資源部門。

Microsoft 提供[通訊範本](https://aka.ms/mfatemplates)和[使用者檔](../user-help/security-info-setup-signin.md)，以協助草擬您的通訊。 您可以藉由選取該頁面上的**安全性資訊**連結，將使用者傳送至[https://myprofile.microsoft.com](https://myprofile.microsoft.com)直接註冊。

## <a name="deployment-considerations"></a>部署考量因素

Azure 多重要素驗證是藉由強制執行具有條件式存取的原則來進行部署。 [條件式存取原則](../conditional-access/overview.md)可能會要求使用者在符合特定條件時執行多重要素驗證，例如：

* [所有使用者]、[特定使用者]、[群組的成員] 或 [指派的角色]
* 正在存取的特定雲端應用程式
* 裝置平台
* 裝置的狀態
* 網路位置或地理位置的 IP 位址
* 用戶端應用程式
* 登入風險（需要身分識別保護）
* 符合規範的裝置
* 已加入混合式 Azure AD 的裝置
* 核准的用戶端應用程式

使用[多重要素驗證推出材料](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)中可自訂的海報和電子郵件範本，為您的組織推出多重要素驗證。

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>透過條件式存取啟用 Multi-Factor Authentication

條件式存取原則會強制註冊，要求在第一次登入時取消註冊的使用者完成登錄，這是重要的安全性考慮。

[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md)會同時為 Azure 多因素驗證案例提供和自動化風險偵測和補救原則的註冊原則。 當有下列[事件](../reports-monitoring/concept-risk-events.md)的登入被視為有風險時，可以建立原則來強制變更密碼，或要求 MFA：

* 認證外洩
* 從匿名 IP 位址登入
* 不可能到達非典型位置的移動
* 從不熟悉的位置登入
* 從受感染的裝置登入
* 從具有可疑活動的 IP 位址登入

Azure Active Directory Identity Protection 偵測到的部分風險偵測會即時發生，有些則需要離線處理。 系統管理員可以選擇封鎖展示有風險行為的使用者，並手動修復、要求變更密碼，或要求多重要素驗證作為其條件式存取原則的一部分。

## <a name="define-network-locations"></a>定義網路位置

我們建議組織使用條件式存取，以使用[命名位置](../conditional-access/location-condition.md#named-locations)定義其網路。 如果您的組織使用 Identity Protection，請考慮使用以風險為基礎的原則，而不是命名位置。

### <a name="configuring-a-named-location"></a>設定命名位置

1. 在 Azure 入口網站中開啟**Azure Active Directory**
2. 選取 [安全性]
3. 在 [**管理**] 下，選擇 [**命名位置**]
4. 選取**新位置**
5. 在 [**名稱**] 欄位中，提供有意義的名稱
6. 選取您是否要使用*IP 範圍*或*國家/地區*來定義位置
   1. 如果使用*IP 範圍*
      1. 決定是否要*標示為信任的位置*。 從受信任位置登入可降低使用者的登入風險。 只有在您知道輸入的 IP 範圍已在組織中建立且可靠時，才將此位置標示為受信任。
      2. 指定 IP 範圍
   2. 如果使用*國家/地區*
      1. 展開下拉式功能表，然後選取您想要為此命名位置定義的國家或地區。
      2. 決定是否要*包含未知的區域*。 未知區域是無法對應至國家/地區的 IP 位址。
7. 選取 [建立]

## <a name="plan-authentication-methods"></a>規劃驗證方法

系統管理員可以選擇他們想要讓使用者使用的[驗證方法](../authentication/concept-authentication-methods.md)。 請務必允許一個以上的驗證方法，讓使用者在主要方法無法使用的情況下，擁有可用的備份方法。 系統管理員可以使用下列方法來啟用：

### <a name="notification-through-mobile-app"></a>行動應用程式的通知

推播通知會傳送至行動裝置上的 Microsoft Authenticator 應用程式。 使用者會查看通知，並選取 [**核准**] 以完成驗證。 透過行動應用程式的推播通知可為使用者提供最小的干擾性選項。 它們也是最可靠且安全的選項，因為它們使用的是資料連線，而不是電話語音。

> [!NOTE]
> 如果您的組織有員工從事或前往中國，則在**Android 裝置**上透過行動裝置**應用程式**方法的通知無法在該國家/地區運作。 其他方法應可供這些使用者使用。

### <a name="verification-code-from-mobile-app"></a>行動應用程式傳回的驗證碼

Microsoft Authenticator 應用程式之類的行動應用程式每隔30秒會產生新的 OATH 驗證碼。 使用者會在登入介面中輸入此驗證碼。 無論電話是否有資料或行動電話信號，都可以使用行動裝置應用程式選項。

### <a name="call-to-phone"></a>電話通話

自動語音通話會放到使用者。 使用者會接聽電話，然後按電話鍵盤上的 **#** 來核准其驗證。 電話撥打電話是行動裝置代理程式更新或驗證碼的絕佳備份方法。

### <a name="text-message-to-phone"></a>電話簡訊

包含驗證碼的文字訊息會傳送給使用者，系統會提示使用者在登入介面中輸入驗證碼。

### <a name="choose-verification-options"></a>選擇驗證選項

1. 瀏覽至 **Azure Active Directory**、**使用者**、**Multi-Factor Authentication**。

   ![從 Azure 入口網站中的 [Azure AD 使用者] 刀鋒視窗存取 Multi-Factor Authentication 入口網站](media/howto-mfa-getstarted/users-mfa.png)

1. 在開啟的新索引標籤中，瀏覽至 [服務設定]。
1. 在 [驗證選項] 底下，核取使用者可用方法的所有方塊。

   ![在 [Multi-Factor Authentication 服務設定] 索引標籤中設定驗證方法](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. 按一下 [ **儲存**]。
1. 關閉 [服務設定] 索引標籤。

## <a name="plan-registration-policy"></a>規劃註冊原則

系統管理員必須判斷使用者如何註冊其方法。 組織應該啟用 Azure MFA 和自助式密碼重設（SSPR）[的新合併註冊體驗](howto-registration-mfa-sspr-combined.md)。 SSPR 可讓使用者使用其用於多重要素驗證的相同方法，以安全的方式重設其密碼。 我們建議這項結合的註冊（目前為公開預覽狀態），因為這是使用者的絕佳體驗，而且能夠針對這兩項服務註冊一次。 針對 SSPR 和 Azure MFA 啟用相同的方法，可讓您的使用者進行註冊，以使用這兩項功能。

### <a name="registration-with-identity-protection"></a>使用 Identity Protection 進行註冊

如果您的組織使用 Azure Active Directory Identity Protection，請[設定 MFA 註冊原則](../identity-protection/howto-mfa-policy.md)，以提示使用者在下一次以互動方式登入時進行註冊。

### <a name="registration-without-identity-protection"></a>無身分識別保護的註冊

如果您的組織沒有啟用 Identity Protection 的授權，系統會提示使用者在下次登入時需要進行 MFA 註冊。 如果使用者未使用以 MFA 保護的應用程式，則可能不會註冊 MFA。 請務必讓所有使用者都已註冊，使不良執行者無法猜測使用者的密碼，並代表他們註冊 MFA，以有效控制帳戶。

#### <a name="enforcing-registration"></a>強制註冊

使用下列步驟，條件式存取原則可以強制使用者註冊多重要素驗證

1. 建立群組，加入目前未註冊的所有使用者。
2. 使用條件式存取，對此群組強制執行多重要素驗證，以存取所有資源。
3. 定期重新評估群組成員資格，並移除已從群組註冊的使用者。

您可以使用依賴[MSOnline powershell 模組](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)的 powershell 命令，來識別已註冊和未註冊的 Azure MFA 使用者。

#### <a name="identify-registered-users"></a>識別已註冊的使用者

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>識別未註冊的使用者

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>將使用者從每位使用者 MFA 轉換成以條件式存取為基礎的 MFA

如果您的使用者是使用每位使用者啟用並強制執行的 Azure 多重要素驗證來啟用，下列 PowerShell 可以協助您轉換成以條件式存取為基礎的 Azure 多重要素驗證。

在 ISE 視窗中執行此 PowerShell 或另存新檔。PS1 要在本機執行的檔案。

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
> 我們最近據此變更了上述行為和 PowerShell 腳本。 先前，腳本會儲存在 MFA 方法中，停用 MFA，並還原方法。 現在已不再需要，因為 disable 的預設行為並不會清除方法。

## <a name="plan-conditional-access-policies"></a>規劃條件式存取原則

若要規劃您的條件式存取原則策略，以決定何時需要 MFA 和其他控制項，請參閱[什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)。

請務必避免不小心鎖定 Azure AD 的租使用者。 您可以藉由[在租使用者中建立兩個或多個緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)，並從您的條件式存取原則中排除它們，來減輕此意外缺少系統管理存取權的影響。

### <a name="create-conditional-access-policy"></a>建立條件式存取原則

1. 使用全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
   ![建立條件式存取原則，為試驗群組中的 Azure 入口網站使用者啟用 MFA](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. 為原則提供有意義的名稱。
1. 在 [使用者和群組] 底下：
   * 在 [包含] 索引標籤上，選取 [所有使用者] 選項按鈕
   * 在 [**排除**] 索引標籤上，核取 [**使用者和群組**] 的方塊，然後選擇您的緊急存取帳戶。
   * 按一下 [完成]。
1. 在 [雲端應用程式] 下方，選取 [所有雲端應用程式] 選項按鈕。
   * 選擇性：在 [排除] 索引標籤上，選擇組織不要要求使用 MFA 的雲端應用程式。
   * 按一下 [完成]。
1. 在 [條件] 區段底下：
   * 選擇性：如果您已啟用 Azure Identity Protection，您可以選擇要在原則中評估登入風險。
   * 選擇性：如果您已設定信任的位置或具名位置，您可以指定要在原則中包含或排除這些位置。
1. 在 [授與] 下方，確定已選取 [授與存取權] 選項按鈕。
    * 核取 [需要多重要素驗證] 的方塊。
    * 按一下 [選取]。
1. 略過 [工作階段] 區段。
1. 將 [啟用原則] 切換為 [開啟]。
1. 按一下 **[建立]** 。

## <a name="plan-integration-with-on-premises-systems"></a>規劃與內部部署系統的整合

某些不會直接向 Azure AD 進行驗證的舊版和內部部署應用程式，需要額外的步驟才能使用 MFA，包括：

* 舊版內部部署應用程式，這需要使用應用程式 proxy。
* 內部部署 RADIUS 應用程式，這將需要搭配使用 MFA 介面卡與 NPS 伺服器。
* 內部部署 AD FS 應用程式，其必須使用具有 AD FS 2016 或更新版本的 MFA 介面卡。

直接使用 Azure AD 進行驗證並具有新式驗證（WS-ADDRESSING、SAML、OAuth、OpenID Connect）的應用程式，可以直接使用條件式存取原則。

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>搭配 Azure AD 應用程式 Proxy 使用 Azure MFA

位於內部部署的應用程式可以透過[Azure AD 應用程式 Proxy](../manage-apps/application-proxy.md)發佈至您的 Azure AD 租使用者，如果設定為使用 Azure AD 預先驗證，則可以利用 Azure 多重要素驗證。

這些應用程式受限於強制執行 Azure 多重要素驗證的條件式存取原則，就像任何其他 Azure AD 整合式應用程式一樣。

同樣地，如果對所有使用者登入強制執行 Azure 多重要素驗證，使用 Azure AD 應用程式 Proxy 發佈的內部部署應用程式將會受到保護。

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>整合 Azure 多重要素驗證與網路原則伺服器

Azure MFA 的網路原則伺服器 (NPS) 擴充功能可使用現有伺服器將雲端式 MFA 功能新增至驗證基礎結構。 使用 NPS 擴充功能，您可以在現有的驗證流程中新增通話、文字訊息或電話應用程式驗證。 這項整合有下列限制：

* 使用 CHAPv2 通訊協定時，只支援驗證器應用程式推播通知和語音通話。
* 無法套用條件式存取原則。

NPS 擴充功能可作為 RADIUS 與雲端式 Azure MFA 之間的介面卡，以提供第二個驗證因素來保護[VPN](howto-mfa-nps-extension-vpn.md)、[遠端桌面閘道](howto-mfa-nps-extension-rdg.md)連線，或其他支援 RADIUS 的應用程式。 在此環境中註冊 Azure MFA 的使用者將會面臨所有驗證嘗試的挑戰，缺少條件式存取原則表示一律需要 MFA。

#### <a name="implementing-your-nps-server"></a>執行 NPS 伺服器

如果您已部署且已在使用中的 NPS 實例，請參閱[整合現有的 Nps 基礎結構與 Azure 多重要素驗證](howto-mfa-nps-extension.md)。 如果您是第一次設定 NPS，請參閱[網路原則伺服器（NPS）](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)以取得相關指示。 如需疑難排解指引，請參閱[從適用于 Azure 多因素驗證的 NPS 擴充功能解決錯誤訊息](howto-mfa-nps-extension-errors.md)一文。

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>為未註冊 MFA 的使用者準備 NPS

選擇當未向 MFA 註冊的使用者嘗試進行驗證時，會發生什麼事。 使用登錄路徑 `HKLM\Software\Microsoft\AzureMFA` 中的登錄設定 `REQUIRE_USER_MATCH` 來控制功能行為。 此設定具有單一設定選項。

| Key | 值 | 預設 |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE/FALSE | 未設定 (相當於 TRUE) |

此設定的目的是要決定當使用者未註冊 MFA 時的行為。 變更此設定的效果會列在下表中。

| 設定 | 使用者 MFA 狀態 | 效果 |
| --- | --- | --- |
| 機碼不存在 | 未註冊 | MFA 挑戰不成功 |
| 設定為 True/未設定的值 | 未註冊 | MFA 挑戰不成功 |
| 金鑰設為 False | 未註冊 | 沒有 MFA 的驗證 |
| 金鑰設為 False 或 True | 過 | 必須使用 MFA 進行驗證 |

### <a name="integrate-with-active-directory-federation-services"></a>與 Active Directory 同盟服務整合

如果您的組織與 Azure AD 同盟，您可以使用[Azure 多重要素驗證來保護](multi-factor-authentication-get-started-adfs.md)內部部署和雲端中的 AD FS 資源。 Azure MFA 可讓您減少密碼，並提供更安全的驗證方式。 從 Windows Server 2016 開始，您現在可以設定 Azure MFA 進行主要驗證。

不同于 Windows Server 2012 R2 中的 AD FS，AD FS 2016 Azure MFA adapter 會直接與 Azure AD 整合，而不需要內部部署 Azure MFA Server。 Azure MFA 介面卡內建于 Windows Server 2016 中，不需要額外的安裝。

搭配使用 Azure MFA 與 AD FS 2016，而目標應用程式受條件式存取原則的制約時，還有其他考慮：

* 當應用程式是信賴憑證者來 Azure AD （與 AD FS 2016 或更新版本同盟）時，就可以使用條件式存取。
* 當應用程式是信賴憑證者 AD FS 2016 或 AD FS 2019，且受管理或同盟 AD FS 2016 或 AD FS 2019 時，就無法使用條件式存取。
* 當 AD FS 2016 或 AD FS 2019 設定為使用 Azure MFA 做為主要驗證方法時，也無法使用條件式存取。

#### <a name="ad-fs-logging"></a>AD FS 記錄

Windows 安全性記錄檔和 AD FS 系統管理員記錄檔中的標準 AD FS 2016 和2019記錄，包含驗證要求及其成功或失敗的相關資訊。 這些事件內的事件記錄檔資料會指出是否已使用 Azure MFA。 例如，AD FS 的審核事件識別碼1200可能包含：

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>更新和管理憑證

在每部 AD FS 伺服器上的 [本機電腦] [我的存放區] 中，將會有一個名為 OU = Microsoft AD FS Azure MFA 的自我簽署 Azure MFA 憑證，其中包含憑證到期日。 請檢查每個 AD FS 伺服器上此憑證的有效期間，以判斷到期日。

如果您憑證的有效期間即將到期，請[在每部 AD FS 伺服器上產生並驗證新的 MFA 憑證](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)。

下列指引會詳細說明如何在您的 AD FS 伺服器上管理 Azure MFA 憑證。 當您使用 Azure MFA 設定 AD FS 時，透過 `New-AdfsAzureMfaTenantCertificate` PowerShell Cmdlet 產生的憑證有效期限為2年。 請在到期前更新並安裝已更新的憑證，以在 MFA 服務中 ovoid 中斷。

## <a name="implement-your-plan"></a>實行您的方案

您已規劃好解決方案，接下來您可以遵循下列步驟來執行：

1. 符合任何必要的必要條件
   1. 為任何混合式案例部署[Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
   1. 針對雲端存取所發佈的任何內部部署應用程式，部署[Azure AD 的應用程式 Proxy](../manage-apps/application-proxy.md)
   1. 針對任何 RADIUS 驗證部署[NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)
   1. 確保使用者已升級至支援的 Microsoft Office 版本，且已啟用新式驗證
1. 設定選擇的[驗證方法](#choose-verification-options)
1. 定義您的[已命名網路位置](../conditional-access/location-condition.md#named-locations)
1. 選取 [群組] 以開始推出 MFA。
1. 設定[條件式存取原則](#create-conditional-access-policy)
1. 設定 MFA 註冊原則
   1. [結合的 MFA 和 SSPR](howto-registration-mfa-sspr-combined.md)
   1. 使用[Identity Protection](../identity-protection/howto-mfa-policy.md)
1. 傳送使用者通訊並讓使用者在[https://aka.ms/mfasetup](https://aka.ms/mfasetup)註冊
1. [追蹤已註冊的人員](#identify-non-registered-users)

> [!TIP]
> 政府雲端使用者可以在[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)註冊

## <a name="manage-your-solution"></a>管理您的解決方案

Azure MFA 的報告

Azure 多因素驗證透過 Azure 入口網站提供報告：

| Report | Location | 描述 |
| --- | --- | --- |
| 使用方式和詐騙警示 | Azure AD > 登入 | 提供整體使用量、使用者摘要和使用者詳細資料的相關資訊；以及在指定的日期範圍期間所提交的詐騙警示歷程記錄。 |

## <a name="troubleshoot-mfa-issues"></a>針對 MFA 問題進行疑難排解

在 Microsoft 支援服務中心的針對[Azure 多因素驗證進行疑難排解一文](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)，尋找 azure MFA 常見問題的解決方案。

## <a name="next-steps"></a>後續步驟

* [驗證方法有哪些？](concept-authentication-methods.md)
* [啟用 Azure Multi-Factor Authentication 的聚合式註冊和 Azure AD 自助密碼重設](concept-registration-mfa-sspr-converged.md)
* 系統會還是不會提示使用者執行 MFA 的原因？ 請參閱＜Azure Multi-Factor Authentication 中的報告＞文件中的 [Azure AD 登入報告](howto-mfa-reporting.md#azure-ad-sign-ins-report)一節。
