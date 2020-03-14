---
title: 在條件式存取原則中授與控制項-Azure Active Directory
description: 什麼是 Azure AD 條件式存取原則中的授與控制項
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0716c2d4475bb538c06b9a591521fbdcfc0c80e4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263435"
---
# <a name="conditional-access-grant"></a>條件式存取：授與

在條件式存取原則中，系統管理員可以利用存取控制來授與或封鎖資源的存取權。

![具有需要多重要素驗證之 grant 控制項的條件式存取原則](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>封鎖存取

[封鎖] 會將任何指派納入考慮，並根據條件式存取原則設定來防止存取。

「封鎖」是一種功能強大的控制項，應該以適當的知識絕大部分是。 在啟用之前，系統管理員應該使用[僅限報告模式](concept-conditional-access-report-only.md)來進行測試。

## <a name="grant-access"></a>授與存取權

系統管理員可以選擇在授與存取權時，強制執行一或多個控制項。 這些控制項包括下列選項： 

- [需要多重要素驗證（Azure 多重要素驗證）](../authentication/concept-mfa-howitworks.md)
- [需要將裝置標示為符合規範（Microsoft Intune）](/intune/protect/device-compliance-get-started)
- [需要已加入混合式 Azure AD 裝置](../devices/concept-azure-ad-join-hybrid.md)
- [需要核准的用戶端應用程式](app-based-conditional-access.md)
- [需要應用程式保護原則](app-protection-based-conditional-access.md)

當系統管理員選擇結合這些選項時，可以選擇下列方法：

- 需要所有選取的控制項（控制項**和**控制項）
- 需要其中一個選取的控制項（控制項**或**控制項）

根據預設，條件式存取需要所有選取的控制項。

### <a name="require-multi-factor-authentication"></a>需要多重要素驗證

選取此核取方塊會要求使用者執行 Azure 多重要素驗證。 如需部署 Azure 多因素驗證的詳細資訊，請參閱[規劃雲端式 Azure 多因素驗證部署一](../authentication/howto-mfa-getstarted.md)文。

### <a name="require-device-to-be-marked-as-compliant"></a>裝置需要標記為符合規範

已部署 Microsoft Intune 的組織可以使用其裝置所傳回的資訊，來識別符合特定合規性需求的裝置。 此原則合規性資訊會從 Intune 轉送至 Azure AD，讓條件式存取可以決定要授與或封鎖資源的存取權。 如需相容性原則的詳細資訊，請參閱[使用 Intune 在裝置上設定規則以允許存取您組織中的資源一](/intune/protect/device-compliance-get-started)文。

Intune （適用于任何裝置作業系統）或協力廠商 MDM 系統（適用于 Windows 10 裝置）可能會將裝置標示為符合規範。 Jamf pro 是唯一支援的協力廠商 MDM 系統。 如需整合的詳細資訊，請參閱[整合 Jamf Pro 與 Intune 以取得相容性](/intune/protect/conditional-access-integrate-jamf)一文。

裝置必須先在 Azure AD 中註冊，才可以標示為符合規範。 如需裝置註冊的詳細資訊，請參閱[什麼是裝置身分識別一](../devices/overview.md)文。

### <a name="require-hybrid-azure-ad-joined-device"></a>需要已加入混合式 Azure AD 裝置

組織可以選擇使用裝置身分識別作為其條件式存取原則的一部分。 組織可以要求裝置必須使用此核取方塊來加入混合式 Azure AD。 如需裝置身分識別的詳細資訊，請參閱[什麼是裝置身分識別？一](../devices/overview.md)文。

### <a name="require-approved-client-app"></a>需要已核准的用戶端應用程式

組織可能需要從已核准的用戶端應用程式，對所選雲端應用程式的存取嘗試進行存取。 這些已核准的用戶端應用程式支援與任何行動裝置管理（MDM）解決方案無關的[Intune 應用程式保護原則](/intune/app-protection-policy)。

此設定適用于下列 iOS 和 Android 應用程式：

- Microsoft Azure 資訊保護
- Microsoft 預約
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

**備註**

- 經過核准的用戶端應用程式支援 Intune 行動應用程式管理功能。
- **需要經過核准的用戶端應用程式**需求：
   - 僅支援 iOS 和 Android 的裝置平臺條件。
- 條件式存取在 InPrivate 模式中無法將 Microsoft Edge 視為已核准的用戶端應用程式。

如需設定範例，請參閱[如何：使用條件式存取針對雲端應用程式存取要求核准的用戶端應用程式](app-based-conditional-access.md)一文。

### <a name="require-app-protection-policy"></a>需要應用程式保護原則

在您的條件式存取原則中，您可以要求用戶端應用程式上有[Intune 應用程式保護原則](/intune/app-protection-policy)，才能存取所選的雲端應用程式。 

此設定適用於下列用戶端應用程式：

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**備註**

- 應用程式保護原則的應用程式支援具有原則保護的 Intune 行動應用程式管理功能。
- **需要應用程式保護原則**需求：
    - 僅支援 iOS 和 Android 的裝置平臺條件。

如需設定範例，請參閱[如何：需要應用程式保護原則和核准的用戶端應用程式，以使用條件式存取進行雲端應用程式存取一](app-protection-based-conditional-access.md)文。

### <a name="terms-of-use"></a>使用規定

如果您的組織已建立使用規定，則在 [授與控制] 底下可能會顯示其他選項。 這些選項可讓系統管理員要求使用規定，做為存取原則所保護之資源的條件。 如需使用條款的詳細資訊，請參閱[Azure Active Directory 使用規定一](terms-of-use.md)文。

## <a name="next-steps"></a>後續步驟

- [條件式存取：會話控制項](concept-conditional-access-session.md)

- [條件式存取的一般原則](concept-conditional-access-policy-common.md)

- [僅限報表模式](concept-conditional-access-report-only.md)
