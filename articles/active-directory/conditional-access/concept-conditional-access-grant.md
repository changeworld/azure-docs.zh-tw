---
title: 條件訪問策略中的授予控制項 - Azure 活動目錄
description: Azure AD 條件訪問策略中的授予控制項是什麼
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ec8dace971cd4dc1407c9e8d20839504c9ecc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331846"
---
# <a name="conditional-access-grant"></a>條件訪問：授予

在條件訪問策略中，管理員可以使用訪問控制項來授予或阻止對資源的訪問。

![具有需要多重要素驗證的授予控制的條件訪問策略](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>封鎖存取

塊會考慮任何分配，並阻止基於條件訪問策略配置的訪問。

塊是一個強大的控制項，應該用適當的知識來運用。 管理員應在啟用之前使用[僅報告模式](concept-conditional-access-report-only.md)進行測試。

## <a name="grant-access"></a>授與存取權

管理員可以選擇在授予存取權限時強制實施一個或多個控制項。 這些控制項包括以下選項： 

- [需要多重要素驗證（Azure 多重要素驗證）](../authentication/concept-mfa-howitworks.md)
- [要求設備標記為符合要求（微軟 Intune）](/intune/protect/device-compliance-get-started)
- [需要混合 Azure AD 聯接設備](../devices/concept-azure-ad-join-hybrid.md)
- [需要已核准的用戶端應用程式](app-based-conditional-access.md)
- [需要應用程式保護原則](app-protection-based-conditional-access.md)

當管理員選擇組合這些選項時，他們可以選擇以下方法：

- 要求所有選定的控制項（**控制和**控制項）
- 需要其中一個選定的控制項（控制項**或**控制項）

預設情況下，條件訪問需要所有選定的控制項。

### <a name="require-multi-factor-authentication"></a>需要多重要素驗證

選擇此核取方塊需要使用者執行 Azure 多重要素驗證。 有關部署 Azure 多重要素驗證的詳細資訊，請參閱規劃[基於雲的 Azure 多重要素驗證部署](../authentication/howto-mfa-getstarted.md)的文章。

### <a name="require-device-to-be-marked-as-compliant"></a>裝置需要標記為符合規範

部署 Microsoft Intune 的組織可以使用從其設備返回的資訊來標識滿足特定合規性要求的設備。 此策略合規性資訊從 Intune 轉發到 Azure AD，其中條件訪問可以做出授予或阻止對資源的訪問的決定。 有關合規性策略的詳細資訊，請參閱文章["在設備上設置規則，允許使用 Intune 訪問組織中的資源](/intune/protect/device-compliance-get-started)"。

Intune（對於任何設備作業系統）或 Windows 10 設備的協力廠商 MDM 系統可以標記為符合標準。 Jamf pro 是唯一受支援的協力廠商 MDM 系統。 有關集成的詳細資訊，請參閱文章，[將 Jamf Pro 與 Intune 集成，以便符合性](/intune/protect/conditional-access-integrate-jamf)。

設備必須在 Azure AD 中註冊，然後才能標記為符合要求。 有關設備註冊的詳細資訊，請參閱文章"[什麼是設備標識](../devices/overview.md)"。

### <a name="require-hybrid-azure-ad-joined-device"></a>需要混合 Azure AD 聯接設備

組織可以選擇將設備標識用作其條件訪問策略的一部分。 組織可以要求設備是使用此核取方塊聯接的混合 Azure AD。 有關設備標識的詳細資訊，請參閱文章["什麼是設備標識？"。](../devices/overview.md)

### <a name="require-approved-client-app"></a>需要已核准的用戶端應用程式

組織可以要求從批准的用戶端應用進行對所選雲應用的訪問嘗試。 這些批准的用戶端應用支援[Intune 應用保護原則](/intune/app-protection-policy)，獨立于任何行動裝置管理 （MDM） 解決方案。

為了利用此授予控制項，條件訪問要求將設備註冊在 Azure 活動目錄中，這需要使用代理應用。 訊息代理程式應用程式可以是適用於 iOS 的 Microsoft 驗證器，或適用於 Android 裝置的 Microsoft 公司入口網站。 如果使用者嘗試進行身份驗證時未在設備上安裝代理應用，則使用者將重定向到應用商店以安裝代理應用。

此設置適用于以下 iOS 和 Android 應用：

- Microsoft Azure 資訊保護
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune 受控瀏覽器
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- 微軟辦公中心
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft 商務用 Skype
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard

**言論**

- 經過核准的用戶端應用程式支援 Intune 行動應用程式管理功能。
- **需要經過核准的用戶端應用程式**需求：
   - 僅支援裝置平台條件適用的 iOS 和 Android。
   - 註冊設備需要代理應用。 在 iOS 上，代理應用程式是微軟身份驗證器，在 Android 上，它是 Intune 公司門戶應用。
- 條件訪問不能將 Microsoft 邊緣在 InPrivate 模式下視為已批准的用戶端應用。

請參閱文章"[如何操作：需要經過批准的用戶端應用才能使用條件訪問進行雲應用訪問](app-based-conditional-access.md)"，瞭解配置示例。

### <a name="require-app-protection-policy"></a>需要應用程式保護原則

在條件訪問策略中，您可以要求在用戶端應用上存在[Intune 應用保護原則](/intune/app-protection-policy)，然後才能訪問選定的雲應用。 

為了利用此授予控制項，條件訪問要求將設備註冊在 Azure 活動目錄中，這需要使用代理應用。 訊息代理程式應用程式可以是適用於 iOS 的 Microsoft 驗證器，或適用於 Android 裝置的 Microsoft 公司入口網站。 如果使用者嘗試進行身份驗證時未在設備上安裝代理應用，則使用者將重定向到應用商店以安裝代理應用。

此設定適用於下列用戶端應用程式：

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**言論**

- 應用保護原則的應用支援 Intune 移動應用程式管理功能，提供策略保護。
- **"需要應用保護原則**"要求：
    - 僅支援裝置平台條件適用的 iOS 和 Android。
    - 註冊設備需要代理應用。 在 iOS 上，代理應用程式是微軟身份驗證器，在 Android 上，它是 Intune 公司門戶應用。

請參閱文章"[如何：需要應用保護原則和已批准的用戶端應用，以便使用條件訪問訪問雲應用](app-protection-based-conditional-access.md)訪問"進行配置示例。

### <a name="terms-of-use"></a>使用規定

如果您的組織已創建使用條款，則授予控制項下可能可以看到其他選項。 這些選項允許管理員要求確認使用條款，作為訪問受策略保護的資源的條件。 有關使用條款的詳細資訊，請參閱["Azure 活動目錄"的使用條款](terms-of-use.md)。

## <a name="next-steps"></a>後續步驟

- [條件訪問：會話控制項](concept-conditional-access-session.md)

- [條件訪問通用策略](concept-conditional-access-policy-common.md)

- [僅限報告模式](concept-conditional-access-report-only.md)
