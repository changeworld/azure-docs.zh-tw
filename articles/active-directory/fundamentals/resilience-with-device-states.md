---
title: 使用 Azure Active Directory 中的裝置狀態建立恢復功能
description: 架構設計人員和 IT 系統管理員指南，可使用裝置狀態建立恢復功能
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d476be7a417cfc31cca76d3409074aaaa281a56
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724605"
---
# <a name="build-resilience-with-device-states"></a>以裝置狀態建立恢復功能

藉由啟用 Azure AD 的 [裝置狀態](../devices/overview.md) ，系統管理員可以撰寫 [條件式存取原則](../conditional-access/overview.md) ，以根據裝置狀態控制應用程式的存取權。 裝置的額外優點是，它可滿足存取資源的強式驗證需求，進而減少額外的 MFA 驗證要求並改善復原能力。 

下列流程圖顯示在 Azure AD 中讓裝置上線以啟用裝置狀態的不同方式。 您可以在組織中使用一個以上的。

![選擇裝置狀態的流程圖](./media/resilience-with-device-states/admin-resilience-devices.png)

當您使用 [裝置狀態](../devices/overview.md)時，在大部分情況下，使用者會透過主要重新整理 [權杖](../devices/concept-primary-refresh-token.md) （ (PRT) 來體驗到資源的單一登入。 PRT 包含有關使用者和裝置的宣告，可用來取得驗證權杖以從裝置存取應用程式。 PRT 的有效期為14天，而且只要使用者主動使用裝置，就會持續更新，為使用者提供復原體驗。 PRT 也可以透過數種方式取得多重要素驗證宣告。 如需詳細資訊，請參閱 [PRT 取得 MFA](../devices/concept-primary-refresh-token.md)宣告的時機。

## <a name="how-do-device-states-help"></a>裝置狀態如何提供協助？

當 PRT 用來要求應用程式的存取權時，其裝置、會話和 MFA 宣告會受到 Azure AD 的信任。 當系統管理員建立需要以裝置為基礎的控制項或多重要素驗證控制的原則時，您可以透過裝置狀態達到原則需求，而不會嘗試進行多重要素驗證。 使用者在同一部裝置上不會看到其他多重要素驗證提示。 這會提高因 Azure MFA 服務或其相依性（例如本機電信提供者）中斷的復原能力。

## <a name="how-do-i-implement-device-states"></a>如何? 實施裝置狀態嗎？

* 針對公司擁有的 Windows 裝置啟用 [混合式 Azure AD 聯結](../devices/hybrid-azuread-join-plan.md) 和 [Azure AD 聯結](../devices/azureadjoin-plan.md) ，並在可能的情況下要求他們加入。 如果不可行，請要求他們註冊。

  如果您的組織中有較舊版本的 Windows，請將這些裝置升級為使用 Windows 10。

* 使用 [Microsoft Edge](/deployedge/microsoft-edge-security-identity) 或 Google Chrome 搭配 [支援](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)的 [延伸](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) 模組，將使用者瀏覽器的存取權標準化，以使用 PRT 啟用無縫 SSO 至 web 應用程式。

* 針對個人或公司擁有的 iOS 和 Android 裝置，部署 [Microsoft Authenticator 應用程式](../user-help/user-help-auth-app-overview.md)。 除了多重要素驗證和無密碼登入功能之外，Microsoft Authenticator 的應用程式會透過代理驗證，讓使用者能夠透過代理 [驗證](../develop/msal-android-single-sign-on.md) ，在原生應用程式之間啟用單一登入，以提供較少的終端使用者驗證提示。

* 針對個人或公司所擁有的 iOS 和 Android 裝置，使用行動 [應用程式管理](/mem/intune/apps/app-management) ，以較少的驗證要求安全地存取公司資源。 

* [使用適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式 (preview) ](../develop/apple-sso-plugin.md)。 這會註冊裝置，並在瀏覽器和原生 Azure AD 應用程式之間提供 SSO。 

## <a name="next-steps"></a>後續步驟
系統管理員和架構設計人員的復原能力資源
 
* [使用認證管理建立恢復功能](resilience-in-credentials.md)

* [使用連續存取評估來建立恢復功能 (CAE) ](resilience-with-continuous-access-evaluation.md)

* [在外部使用者驗證中建立恢復功能](resilience-b2b-authentication.md)

* [打造混合式驗證的復原能力](resilience-in-hybrid.md)

* [使用應用程式 Proxy 來建立應用程式存取的復原能力](resilience-on-premises-access.md)


開發人員的復原能力資源

* [在您的應用程式中建立 IAM 復原](resilience-app-development-overview.md)

* [在您的 CIAM 系統中打造復原能力](resilience-b2c.md)