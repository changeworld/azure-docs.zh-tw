---
title: 計畫混合 Azure 活動目錄聯接 - Azure 活動目錄
description: 了解如何設定混合式 Azure Active Directory 已加入的裝置。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 152ff52ce52b573d7f24cbb2fafc944b1794f6d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129249"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>如何：規劃混合 Azure 活動目錄聯接實現

以類似的使用方式，裝置是您想要保護的另一個核心身分識別，可用來隨時隨地保護您的資源。 您可以使用下列其中一種方法，將裝置身分識別導入 Azure AD 中進行管理，以達到此目標：

- Azure AD Join
- 混合式 Azure AD Join
- Azure AD 註冊

將您的裝置導入 Azure AD 中，您將可透過跨雲端和內部部署資源的單一登入 (SSO)，將使用者的生產力最大化。 同時，您可以使用[條件訪問](../active-directory-conditional-access-azure-portal.md)來保護對雲和本地資源的訪問。

如果您有本地活動目錄 （AD） 環境，並且希望將 AD 域聯接的電腦加入到 Azure AD，則可以通過執行混合 Azure AD 聯接來實現此目的。 本文提供在您的環境中實作混合式 Azure AD Join 的相關步驟。 

## <a name="prerequisites"></a>Prerequisites

本文假定您熟悉[Azure 活動目錄中的設備標識管理簡介](../device-management-introduction.md)。

> [!NOTE]
> Windows 10 混合 Azure AD 聯接的最低需要網域控制站版本是 Windows Server 2008 R2。

## <a name="plan-your-implementation"></a>計劃您的實作

若要規劃您的混合式 Azure AD 實作，您應該熟悉：

|   |   |
| --- | --- |
| ![勾選][1] | 檢閱支援的裝置 |
| ![勾選][1] | 檢閱您應該知道的事情 |
| ![勾選][1] | 查看混合 Azure AD 聯接的控制驗證 |
| ![勾選][1] | 根據您的身份基礎結構選擇方案 |
| ![勾選][1] | 查看本地 AD UPN 對混合 Azure AD 聯接的支援 |

## <a name="review-supported-devices"></a>檢閱支援的裝置

混合式 Azure AD Join 支援廣泛的 Windows 裝置。 因為執行舊版 Windows 的裝置設定需要其他或不同的步驟，所以支援的裝置會分為兩類：

### <a name="windows-current-devices"></a>現行 Windows 裝置

- Windows 10
- Windows Server 2016
  - **注意**： Azure 國家雲客戶需要版本 1809
- Windows Server 2019

對於運行 Windows 桌面作業系統的設備，本文中列出了受支援的版本[Windows 10 發佈資訊](/windows/release-information/)。 作為最佳實踐，Microsoft 建議您升級到最新版本的 Windows 10。

### <a name="windows-down-level-devices"></a>舊版 Windows 裝置

