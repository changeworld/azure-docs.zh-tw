---
title: 設定適用於受控網域的混合式 Azure Active Directory Join | Microsoft Docs
description: 了解如何設定適用於受控網域的混合式 Azure Active Directory Join。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56b0685dee518399ae8328ddac18f03e82918a38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89268412"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>教學課程：設定適用於受控網域的混合式 Azure Active Directory Join

在本教學課程中，您會了解如何為已加入 Active Directory 網域的裝置設定混合式 Azure Active Directory (Azure AD) Join。 針對同時包含內部部署 Active Directory 和 Azure AD 的受控環境，此方法可為其提供支援。

就像是貴組織中的使用者，裝置是您想要保護的核心身分識別。 您可以使用裝置的身分識別，隨時隨地保護您的資源。 您可以藉由管理 Azure AD 中的裝置身分識別來完成此目標。 請使用下列其中一個方法：

- Azure AD Join
- 混合式 Azure AD Join
- Azure AD 註冊

本文著重於混合式 Azure AD Join。

將您的裝置導入 Azure AD 中，您將可透過跨雲端和內部部署資源的單一登入 (SSO)，將使用者的生產力最大化。 同時您可以利用[條件式存取](../conditional-access/howto-conditional-access-policy-compliant-device.md)來保護對雲端和內部部署資源的存取。

您可以藉由使用[密碼雜湊同步 (PHS)](../hybrid/whatis-phs.md) 或[傳遞驗證 (PTA)](../hybrid/how-to-connect-pta.md) 並搭配[無縫單一登入](../hybrid/how-to-connect-sso.md)來部署受控環境。 在這些案例中，您不需要設定同盟伺服器以進行驗證。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定混合式 Azure AD Join
> * 啟用舊版 Windows 裝置
> * 確認加入的裝置
> * 疑難排解

## <a name="prerequisites"></a>必要條件

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 或更新版本)
- Azure AD 租用戶的全域管理員認證
- 每個樹系的企業系統管理員認證

熟悉下列文章：

