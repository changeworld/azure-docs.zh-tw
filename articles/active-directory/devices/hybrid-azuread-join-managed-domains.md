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
ms.openlocfilehash: bcd00972c2da0d3d5dafe76a8619e0f0ccaedc19
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79081988"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>教學課程：設定適用於受控網域的混合式 Azure Active Directory Join

在本教學課程中，您將瞭解如何為 Active Directory 已加入網域的裝置設定混合式 Azure Active Directory （Azure AD）聯結。 這個方法支援包含內部部署 Active Directory 和 Azure AD 的受管理環境。

就像是貴組織中的使用者，裝置是您想要保護的核心身分識別。 您可以使用裝置的身分識別，隨時隨地保護您的資源。 您可以藉由管理 Azure AD 中的裝置身分識別來完成此目標。 請使用下列其中一個方法：

- Azure AD Join
- 混合式 Azure AD Join
- Azure AD 註冊

本文著重于混合式 Azure AD 聯結。

將您的裝置導入 Azure AD 中，您將可透過跨雲端和內部部署資源的單一登入 (SSO)，將使用者的生產力最大化。 同時您可以利用[條件式存取](../active-directory-conditional-access-azure-portal.md)來保護對雲端和內部部署資源的存取。

您可以使用[密碼雜湊同步處理（PHS）](../hybrid/whatis-phs.md)或[傳遞驗證（PTA）](../hybrid/how-to-connect-pta.md)搭配[無縫單一登入](../hybrid/how-to-connect-sso.md)來部署受管理的環境。 在這些案例中，您不需要設定同盟伺服器以進行驗證。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定混合式 Azure AD Join
> * 啟用舊版 Windows 裝置
> * 確認加入的裝置
> * 疑難排解

## <a name="prerequisites"></a>Prerequisites

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) （1.1.819.0 或更新版本）
- Azure AD 租用戶的全域管理員認證
- 每個樹系的企業系統管理員認證

熟悉下列文章：

- [什麼是裝置身分識別？](overview.md)
- [如何：規劃混合式 Azure Active Directory 聯結執行](hybrid-azuread-join-plan.md)
- [混合式 Azure AD 聯結的受控驗證](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD 不支援受控網域中的智慧卡或憑證。

確認 Azure AD Connect 已將您要加入混合式 Azure AD 之裝置的電腦物件同步處理至 Azure AD。 如果電腦物件屬於特定組織單位（Ou），請將 Ou 設定為在 Azure AD Connect 中同步處理。 若要深入瞭解如何使用 Azure AD Connect 同步處理電腦物件，請參閱以[組織單位為基礎的篩選](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)。

從版本1.1.819.0 開始，Azure AD Connect 包含設定混合式 Azure AD 聯結的 wizard。 此精靈可大幅簡化設定程序。 精靈會設定裝置註冊的服務連接點 (SCP)。

本文中的設定步驟是以在 Azure AD Connect 中使用精靈為基礎。

要使用混合式 Azure AD Join，您的裝置必須能夠從組織的網路中存取下列 Microsoft 資源：  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (如果您使用或計劃使用無縫 SSO)

如果您的組織需要透過輸出 proxy 存取網際網路，建議您[執行 Web Proxy 自動探索（WPAD）](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10))來啟用 Windows 10 電腦，以使用 Azure AD 進行裝置註冊。 若要解決設定和管理 WPAD 的問題，請參閱[疑難排解自動偵測](/previous-versions/tn-archive/cc302643(v=technet.10))。

如果您未使用 WPAD，則可以從 Windows 10 1709 開始設定電腦上的 proxy 設定。 如需詳細資訊，請參閱[GPO 部署的 WinHTTP Proxy 設定](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)。

> [!NOTE]
> 如果您使用 WinHTTP 設定在電腦上設定 Proxy 設定，則任何無法連線到所設定 Proxy 的電腦將無法連線到網際網路。

如果您的組織需要透過已驗證的輸出 proxy 存取網際網路，請確定您的 Windows 10 電腦可以成功向輸出 proxy 進行驗證。 由於 Windows 10 電腦會使用電腦內容來執行裝置註冊，因此請使用電腦內容來設定輸出 proxy 驗證。 請向您的輸出 Proxy 提供者洽詢相關設定需求。

使用 [[測試裝置註冊連線能力](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)] 腳本，確認裝置可以存取系統帳戶底下的上述 Microsoft 資源。

## <a name="configure-hybrid-azure-ad-join"></a>設定混合式 Azure AD Join

若要使用 Azure AD Connect 設定混合式 Azure AD 聯結：

