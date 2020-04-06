---
title: Azure 多重身份驗證的部署注意事項
description: 瞭解成功實現 Azure 多重身份驗證的部署注意事項和策略
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
ms.openlocfilehash: a70c6ae3ebc7f5b39550508594bd4d4907e68a67
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667351"
---
# <a name="plan-an-azure-multi-factor-authentication-deployment"></a>規劃 Azure 多重身份驗證部署

在日益複雜的場景中,人們正在連接到組織資源。 人們使用智慧手機、平板電腦、PC 和筆記型電腦(通常在多個平臺上)在公司網路上和公司網路外使用組織擁有、個人和公共設備進行連接。 在這個始終連接、多設備和多平臺的世界中,使用者帳戶的安全性比以往任何時候都更加重要。 跨設備、網路和平臺使用的密碼無論其複雜性如何,都已不足以確保使用者帳戶的安全性,尤其是在使用者傾向於跨帳戶重複使用密碼時。 複雜的網路釣魚和其他社交工程攻擊可能導致使用者名和密碼在暗網中發佈和銷售。

[Azure 多重身份驗證 (MFA)](concept-mfa-howitworks.md)有助於保護對數據和應用程式的訪問。 它使用第二種身份驗證形式提供額外的安全層。 組織可以使用[條件存取](../conditional-access/overview.md)使解決方案滿足其特定需求。

此部署指南示範如何規劃然後測試 Azure 多重身份驗證推出。

要快速查看 Azure 多重身份驗證的生效,然後回來瞭解其他部署注意事項:

