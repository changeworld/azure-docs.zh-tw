---
title: 規劃混合式 Azure Active Directory 聯結-Azure Active Directory
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
ms.openlocfilehash: 22ab3e7403069ed1b579631b88c2ac2c41191ecd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181319"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>如何：規劃混合式 Azure Active Directory 聯結執行

以類似的使用方式，裝置是您想要保護的另一個核心身分識別，可用來隨時隨地保護您的資源。 您可以使用下列其中一種方法，將裝置身分識別導入 Azure AD 中進行管理，以達到此目標：

- Azure AD Join
- 混合式 Azure AD Join
- Azure AD 註冊

將您的裝置導入 Azure AD 中，您將可透過跨雲端和內部部署資源的單一登入 (SSO)，將使用者的生產力最大化。 同時，您可以使用[條件式存取](../active-directory-conditional-access-azure-portal.md)來保護對雲端和內部部署資源的存取。

如果您有內部部署 Active Directory （AD）環境，而且想要將已加入 AD 網域的電腦加入 Azure AD，您可以執行混合式 Azure AD 聯結來完成這項作業。 本文提供在您的環境中實作混合式 Azure AD Join 的相關步驟。 

## <a name="prerequisites"></a>Prerequisites

本文假設您已熟悉[Azure Active Directory 中的裝置身分識別管理簡介](../device-management-introduction.md)。

> [!NOTE]
> Windows 10 混合式 Azure AD 加入所需的最低網域控制站版本為 Windows Server 2008 R2。

## <a name="plan-your-implementation"></a>計劃您的實作

若要規劃您的混合式 Azure AD 實作，您應該熟悉：

|   |   |
| --- | --- |
| ![勾選][1] | 檢閱支援的裝置 |
| ![勾選][1] | 檢閱您應該知道的事情 |
| ![勾選][1] | 審查混合式 Azure AD 聯結的受控制驗證 |
| ![勾選][1] | 根據您的身分識別基礎結構來選取您的案例 |
| ![勾選][1] | 檢查混合式 Azure AD 聯結的內部部署 AD UPN 支援 |

## <a name="review-supported-devices"></a>檢閱支援的裝置

混合式 Azure AD Join 支援廣泛的 Windows 裝置。 因為執行舊版 Windows 的裝置設定需要其他或不同的步驟，所以支援的裝置會分為兩類：

### <a name="windows-current-devices"></a>現行 Windows 裝置

- Windows 10
- Windows Server 2016
  - **注意**： Azure 國家/地區雲端客戶需要版本1809
- Windows Server 2019

對於執行 Windows 桌面作業系統的裝置，支援的版本會列在這篇文章中的[windows 10 版本資訊](/windows/release-information/)。 最佳作法是 Microsoft 建議您升級至最新版本的 Windows 10。

### <a name="windows-down-level-devices"></a>舊版 Windows 裝置