1. 啟動 Azure AD Connect，然後選取 [設定]。

   ![歡迎使用](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. 在 [**其他**工作] 中，選取 [**設定裝置選項**]，然後選取 **[下一步]** 。

   ![其他工作](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. 在 **[總覽**] 中選取 **[下一步]** 。

   ![概觀](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. 在 **[連線到 Azure AD]** 中，輸入 Azure AD 租使用者的全域管理員認證。  

   ![連接至 Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. 在 [**裝置選項**] 中，選取 [**設定混合式 Azure AD 聯結**]，然後選取 **[下一步]** 。

   ![裝置選項](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. 在 [ **SCP**設定] 中，針對您想要 AZURE AD CONNECT 設定 SCP 的每個樹系，完成下列步驟，然後選取 **[下一步]** 。

   1. 選取**樹**系。
   1. 選取**驗證服務**。
   1. 選取 [新增]，並輸入企業系統管理員認證。

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. 在 [**裝置作業系統**] 中，選取 Active Directory 環境中的裝置所使用的作業系統，然後選取 **[下一步]** 。

   ![裝置作業系統](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. 在 [**準備好設定**] 中，選取 [**設定**]。

   ![準備設定](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. 在 [設定完成] 中 **，選取 [** **結束**]。

   ![組態完成](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>啟用舊版 Windows 裝置

如果有些已加入網域的裝置是舊版 Windows 裝置，您必須：

- 設定裝置註冊的近端內部網路設定
- 設定無縫 SSO
- 為舊版 Windows 電腦安裝 Microsoft Workplace Join

> [!NOTE]
> Windows 7 支援已於 2020 年 1 月 14 日終止。 如需詳細資訊，請參閱[Windows 7 支援結束](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020)。

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>設定裝置註冊的近端內部網路設定

若要完成 Windows 下層裝置的混合式 Azure AD 聯結，並避免在裝置向 Azure AD 進行驗證時出現憑證提示，您可以將原則推送到已加入網域的裝置，以將下列 Url 新增至 Internet Explorer 中的近端內部網路區域：

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

您也必須在使用者的近端內部網路區域中啟用 [**允許透過腳本更新狀態列**]。

### <a name="configure-seamless-sso"></a>設定無縫 SSO

若要在使用[密碼雜湊同步](../hybrid/whatis-phs.md)[處理或傳遞驗證](../hybrid/how-to-connect-pta.md)做為您 Azure AD 雲端驗證方法的受控網域中完成混合式 Azure AD 聯結 Windows 下層裝置，您也必須[設定無縫 SSO](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)。

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>為舊版 Windows 電腦安裝 Microsoft Workplace Join

若要註冊舊版 Windows 裝置，組織必須安裝[適用于非 Windows 10 電腦的 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)。 適用於非 Windows 10 電腦的 Microsoft Workplace Join 可在 Microsoft 下載中心取得。

您可以使用軟體發佈系統 (例如  [Microsoft Endpoint Configuration Manager](/configmgr/)) 來部署此套件。 此套件支援使用 `quiet` 參數的標準無訊息安裝選項。 目前的 Configuration Manager 版本提供舊版的優點，例如能夠追蹤已完成的註冊。

安裝程式會在系統上建立排定的工作，此工作是在使用者內容中執行。 此工作會在使用者登入 Windows 時觸發。 此工作會在使用 Azure AD 進行驗證之後，利用使用者認證以無訊息方式向 Azure AD 加入裝置。

## <a name="verify-the-registration"></a>確認註冊

使用 **[get-msoldevice](/powershell/msonline/v1/get-msoldevice)** 確認 Azure 租使用者中的裝置註冊狀態。 此 Cmdlet 位於[Azure Active Directory PowerShell 模組](/powershell/azure/install-msonlinev1?view=azureadps-2.0)中。

使用 **Get-MSolDevice** Cmdlet 來檢查服務詳細資料時：

- 必須要有**裝置識別碼**與 Windows 用戶端上的識別碼相符的物件存在。
- **DeviceTrustType**的值已**加入網域**。 此設定相當於 Azure AD 入口網站中的 [裝置] 頁面上所呈現的 [已加入混合式 Azure AD] 狀態。
- 針對在條件式存取中使用的裝置，[**已啟用**] 的值為**True**且 **[devicetrustlevel]** 為 [已**管理**]。

若要檢查服務詳細資料：

1. 以系統管理員身分開啟 Windows PowerShell。
1. 輸入 `Connect-MsolService` 以連線至您的 Azure 租用戶。  
1. 輸入 `get-msoldevice -deviceId <deviceId>`。
1. 確認 [已啟用] 設為 [True]。

## <a name="troubleshoot-your-implementation"></a>對您的實作進行疑難排解

如果您在完成已加入網域之 Windows 裝置的混合式 Azure AD 聯結時遇到問題，請參閱：

- [針對混合式 Azure Active Directory 已加入裝置進行疑難排解](troubleshoot-hybrid-join-windows-current.md)
- [針對已加入混合式 Azure Active Directory 的下層裝置進行疑難排解](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>後續步驟

前進到下一篇文章，以瞭解如何使用 Azure 入口網站來管理裝置身分識別。
> [!div class="nextstepaction"]
> [管理裝置身分識別](device-management-azure-portal.md)