> [!div class="nextstepaction"]
> [啟用 Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Prerequisites

在開始部署 Azure 多重身份驗證之前,應考慮一些先決條件項。

| 狀況 | 必要條件 |
| --- | --- |
| 具有現代身份驗證**的僅限雲**的標識環境 | **無其他先決條件任務** |
| **混合**識別機制 | 部署[Azure AD 連接](../hybrid/whatis-hybrid-identity.md),並且使用者識別與具有 Azure 活動目錄的本地活動目錄域服務同步或聯合。 |
| 為雲存取而發布的本地舊版應用程式 | 已部署 Azure AD[應用程式代理](../manage-apps/application-proxy.md)程式 。 |
| 將 Azure MFA 與 RADIUS 身份驗證一起使用 | 部署了[網路策略伺服器 (NPS)。](howto-mfa-nps-extension.md) |
| 使用者擁有 Microsoft Office 2010 或更早版本,或 iOS 11 或更早的 Apple 郵件 | 升級到[Microsoft Office 2013 或更高版本](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o),以及適用於 iOS 12 或更高版本的 Apple 郵件。 舊身份驗證協定不支援條件訪問。 |

## <a name="plan-user-rollout"></a>計畫使用者推出

您的 MFA 推出計畫應包括試驗部署,然後是支援能力範圍內的部署波。 通過將條件訪問策略應用於一小群試點用戶來開始推出。 評估對試點使用者、使用的進程和註冊行為的影響后,可以將更多組添加到策略中,也可以向現有組添加更多使用者。

### <a name="user-communications"></a>使用者通訊

在計劃的通信中,向使用者通報即將進行的更改、Azure MFA 註冊要求以及任何必要的使用者操作至關重要。 我們建議與組織內部的代表(如通信、變更管理或人力資源部門)協調開發通信。

Microsoft 提供[通訊範本](https://aka.ms/mfatemplates)和[最終用戶文檔](../user-help/security-info-setup-signin.md),以説明起草您的通信。 您可以通過選擇該頁面[https://myprofile.microsoft.com](https://myprofile.microsoft.com)上**的安全資訊**連結將使用者發送到直接註冊。

## <a name="deployment-considerations"></a>部署考量因素

通過強制實施具有條件訪問的策略來部署 Azure 多重身份驗證。 [條件存取原則](../conditional-access/overview.md)可以要求使用者在滿足某些條件時執行多重身份驗證,例如:

* 所有使用者、特定使用者、組成員或分配的角色
* 正在存取的特定雲應用程式
* 裝置平台
* 裝置狀態
* 網路位置或地理位置 IP 位址
* 用戶端應用程式
* 登入風險(需要身份保護)
* 符合規範的裝置
* 已加入混合式 Azure AD 的裝置
* 已批准的客戶端申請

在[多重身份驗證推出材料](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)中使用可自定義的海報和電子郵件範本向您的組織推出多重身份驗證。

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>透過條件式存取啟用 Multi-Factor Authentication

條件訪問策略強制註冊,要求未註冊的使用者在第一次登錄時完成註冊,這是一個重要的安全考慮。

[Azure AD 標誌保護](../identity-protection/howto-configure-risk-policies.md)為 Azure 多重身份驗證情景提供了註冊策略和自動風險檢測和補救策略。 當存在身份洩露的威脅時,可以創建策略以強制更改密碼,或者當登錄被以下[事件](../reports-monitoring/concept-risk-events.md)視為風險時,需要 MFA:

* 認證外洩
* 從匿名 IP 位址登入
* 不可能到達非典型位置的移動
* 從不熟悉的位置登入
* 從受感染的裝置登入
* 來自具有可疑活動的 IP 位址的登入

Azure 活動目錄標識保護檢測到的某些風險檢測是即時進行的,有些需要脫機處理。 管理員可以選擇阻止表現出危險行為並手動修復、需要更改密碼或需要多重身份驗證的用戶,作為條件訪問策略的一部分。

## <a name="define-network-locations"></a>定義網路位置

我們建議組織使用條件訪問使用[命名位置](../conditional-access/location-condition.md#named-locations)定義其網路。 如果您的組織正在使用標識保護,請考慮使用基於風險的策略,而不是指定位置。

### <a name="configuring-a-named-location"></a>設定命名位置

1. 在 Azure 門戶中開啟**Azure 活動目錄**
2. 選取 [安全性]****
3. 在 **'管理**' 下, 選擇**命名位置**
4. 選擇**新位置**
5. 在 **「名稱」** 欄位中,提供有意義的名稱
6. 選擇為*IP 範圍*或*國家/ 地區*定義位置
   1. 如果使用*IP 範圍*
      1. 決定是否*將標記為信任位置*。 從受信任位置登入可降低使用者的登入風險。 僅當您知道輸入的 IP 範圍在組織中已建立且可信時,才將此位置標記為可信。
      2. 指定 IP 範圍
   2. 如果使用*國家/地區*
      1. 展開下拉菜單,並選擇要為此命名位置定義的國家或地區。
      2. 決定是否*包含未知區域*。 未知區域是無法映射到國家/地區 IP 位址。
7. 選擇 **"創建"**

## <a name="plan-authentication-methods"></a>排程認證認證認證方法

管理員可以選擇他們希望提供給使用者的[認證方法](../authentication/concept-authentication-methods.md)。 請務必允許多個身份驗證方法,以便使用者在其主方法不可用時具有可用的備份方法。 以下方法可供管理員啟用:

### <a name="notification-through-mobile-app"></a>行動應用程式的通知

推送通知將發送到行動裝置上的Microsoft身份驗證器應用。 使用者查看通知並選擇 **「批准」** 以完成驗證。 通過移動應用推送通知為使用者提供了最少的侵入性選項。 它們也是最可靠和安全的選項,因為它們使用數據連接而不是電話。

> [!NOTE]
> 如果您的組織有工作人員在中國工作或前往中國,則**通過Android設備上****的移動應用方法通知**在該國不起作用。 應為這些使用者提供替代方法。

### <a name="verification-code-from-mobile-app"></a>行動應用程式傳回的驗證碼

像 Microsoft 身份驗證器應用這樣的行動應用每 30 秒生成一個新的 OATH 驗證碼。 使用者會在登入介面中輸入此驗證碼。 無論手機是否有數據或蜂窩信號,都可以使用移動應用選項。

### <a name="call-to-phone"></a>電話通話

向使用者發出自動語音呼叫。 使用者應答呼叫,並在電話鍵盤**#** 上按壓以批准其身份驗證。 撥打電話是一種從移動應用通知或驗證代碼的備份方法。

### <a name="text-message-to-phone"></a>電話簡訊

包含驗證碼的文本消息將發送給使用者,系統將提示使用者將驗證碼輸入登錄介面。

### <a name="choose-verification-options"></a>選擇驗證選項

1. 瀏覽至 **Azure Active Directory**、**使用者**、**Multi-Factor Authentication**。

   ![從 Azure 入口網站中的 [Azure AD 使用者] 刀鋒視窗存取 Multi-Factor Authentication 入口網站](media/howto-mfa-getstarted/users-mfa.png)

1. 在開啟的新索引標籤中，瀏覽至 [服務設定]****。
1. 在 [驗證選項]**** 底下，核取使用者可用方法的所有方塊。

   ![在 [Multi-Factor Authentication 服務設定] 索引標籤中設定驗證方法](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. 按一下 [ **儲存**]。
1. 關閉 [服務設定]**** 索引標籤。

## <a name="plan-registration-policy"></a>計劃註冊政策

管理員必須確定使用者如何註冊其方法。 組織應啟用 Azure MFA 與自助服務密碼重置 (SSPR)[的新組合註冊體驗](howto-registration-mfa-sspr-combined.md)。 SSPR 允許使用者使用與用於多重身份驗證的相同方法以安全方式重置密碼。 我們建議此組合註冊(當前在公共預覽版中),因為它對用戶來說是一種很好的體驗,能夠為這兩種服務註冊一次。 為 SSPR 和 Azure MFA 啟用相同的方法將允許註冊使用者使用這兩種功能。

### <a name="registration-with-identity-protection"></a>身份保護註冊

如果您的組織正在使用 Azure 活動目錄識別保護,[請配置 MFA 註冊策略](../identity-protection/howto-mfa-policy.md),以提示使用者下次以互動方式登錄時進行註冊。

### <a name="registration-without-identity-protection"></a>無身份保護的註冊

如果您的組織沒有啟用身份保護的許可證,系統將提示使用者下次在登錄時註冊 MFA。 如果使用者不使用受 MFA 保護的應用程式,則可能無法註冊 MFA。 註冊所有使用者非常重要,以便不法分子無法猜測使用者的密碼並代表他們註冊 MFA,從而有效地控制帳戶。

#### <a name="enforcing-registration"></a>強制執行註冊

使用以下步驟,條件存取策略可以強制使用者註冊多重身份驗證

1. 創建組,添加當前未註冊的所有使用者。
2. 使用條件訪問,為此組強制實施多重身份驗證以訪問所有資源。
3. 定期重新評估組成員身份,並刪除從組註冊的使用者。

您可以使用依賴於[MSOnline PowerShell 模組](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)的 PowerShell 命令標識已註冊和非註冊的 Azure MFA 使用者。

#### <a name="identify-registered-users"></a>識別註冊使用者

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>識別非註冊使用者

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>將使用者從每個使用者 MFA 轉換為基於條件存取的 MFA

如果使用者啟用了每個使用者並強制執行了 Azure 多重身份驗證,則以下 PowerShell 可以説明您轉換為基於條件訪問的 Azure 多重身份驗證。

在 ISE 視窗中執行此 PowerShell,或`.PS1`另存為檔以在本地運行。

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
> 我們最近相應地更改了上述行為和 PowerShell 腳本。 以前,腳本保存了 MFA 方法、禁用 MFA 並還原了這些方法。 現在,禁用的默認行為未清除方法,這不再需要。

## <a name="plan-conditional-access-policies"></a>排程條件存取原則

要規劃條件訪問策略策略(這將確定何時需要 MFA 和其他控件),請參閱[Azure 活動目錄中的條件訪問是什麼?](../conditional-access/overview.md)

請務必防止無意中被鎖定在 Azure AD 租戶之外。 通過在[租戶中創建兩個或多個緊急訪問帳戶](../users-groups-roles/directory-emergency-access.md)並將其從條件訪問策略中排除,可以減輕這種意外缺乏管理訪問許可權的影響。

### <a name="create-conditional-access-policy"></a>建立條件式存取原則

1. 使用全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽到**Azure 的目錄** > **安全** > **條件存取**。
1. 選取 [新增原則]****。
   ![建立條件存取原則,為實驗群組中的 Azure 入口使用者啟用 MFA](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. 為原則提供有意義的名稱。
1. 在 [使用者和群組]**** 底下：
   * 在 [包含]**** 索引標籤上，選取 [所有使用者]**** 選項按鈕
   * 在 **"排除**"選項卡上,選中 **"使用者和組**"複選框,然後選擇緊急存取帳戶。
   * 按一下 [完成]****。
1. 在 [雲端應用程式]**** 下方，選取 [所有雲端應用程式]**** 選項按鈕。
   * 選擇性：在 [排除]**** 索引標籤上，選擇組織不要要求使用 MFA 的雲端應用程式。
   * 按一下 [完成]****。
1. 在 [條件]**** 區段底下：
   * 選擇性：如果您已啟用 Azure Identity Protection，您可以選擇要在原則中評估登入風險。
   * 選擇性：如果您已設定信任的位置或具名位置，您可以指定要在原則中包含或排除這些位置。
1. 在 **「授予**」下,請確保選擇**了 「授予訪問**」單選按鈕。
    * 核取 [需要多重要素驗證]**** 的方塊。
    * 按下 **「選擇**」。
1. 略過 [工作階段]**** 區段。
1. 將**啟用策略**切換設定為 **「打開**」 。
1. 按一下頁面底部的 [新增]  。

## <a name="plan-integration-with-on-premises-systems"></a>排程與本地系統整合

某些不直接針對 Azure AD 進行身份驗證的舊版和本地應用程式需要使用 MFA 的其他步驟,包括:

* 舊式本地應用程式,需要使用應用程式代理。
* 本地 RADIUS 應用程式,需要將 MFA 適配器與 NPS 伺服器一起使用。
* 本地 AD FS 應用程式,需要將 MFA 適配器與 AD FS 2016 或更高版本一起使用。

直接使用 Azure AD 進行身份驗證並具有現代身份驗證(WS-Fed、SAML、OAuth、OpenID Connect)的應用程式可以直接使用條件存取策略。

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>將 Azure MFA 與 Azure AD 應用程式代理一起使用

駐留在本地的應用程式可以通過[Azure AD 應用程式代理](../manage-apps/application-proxy.md)發佈到 Azure AD 租戶,如果它們配置為使用 Azure AD 預身份驗證,則可以利用 Azure 多重身份驗證。

這些應用程式受條件訪問策略的約束,這些策略強制實施 Azure 多重身份驗證,就像任何其他 Azure AD 集成應用程式一樣。

同樣,如果對所有使用者登錄強制實施 Azure 多重身份驗證,則使用 Azure AD 應用程式代理發佈的本地應用程式將受到保護。

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>將 Azure 多重身份驗證與網路策略伺服器整合

Azure MFA 的網路原則伺服器 (NPS) 擴充功能可使用現有伺服器將雲端式 MFA 功能新增至驗證基礎結構。 使用 NPS 分機,您可以將電話呼叫、簡訊或電話應用驗證添加到現有身份驗證流中。 此整合的限制:

* 使用 CHAPv2 協定,僅支援身份驗證器應用推送通知和語音呼叫。
* 無法應用條件訪問策略。

NPS 擴展充當 RADIUS 和基於雲端的 Azure MFA 之間的適配器,以提供第二個身份驗證因數,以保護[VPN、](howto-mfa-nps-extension-vpn.md)[遠端桌面閘道連接](howto-mfa-nps-extension-rdg.md)或其他支援 RADIUS 的應用程式。 在此環境中註冊 Azure MFA 的使用者將對所有身份驗證嘗試提出質疑,缺少條件訪問策略意味著始終需要 MFA。

#### <a name="implementing-your-nps-server"></a>實作 NPS 伺服器

如果已部署和正在使用的 NPS 實體,請參閱[將現有的 NPS 基礎結構與 Azure 多重身份驗證整合](howto-mfa-nps-extension.md)。 如果您是首次設置 NPS,請參閱[網路策略伺服器 (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)獲得說明。 容損排除指南可在「[解決來自 Azure 多重身份驗證的 NPS 副檔名中的錯誤訊息](howto-mfa-nps-extension-errors.md)」一文中找到。

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>為未註冊 MFA 的使用者準備 NPS

選擇未註冊 MFA 的用戶嘗試進行身份驗證時發生的情況。 使用註冊表路徑`HKLM\Software\Microsoft\AzureMFA``REQUIRE_USER_MATCH`中的 註冊表設置來控制功能行為。 此設定具有單個配置選項。

| Key | 值 | 預設 |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | 真/假 | 未設定 (相當於 TRUE) |

此設定的目的是要決定當使用者未註冊 MFA 時的行為。 下表列出了更改此設置的效果。

| 設定 | 使用者 MFA 狀態 | 效果 |
| --- | --- | --- |
| 金鑰不存在 | 未註冊 | MFA 挑戰不成功 |
| 值設定為 True / 未設定 | 未註冊 | MFA 挑戰不成功 |
| 金鑰設置為"錯誤" | 未註冊 | 沒有 MFA 的身份驗證 |
| 金鑰設置為"錯誤"或"真" | 已註冊 | 必須使用 MFA 進行認證 |

### <a name="integrate-with-active-directory-federation-services"></a>與活動目錄來服務整合

如果組織與 Azure AD 聯合,則可以使用[Azure 多重身份驗證來保護本地](multi-factor-authentication-get-started-adfs.md)和雲端中的 AD FS 資源。 Azure MFA 使您能夠減少密碼並提供更安全的身份驗證方式。 從 Windows 伺服器 2016 開始,現在可以為主身份驗證配置 Azure MFA。

與 Windows 伺服器 2012 R2 中的 AD FS 不同,AD FS 2016 Azure MFA 適配器與 Azure AD 直接整合,不需要本地 Azure MFA 伺服器。 Azure MFA 適配器內建於 Windows Server 2016 中,無需進行其他安裝。

將 Azure MFA 與 AD FS 2016 一起使用時,目標應用程式受條件存取策略的約束,還有其他注意事項:

* 當應用程式是 Azure AD 的依託方,與 AD FS 2016 或更高版本聯合時,條件訪問可用。
* 當應用程式是 AD FS 2016 或 AD FS 2019 的依託方,並且與 AD FS 2016 或 AD FS 2019 進行管理或聯合時,條件訪問不可用。
* 當 AD FS 2016 或 AD FS 2019 配置為使用 Azure MFA 作為主要身份驗證方法時,條件訪問也不可用。

#### <a name="ad-fs-logging"></a>AD FS 紀錄記錄

標準 AD FS 2016 和 2019 登入 Windows 安全日誌和 AD FS 管理紀錄,包含有關身份驗證請求及其成功或失敗的資訊。 這些事件中的事件日誌數據將指示是否使用了 Azure MFA。 例如,AD FS 稽核事件 ID 1200 可能包含:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>續訂與管理憑證

在每個 AD FS 伺服器上,在本地電腦「我的應用商店」中,將有一個名為 OU_Microsoft AD FS Azure MFA 的自簽名 Azure MFA 證書,其中包含證書到期日期。 檢查每個 AD FS 伺服器上的此憑證的有效期,以確定到期日期。

如果憑證的有效期接近過期,請在每個 AD [FS 伺服器產生並驗證新的 MFA 憑證](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)。

以下指南詳細介紹了如何在 AD FS 伺服器上管理 Azure MFA 證書。 使用 Azure MFA 配置 AD`New-AdfsAzureMfaTenantCertificate`FS 時, 透過 PowerShell cmdlet 生成的憑證有效期為兩年。 在過期之前續訂並安裝續訂的證書,以在 MFA 服務中取消中斷。

## <a name="implement-your-plan"></a>實施您的計劃

現在,您已經規劃了解決方案,您可以通過以下步驟實現:

1. 滿足任何必要的先決條件
   1. 為任何混合方案部署[Azure AD 連線](../hybrid/whatis-hybrid-identity.md)
   1. 在為雲端存取的任何本地應用程式上部署[Azure AD 應用程式代理](../manage-apps/application-proxy.md)
   1. 部署[NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)進行任何 RADIUS 認證
   1. 確保使用者已升級到啟用了現代身份驗證的 Microsoft Office 支援的版本
1. 設定選擇[的身份驗證方法](#choose-verification-options)
1. 定義[的網路位置](../conditional-access/location-condition.md#named-locations)
1. 選擇要開始推出 MFA 的組。
1. 設定[條件存取原則](#create-conditional-access-policy)
1. 設定 MFA 註冊原則
   1. [聯合多邊基金和SSPR](howto-registration-mfa-sspr-combined.md)
   1. [帶 識別保護](../identity-protection/howto-mfa-policy.md)
1. 傳送使用者通訊並讓使用者註冊[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [追蹤註冊者](#identify-non-registered-users)

> [!TIP]
> 政府雲使用者可以註冊[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>管理您的解決方案

Azure MFA 的報告

Azure 多重身份驗證通過 Azure 門戶提供報告:

| Report | Location | 描述 |
| --- | --- | --- |
| 使用方式和詐騙警示 | Azure AD > 登入 | 提供整體使用量、使用者摘要和使用者詳細資料的相關資訊；以及在指定的日期範圍期間所提交的詐騙警示歷程記錄。 |

## <a name="troubleshoot-mfa-issues"></a>排除 MFA 問題

在 Microsoft 支援中心的[「排除 Azure 多重身份驗證」 中](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)查找與 Azure MFA 的常見問題的解決方案。

## <a name="next-steps"></a>後續步驟

要查看 Azure 多重身份驗證,請完成以下教程:

> [!div class="nextstepaction"]
> [啟用 Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)