- [什麼是裝置身分識別？](overview.md)
- [How To:規劃混合式 Azure Active Directory Join 實作](hybrid-azuread-join-plan.md)
- [混合式 Azure AD Join 的受控驗證](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD 不支援受控網域中的智慧卡或憑證。

確認 Azure AD Connect 已將您要加入混合式 Azure AD 之裝置的電腦物件同步處理至 Azure AD。 如果電腦物件屬於特定組織單位 (OU)，請設定 OU 使其在 Azure AD Connect 中進行同步處理。 若要深入了解如何使用 Azure AD Connect 來同步處理電腦物件，請參閱[組織單位型篩選](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)。

從 1.1.819.0 版開始，Azure AD Connect 就包含可用來設定混合式 Azure AD Join 的精靈。 此精靈可大幅簡化設定程序。 精靈會設定裝置註冊的服務連接點 (SCP)。

本文中的設定步驟是以在 Azure AD Connect 中使用精靈為基礎。

要使用混合式 Azure AD Join，您的裝置必須能夠從組織的網路中存取下列 Microsoft 資源：  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (如果您使用或計劃使用無縫 SSO)

> [!WARNING]
> 如果您的組織使用的 Proxy 伺服器會在防止資料遺失或 Azure AD 租用戶限制等案例中攔截 SSL 流量，請確認已從 TLS 中斷和檢查 (TLS break-and-inspect) 中排除 'https://device.login.microsoftonline.com ' 的流量。 無法排除 'https://device.login.microsoftonline.com ' 可能會造成用戶端憑證驗證的干擾，進而產生裝置註冊和裝置型條件式存取的問題。

如果組織需要透過輸出 Proxy 來存取網際網路，您可以使用[實作 Web Proxy 自動探索 (WPAD)](/previous-versions/tn-archive/cc995261(v=technet.10)) \(英文\)，讓 Windows 10 電腦能向 Azure AD 進行裝置註冊。 若要解決 WPAD 的設定和管理問題，請參閱[針對自動偵測進行疑難排解](/previous-versions/tn-archive/cc302643(v=technet.10))。 在 1709 更新之前的 Windows 10 裝置中，WPAD 是唯一可用來將 Proxy 設定為使用混合式 Azure AD 聯結的選項。 

如果您未使用 WPAD，則從 Windows 10 1709 開始，您可以在電腦上設定 WinHTTP Proxy 設定。 如需詳細資訊，請參閱 [GPO 所部署的 WinHTTP Proxy 設定](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo)。

> [!NOTE]
> 如果您使用 WinHTTP 設定在電腦上設定 Proxy 設定，則任何無法連線到所設定 Proxy 的電腦將無法連線到網際網路。

如果您的組織需要透過已驗證的輸出 Proxy 存取網際網路，請確定 Windows 10 電腦可以成功向輸出 Proxy 進行驗證。 因為 Windows 10 電腦會使用電腦內容來執行裝置註冊，所以請使用電腦內容來設定輸出 Proxy 驗證。 請向您的輸出 Proxy 提供者洽詢相關設定需求。

請使用[測試裝置註冊連線能力](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)指令碼，來確認裝置是否能夠在系統帳戶下存取上述 Microsoft 資源。

## <a name="configure-hybrid-azure-ad-join"></a>設定混合式 Azure AD Join

若要使用 Azure AD Connect 來設定混合式 Azure AD Join：

1. 啟動 Azure AD Connect，然後選取 [設定]。

   ![歡迎使用](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. 在 [其他工作] 中選取 [設定裝置選項]，然後選取 [下一步]。

   ![其他工作](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. 在 [概觀] 中，選取 [下一步]。

   ![概觀](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. 在 [連線到 Azure AD] 中，輸入 Azure AD 租用戶的全域管理員認證。  

   ![連接至 Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. 在 [裝置選項] 中選取 [設定混合式 Azure AD Join]，然後選取 [下一步]。

   ![裝置選項](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. 在 [SCP 設定] 中，針對要以 Azure AD Connect 設定 SCP 的每個樹系完成下列步驟，然後選取 [下一步]。

   1. 選取 [樹系]。
   1. 選取 [驗證服務]。
   1. 選取 [新增]，並輸入企業系統管理員認證。

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. 在 [裝置作業系統] 中，選取 Active Directory 環境中的裝置所使用的作業系統，然後選取 [下一步]。

   ![裝置作業系統](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. 在 [準備好設定] 中，選取 [設定]。

   ![準備設定](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. 在 [設定完成] 中，選取 [結束]。

   ![組態完成](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>啟用舊版 Windows 裝置

如果有些已加入網域的裝置是舊版 Windows 裝置，則您必須︰

- 設定裝置註冊的近端內部網路設定
- 設定無縫 SSO
- 為舊版 Windows 電腦安裝 Microsoft Workplace Join

> [!NOTE]
> Windows 7 支援已於 2020 年 1 月 14 日終止。 如需詳細資訊，請參閱 [Windows 7 支援已終止](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020)。

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>設定裝置註冊的近端內部網路設定

若要完成舊版 Windows 裝置的混合式 Azure AD Join，並避免在裝置向 Azure AD 進行驗證時出現憑證提示，您可以將原則推送到已加入網域的裝置，以在 Internet Explorer 中將下列 URL 新增至近端內部網路區域︰

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

您也必須在使用者的本機內部網路區域中啟用 [允許透過指令碼更新狀態列]。

### <a name="configure-seamless-sso"></a>設定無縫 SSO

若要在使用[密碼雜湊同步](../hybrid/whatis-phs.md)或[傳遞驗證](../hybrid/how-to-connect-pta.md)作為 Azure AD 雲端驗證方法的受控網域中完成舊版 Windows 裝置的混合式 Azure AD Join，您還必須[設定無縫 SSO](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)。

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>為舊版 Windows 電腦安裝 Microsoft Workplace Join

若要註冊舊版 Windows 裝置，組織必須安裝[適用於非 Windows 10 電腦的 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)。 適用於非 Windows 10 電腦的 Microsoft Workplace Join 可在 Microsoft 下載中心取得。

您可以使用軟體發佈系統 (例如  [Microsoft Endpoint Configuration Manager](/configmgr/)) 來部署此套件。 此套件支援使用 `quiet` 參數的標準無訊息安裝選項。 Configuration Manager 的目前版本會提供勝過舊版的好處，例如能夠追蹤已完成的註冊。

安裝程式會在系統上建立排定的工作，此工作是在使用者內容中執行。 此工作會在使用者登入 Windows 時觸發。 此工作會在使用 Azure AD 進行驗證之後，利用使用者認證以無訊息方式向 Azure AD 加入裝置。

## <a name="verify-the-registration"></a>確認註冊

以下是用來尋找並驗證裝置狀態的三種方式：

### <a name="locally-on-the-device"></a>在本機裝置上

1. 開啟 Windows PowerShell。
2. 輸入 `dsregcmd /status`。
3. 確認 **AzureAdJoined** 和 **DomainJoined** 都設定為 [是]。
4. 您可以使用 **DeviceId**，並使用 Azure 入口網站或 PowerShell 來比較服務的狀態。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

1. 使用[直接連結](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)移至裝置頁面。
2. 如需如何找出裝置的資訊，請參閱[如何使用 Azure 入口網站管理裝置身分識別](./device-management-azure-portal.md)。
3. 如果**已註冊**資料行顯示為**擱置**，表示混合式 Azure AD Join 尚未完成。
4. 如果**已註冊**資料行包含**日期/時間**，表示混合式 Azure AD Join 已完成。

### <a name="using-powershell"></a>使用 PowerShell

請使用 **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** 確認 Azure 租用戶中的裝置註冊狀態。 此 Cmdlet 位於 [Azure Active Directory PowerShell 模組](/powershell/azure/active-directory/install-msonlinev1?view=azureadps-2.0)中。

使用 **Get-MSolDevice** Cmdlet 來檢查服務詳細資料時：

- 必須要有**裝置識別碼**與 Windows 用戶端上的識別碼相符的物件存在。
- **DeviceTrustType** 的值是 [已加入網域]。 此設定相當於 Azure AD 入口網站中的 [裝置] 頁面上所呈現的 [已加入混合式 Azure AD] 狀態。
- 在條件式存取中使用的裝置，[Enabled] 的值是 [True]，而 [DeviceTrustLevel] 是 [Managed]。

1. 以系統管理員身分開啟 Windows PowerShell。
2. 輸入 `Connect-MsolService` 以連線至您的 Azure 租用戶。

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>計算所有已加入混合式 Azure AD 的裝置 (不包括**擱置**狀態)

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>計算所有已加入混合式 Azure AD 的裝置 (包括**擱置**狀態)

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>列出已加入混合式 Azure AD 的裝置

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>列出所有已加入混合式 Azure AD 的裝置 (包括**擱置**狀態)

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>列出單一裝置的詳細資料：

1. 輸入 `get-msoldevice -deviceId <deviceId>` (這是在本機裝置上取得的 **DeviceId**)。
2. 確認 [已啟用] 設為 [True]。

## <a name="troubleshoot-your-implementation"></a>對您的實作進行疑難排解

如果您在為已加入網域的 Windows 裝置完成混合式 Azure AD Join 時遇到問題，請參閱：

- [使用 dsregcmd 命令針對裝置進行疑難排解](./troubleshoot-device-dsregcmd.md)
- [針對已進行混合式 Azure Active Directory Join 的裝置進行疑難排解](troubleshoot-hybrid-join-windows-current.md)
- [針對已加入混合式 Azure Active Directory 的下層裝置進行疑難排解](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>後續步驟

請前往下一篇文章，以了解如何使用 Azure 入口網站來管理裝置身分識別。
> [!div class="nextstepaction"]
> [管理裝置身分識別](device-management-azure-portal.md)