- Windows 8.1
- Windows 7 支援已於 2020 年 1 月 14 日終止。 如需詳細資訊，請參閱[Windows 7 的支援已結束](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)。
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2。 如需 Windows Server 2008 和 2008 R2 的支援資訊，請參閱[準備 Windows server 2008 終止支援](https://www.microsoft.com/cloud-platform/windows-server-2008)。

在第一個規劃步驟中，您應該檢閱您的環境，並判斷是否需要支援 Windows 舊版裝置。

## <a name="review-things-you-should-know"></a>檢閱您應該知道的事情

### <a name="unsupported-scenarios"></a>不支援的情節
- 如果您的環境包含將識別資料同步處理至多個 Azure AD 租使用者的單一 AD 樹系，則目前不支援混合式 Azure AD 聯結。

- 執行網域控制站（DC）角色的 Windows Server 不支援混合式 Azure AD 聯結。

- 使用認證漫遊或使用者設定檔漫遊或強制設定檔時，舊版 Windows 裝置不支援混合式 Azure AD 聯結。

- Server Core OS 不支援任何類型的裝置註冊。

### <a name="os-imaging-considerations"></a>OS 映射考慮
- 如果您依賴系統準備工具（Sysprep），而且您使用**Windows 前 10 1809**映射來進行安裝，請確定映射不是來自已向 Azure AD 註冊的裝置混合式 Azure AD 聯結。

- 如果您依賴虛擬機器（VM）快照集來建立其他 Vm，請確定快照集不是來自已向 Azure AD 註冊的 VM，因為混合式 Azure AD 聯結。

- 如果您使用的是[整合寫入篩選器](/windows-hardware/customize/enterprise/unified-write-filter)，以及在重新開機時清除磁片變更的類似技術，則必須在裝置混合式 Azure AD 聯結之後套用。 在混合式 Azure AD 聯結完成之前啟用這類技術，會導致裝置在每次重新開機時都無法退出

### <a name="handling-devices-with-azure-ad-registered-state"></a>處理具有 Azure AD 註冊狀態的裝置
如果您已加入 Windows 10 網域的裝置 Azure AD 向您的租使用者[註冊](overview.md#getting-devices-in-azure-ad)，則可能會導致混合式 Azure AD 已加入的雙重狀態並 Azure AD 已註冊的裝置。 建議您升級至 Windows 10 1803 （已套用 KB4489894）或更新版本，以自動解決這種情況。 在1803之前的版本中，您必須先手動移除 Azure AD 註冊狀態，才能啟用混合式 Azure AD 聯結。 在1803和更新版本中，已進行下列變更以避免這種雙重狀態：

- <i>當裝置已加入混合式 Azure AD，而且使用者登入之後</i>，系統會自動移除任何現有的 Azure AD 註冊狀態。 例如，如果使用者 A 在裝置上有 Azure AD 註冊的狀態，則只有在使用者 A 登入裝置時，才會清除使用者 A 的雙重狀態。 如果相同的裝置上有多個使用者，當這些使用者登入時，會個別清除雙重狀態。
- 您可以將下列登錄值新增至 HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin，以防止加入網域的裝置 Azure AD 註冊： "BlockAADWorkplaceJoin" = dword：00000001。
- 在 Windows 10 1803 中，如果您已設定 Windows Hello 企業版，則使用者必須在雙重狀態清除之後重新設定 Windows Hello 企業版。KB4512509 已解決此問題

> [!NOTE]
> 如果 Azure AD 已註冊的裝置受 Intune 管理，則不會自動移除。

### <a name="additional-considerations"></a>其他考量
- 如果您的環境使用虛擬桌面基礎結構（VDI），請參閱[裝置身分識別和桌面虛擬化](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure)。

- 符合 FIPS 規範的 TPM 2.0 支援混合式 Azure AD 聯結，TPM 1.2 則不支援。 如果您的裝置具有 FIPS 相容的 TPM 1.2，您必須先停用它們，再繼續進行混合式 Azure AD 聯結。 Microsoft 不會提供任何工具來停用 Tpm 的 FIPS 模式，因為它相依于 TPM 製造商。 請洽詢您的硬體 OEM 以取得支援。 

- 從 Windows 10 1903 版本開始，Tpm 1.2 不會與混合式 Azure AD 聯結搭配使用，而那些 Tpm 的裝置將被視為沒有 TPM。

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>審查混合式 Azure AD 聯結的受控制驗證

當所有必要條件都已就緒時，Windows 裝置會自動在您的 Azure AD 租使用者中註冊為裝置。 這些裝置身分識別在 Azure AD 中的狀態稱為混合式 Azure AD 聯結。 如需本文所涵蓋概念的詳細資訊，請參閱[Azure Active Directory 中的裝置身分識別管理簡介](overview.md)一文。

組織可能會想要對混合式 Azure AD 聯結進行受控制的驗證，然後一次在整個組織中啟用它。 請參閱[受控制的混合式 Azure AD 聯結驗證一](hybrid-azuread-join-control.md)文，以瞭解如何完成。

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>根據您的身分識別基礎結構來選取您的案例

視 UPN 是否可路由傳送或無法路由傳送，混合式 Azure AD 聯結適用于、受控和同盟環境。 如需支援的案例，請參閱資料表的底部頁面。  

### <a name="managed-environment"></a>受控環境

受控環境可使用[無縫單一登入](/azure/active-directory/hybrid/how-to-connect-sso)透過[密碼雜湊同步 (PHS)](/azure/active-directory/hybrid/whatis-phs) 或[傳遞驗證 (PTA)](/azure/active-directory/hybrid/how-to-connect-pta) 進行部署。

在這些案例中，您不需要設定同盟伺服器以進行驗證。

### <a name="federated-environment"></a>同盟環境

同盟環境應具有支援下列需求的識別提供者。 如果您的同盟環境使用 Active Directory 同盟服務 (AD FS)，則已支援下列需求。

- **WIAORMULTIAUTHN 宣告：** 需要此宣告，才能執行適用于舊版 Windows 裝置的混合式 Azure AD 聯結。
- **Ws-trust 通訊協定：** 必須使用此通訊協定，才能向 Azure AD 驗證已加入 Windows 的混合式 Azure AD 裝置。 當您使用 AD FS 時，您必須啟用下列 WS-Trust 端點：`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 或 **adfs/services/trust/13/windowstransport** 都只能啟用為內部網路對應端點，且不得透過 Web 應用程式 Proxy 公開為內部網路對應端點。 若要深入了解如何停用 WS-Trust Windows 端點，請參閱[在 Proxy上停用 WS-Trust Windows 端點](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)。 您可以在 AD FS 管理主控台的 [服務]   > [端點]  下方查看已啟用的端點。

> [!NOTE]
> Azure AD 不支援受控網域中的智慧卡或憑證。

從 1.1.819.0 版開始，Azure AD Connect 即為您提供用來設定混合式 Azure AD Join 的精靈。 此精靈可讓您大幅簡化設定程序。 如果安裝必要的 Azure AD Connect 版本不適合您，請參閱[如何手動設定裝置註冊](hybrid-azuread-join-manual.md)。 

根據符合您身分識別基礎結構的案例，請參閱：

- [設定聯合環境的混合式 Azure Active Directory 聯結](hybrid-azuread-join-federated-domains.md)
- [設定受管理環境的混合式 Azure Active Directory 聯結](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>審查內部部署 AD 使用者混合式 Azure AD 聯結的 UPN 支援

有時候，您的內部部署 AD 使用者 Upn 可能會與您的 Azure AD Upn 不同。 在此情況下，Windows 10 混合式 Azure AD Join 會根據[驗證方法](/azure/security/fundamentals/choose-ad-authn)、網域類型和 Windows 10 版本，提供有限的內部部署 AD UPN 支援。 您的環境中可以有兩種類型的內部部署 AD UPN：

- 可路由使用者 UPN：可路由的 UPN 具有有效的已驗證網域，並已向網域註冊機構註冊。 例如，如果 Azure AD 中的主要網域為 contoso.com，則 contoso.org 在 Contoso 所擁有且[已在 Azure AD 中驗證](/azure/active-directory/fundamentals/add-custom-domain)的內部部署 AD 中是主要網域
- 不可路由傳送的使用者 UPN：不可路由傳送的 UPN 沒有已驗證的網域。 它僅適用於組織的私人網路內。 例如，如果 Azure AD 中的主要網域為 contoso.com，則 contoso.local 是內部部署 AD 中的主要網域，但不是網際網路中可驗證的網域，而且只能在 Contoso 的網路內使用。

> [!NOTE]
> 本節中的資訊僅適用于內部部署使用者 UPN。 它不適用於內部部署電腦網域尾碼（例如： computer1）。

下表提供有關在 Windows 10 混合式 Azure AD Join 中支援這些內部部署 AD UPN 的詳細資料

| 內部部署 AD UPN 的類型 | 網域類型 | Windows 10 版本 | 描述 |
| ----- | ----- | ----- | ----- |
| 路由式 | 同盟 | 自 1703 版起 | 正式推出 |
| 非可路由傳送 | 同盟 | 自 1803 版起 | 正式推出 |
| 路由式 | 受管理 | 自 1803 版起 | 已正式推出，不支援 Windows 鎖屏上的 Azure AD SSPR |
| 非可路由傳送 | 受管理 | 不支援 | |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定同盟環境](hybrid-azuread-join-federated-domains.md)
> 的混合式 Azure Active Directory 聯結[設定受管理環境的混合式 Azure Active Directory 聯結](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