- Windows 8.1
- Windows 7 支援已於 2020 年 1 月 14 日終止。 有關詳細資訊，請參閱對[Windows 7 的支援已結束](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)。
- Windows Server 2012 R2
- Windows Server 2012
- Windows 伺服器 2008 R2。 有關 Windows 伺服器 2008 和 2008 R2 的支援資訊，請參閱[為 Windows 伺服器 2008 結束支援做好準備](https://www.microsoft.com/cloud-platform/windows-server-2008)。

在第一個規劃步驟中，您應該檢閱您的環境，並判斷是否需要支援 Windows 舊版裝置。

## <a name="review-things-you-should-know"></a>檢閱您應該知道的事情

### <a name="unsupported-scenarios"></a>不支援的情節
- 如果環境由單個 AD 林組成，將標識資料同步到多個 Azure AD 租戶，則當前不支援混合 Azure AD 聯接。

- 運行網域控制站 （DC） 角色的 Windows 伺服器不支援混合 Azure AD 聯接。

- 使用憑據漫遊或使用者設定檔漫遊或強制設定檔時，Windows 低級設備不支援混合 Azure AD 聯接。

- 伺服器核心作業系統不支援任何類型的設備註冊。

### <a name="os-imaging-considerations"></a>作業系統映射注意事項
- 如果依賴于系統準備工具 （Sysprep），並且使用**Windows 10 1809 前**映射進行安裝，請確保映射不是來自已在 Azure AD 註冊為混合 Azure AD 聯接的設備。

- 如果依賴于虛擬機器 （VM） 快照來創建其他 VM，請確保快照不是來自已在 Azure AD 註冊為混合 Azure AD 聯接的 VM。

- 如果使用[統一寫入篩選器](/windows-hardware/customize/enterprise/unified-write-filter)和在重新開機時清除磁片更改的類似技術，則必須在設備加入混合 Azure AD 後應用這些技術。 在混合 Azure AD 聯接完成之前啟用此類技術將導致設備在每次重新開機時未連接

### <a name="handling-devices-with-azure-ad-registered-state"></a>處理具有 Azure AD 註冊狀態的設備
如果 Windows 10 域加入的設備是[Azure AD 註冊](overview.md#getting-devices-in-azure-ad)給租戶，則可能導致混合 Azure AD 加入和 Azure AD 註冊設備的雙重狀態。 我們建議升級到 Windows 10 1803（應用 KB4489894）或以上，以自動解決此方案。 在 1803 以前的版本中，在啟用混合 Azure AD 聯接之前，需要手動刪除 Azure AD 註冊狀態。 在 1803 及以上版本中，已進行了以下更改，以避免這種雙重狀態：

- <i>在設備加入混合 Azure AD 並且同一使用者登錄 後，</i>將自動刪除使用者的任何現有 Azure AD 註冊狀態。 例如，如果使用者 A 在設備上具有 Azure AD 註冊狀態，則僅當使用者 A 登錄到設備時，才會清除使用者 A 的雙狀態。 如果同一設備上有多個使用者，則當這些使用者登錄時，將單獨清理雙狀態。
- 您可以通過添加此登錄機碼 - HKLM_SOFTWARE_政策\微軟_Windows_工作區聯接，"BlockAAD WorkplaceJoin"\dword：00000001 來防止域加入的設備被 Azure AD 註冊。
- 在 Windows 10 1803 中，如果配置了適用于企業的 Windows Hello，則使用者需要在雙狀態清理後重新設置適用于企業的 Windows Hello。此問題已通過 KB4512509 得到解決

> [!NOTE]
> 如果 Azure AD 註冊設備由 Intune 管理，則不會自動刪除該設備。

### <a name="additional-considerations"></a>其他考量
- 如果您的環境使用虛擬桌面基礎結構 （VDI），請參閱[設備標識和桌面虛擬化](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure)。

- 符合 FIPS 的 TPM 2.0 支援混合 Azure AD 聯接，TPM 1.2 不支援混合 Azure AD 聯接。 如果設備具有符合 FIPS 的 TPM 1.2，則必須在繼續混合 Azure AD 聯接之前禁用它們。 Microsoft 不提供任何用於禁用 TPM FIPS 模式的工具，因為它依賴于 TPM 製造商。 請聯繫您的硬體 OEM 以獲取支援。 

- 從 Windows 10 1903 版本開始，TPM 1.2 不與混合 Azure AD 聯接一起使用，並且具有這些 TPM 的設備將被視為沒有 TPM。

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>查看混合 Azure AD 聯接的控制驗證

當所有先決條件都到位時，Windows 設備將自動註冊為 Azure AD 租戶中的設備。 Azure AD 中的這些設備標識的狀態稱為混合 Azure AD 聯接。 有關本文仲介紹的概念的詳細資訊，請參閱[Azure 活動目錄中的設備標識管理簡介](overview.md)。

組織可能需要對混合 Azure AD 聯接進行受控驗證，然後再在整個組織中同時啟用它。 查看混合[Azure AD 聯接的受控驗證](hybrid-azuread-join-control.md)文章，瞭解如何完成它。

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>根據您的身份基礎結構選擇方案

混合 Azure AD 聯接適用于兩者、託管和聯合環境，具體取決於 UPN 是可路由還是不可路由。 有關支援的方案，請參閱頁面底部。  

### <a name="managed-environment"></a>受控環境

受控環境可使用[無縫單一登入](/azure/active-directory/hybrid/how-to-connect-sso)透過[密碼雜湊同步 (PHS)](/azure/active-directory/hybrid/whatis-phs) 或[傳遞驗證 (PTA)](/azure/active-directory/hybrid/how-to-connect-pta) 進行部署。

在這些案例中，您不需要設定同盟伺服器以進行驗證。

### <a name="federated-environment"></a>同盟環境

同盟環境應具有支援下列需求的識別提供者。 如果您的同盟環境使用 Active Directory 同盟服務 (AD FS)，則已支援下列需求。

- **維奧多奧奧奧森索賠：** 此聲明需要為 Windows 低級設備執行混合 Azure AD 聯接。
- **WS-信任協定：** 此協定需要通過 Azure AD 對 Windows 當前混合 Azure AD 聯接的設備進行身份驗證。 當您使用 AD FS 時，您必須啟用下列 WS-Trust 端點：`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 或 **adfs/services/trust/13/windowstransport** 都只能啟用為內部網路對應端點，且不得透過 Web 應用程式 Proxy 公開為內部網路對應端點。 若要深入了解如何停用 WS-Trust Windows 端點，請參閱[在 Proxy上停用 WS-Trust Windows 端點](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)。 您可以在**服務** > **終結點**下通過 AD FS 管理主控台查看啟用哪些終結點。

> [!NOTE]
> Azure AD 不支援受控網域中的智慧卡或憑證。

從 1.1.819.0 版開始，Azure AD Connect 即為您提供用來設定混合式 Azure AD Join 的精靈。 此精靈可讓您大幅簡化設定程序。 如果安裝必要的 Azure AD Connect 版本不適合您，請參閱[如何手動設定裝置註冊](hybrid-azuread-join-manual.md)。 

根據與您的標識基礎結構匹配的方案，請參閱：

- [為聯合環境配置混合 Azure 活動目錄聯接](hybrid-azuread-join-federated-domains.md)
- [為託管環境配置混合 Azure 活動目錄聯接](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>查看本地 AD 使用者 UPN 對混合 Azure AD 聯接的支援

有時，本地 AD 使用者 UPN 可能不同于 Azure AD UPN。 在此情況下，Windows 10 混合式 Azure AD Join 會根據[驗證方法](/azure/security/fundamentals/choose-ad-authn)、網域類型和 Windows 10 版本，提供有限的內部部署 AD UPN 支援。 您的環境中可以有兩種類型的內部部署 AD UPN：

- 可路由使用者 UPN：可路由的 UPN 具有有效的已驗證域，該域註冊商註冊。 例如，如果 Azure AD 中的主要網域為 contoso.com，則 contoso.org 在 Contoso 所擁有且[已在 Azure AD 中驗證](/azure/active-directory/fundamentals/add-custom-domain)的內部部署 AD 中是主要網域
- 不可路由使用者 UPN：不可路由的 UPN 沒有經過驗證的域。 它僅適用於組織的私人網路內。 例如，如果 Azure AD 中的主要網域為 contoso.com，則 contoso.local 是內部部署 AD 中的主要網域，但不是網際網路中可驗證的網域，而且只能在 Contoso 的網路內使用。

> [!NOTE]
> 本節中的資訊僅適用于本地使用者 UPN。 它不適用於本地電腦域尾碼（例如：電腦1.contoso.local）。

下表提供有關在 Windows 10 混合式 Azure AD Join 中支援這些內部部署 AD UPN 的詳細資料

| 內部部署 AD UPN 的類型 | 網域類型 | Windows 10 版本 | 描述 |
| ----- | ----- | ----- | ----- |
| 路由式 | 同盟 | 自 1703 版起 | 正式推出 |
| 非可路由傳送 | 同盟 | 自 1803 版起 | 正式推出 |
| 路由式 | 受控 | 自 1803 版起 | 通常可用，不支援 Windows 鎖屏上的 Azure AD SSPR |
| 非可路由傳送 | 受控 | 不支援 | |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [為聯合環境](hybrid-azuread-join-federated-domains.md)
> 配置混合 Azure 活動目錄聯接[為託管環境配置混合 Azure 活動目錄聯接](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
