---
title: 在條件式存取原則中授與控制項-Azure Active Directory
description: 什麼是 Azure AD 條件式存取原則中的授與控制項
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1d30a32a58dd2385a214d813307c645c56afdc8
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024439"
---
# <a name="conditional-access-grant"></a>條件式存取：授與

在條件式存取原則中，系統管理員可以利用存取控制來授與或封鎖資源的存取權。

![具有需要多重要素驗證之 grant 控制項的條件式存取原則](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>封鎖存取

[封鎖] 會將任何指派納入考慮，並根據條件式存取原則設定來防止存取。

「封鎖」是一種功能強大的控制項，應該以適當的知識絕大部分是。 具有 block 語句的原則可能會產生非預期的副作用。 適當的測試和驗證在大規模啟用之前十分重要。 系統管理員應該在進行變更時，利用條件式存取中的工具，例如[條件式存取報表專用模式](concept-conditional-access-report-only.md)和[What If 工具](what-if-tool.md)。

## <a name="grant-access"></a>授與存取權

系統管理員可以選擇在授與存取權時，強制執行一或多個控制項。 這些控制項包括下列選項： 

- [需要多重要素驗證（Azure 多重要素驗證）](../authentication/concept-mfa-howitworks.md)
- [需要將裝置標示為符合規範（Microsoft Intune）](/intune/protect/device-compliance-get-started)
- [需要已加入混合式 Azure AD 裝置](../devices/concept-azure-ad-join-hybrid.md)
- [需要已核准的用戶端應用程式]
- [需要應用程式保護原則](app-protection-based-conditional-access.md)
- [需要密碼變更](#require-password-change)

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

### <a name="require-hybrid-azure-ad-joined-device"></a>需要已加入混合式 Azure AD 的裝置

組織可以選擇使用裝置身分識別作為其條件式存取原則的一部分。 組織可以要求裝置必須使用此核取方塊來加入混合式 Azure AD。 如需裝置身分識別的詳細資訊，請參閱[什麼是裝置身分識別？一](../devices/overview.md)文。

使用[裝置程式碼 OAuth 流程](../develop/v2-oauth2-device-code.md)時，不支援 [需要受控裝置授與控制] 或 [裝置狀態] 條件。 這是因為執行驗證的裝置無法將其裝置狀態提供給提供程式碼的裝置，而且權杖中的裝置狀態會鎖定給執行驗證的裝置。 請改用 [需要多重要素驗證] 授與控制。

### <a name="require-approved-client-app"></a>需要已核准的用戶端應用程式

組織可能需要從已核准的用戶端應用程式，對所選雲端應用程式的存取嘗試進行存取。 這些已核准的用戶端應用程式支援與任何行動裝置管理（MDM）解決方案無關的[Intune 應用程式保護原則](/intune/app-protection-policy)。

若要利用此授與控制，條件式存取會要求裝置必須在需要使用 broker 應用程式的 Azure Active Directory 中註冊。 訊息代理程式應用程式可以是適用於 iOS 的 Microsoft 驗證器，或適用於 Android 裝置的 Microsoft 公司入口網站。 當使用者嘗試驗證時，如果裝置上未安裝 broker 應用程式，則會將使用者重新導向至 app store 以安裝 broker 應用程式。

此設定適用于下列 iOS 和 Android 應用程式：

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

**備註**

- 經過核准的用戶端應用程式支援 Intune 行動應用程式管理功能。
- **需要經過核准的用戶端應用程式**需求：
   - 僅支援裝置平台條件適用的 iOS 和 Android。
   - 需要有 broker 應用程式才能註冊裝置。 在 iOS 上，broker 應用程式是 Microsoft Authenticator 且在 Android 上，它是 Intune 公司入口網站應用程式。
- 條件式存取在 InPrivate 模式中無法將 Microsoft Edge 視為已核准的用戶端應用程式。

如需設定範例，請參閱[如何：使用條件式存取針對雲端應用程式存取要求核准的用戶端應用程式](app-based-conditional-access.md)一文。

### <a name="require-app-protection-policy"></a>需要應用程式保護原則

在您的條件式存取原則中，您可以要求用戶端應用程式上有[Intune 應用程式保護原則](/intune/app-protection-policy)，才能存取所選的雲端應用程式。 

若要利用此授與控制，條件式存取會要求裝置必須在需要使用 broker 應用程式的 Azure Active Directory 中註冊。 訊息代理程式應用程式可以是適用於 iOS 的 Microsoft 驗證器，或適用於 Android 裝置的 Microsoft 公司入口網站。 當使用者嘗試驗證時，如果裝置上未安裝 broker 應用程式，則會將使用者重新導向至 app store 以安裝 broker 應用程式。

此設定適用於下列用戶端應用程式：

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**備註**

- 應用程式保護原則的應用程式支援具有原則保護的 Intune 行動應用程式管理功能。
- **需要應用程式保護原則**需求：
    - 僅支援裝置平台條件適用的 iOS 和 Android。
    - 需要有 broker 應用程式才能註冊裝置。 在 iOS 上，broker 應用程式是 Microsoft Authenticator 且在 Android 上，它是 Intune 公司入口網站應用程式。

如需設定範例，請參閱[如何：需要應用程式保護原則和核准的用戶端應用程式，以使用條件式存取進行雲端應用程式存取一](app-protection-based-conditional-access.md)文。

### <a name="require-password-change"></a>需要密碼變更 

當偵測到使用者風險時，使用使用者風險原則條件時，系統管理員可以選擇讓使用者使用 Azure AD 自助式密碼重設來安全地變更密碼。 如果偵測到使用者風險，使用者可以執行自助式密碼重設來進行自我補救，這會關閉使用者風險事件，以避免系統管理員不必要的干擾。 

當系統提示使用者變更其密碼時，必須先完成多重要素驗證。 您會想要確定您的所有使用者都已註冊多重要素驗證，以便在偵測到其帳戶發生風險的情況下進行準備。  

> [!WARNING]
> 使用者必須先註冊自助式密碼重設，才能觸發使用者風險原則。 

當您使用密碼變更控制來設定原則時，有幾項限制。  

1. 必須將原則指派給 [所有雲端應用程式]。 這可防止攻擊者使用不同的應用程式來變更使用者的密碼，以及重設帳戶的風險，方法是直接登入不同的應用程式。 
1. [需要密碼變更] 無法用於其他控制項，例如要求符合規範的裝置。  
1. 密碼變更控制只能與使用者和群組指派條件搭配使用，即雲端應用程式指派條件（必須設定為全部）和使用者風險條件。 

### <a name="terms-of-use"></a>使用規定

如果您的組織已建立使用規定，則在 [授與控制] 底下可能會顯示其他選項。 這些選項可讓系統管理員要求使用規定，做為存取原則所保護之資源的條件。 如需使用條款的詳細資訊，請參閱[Azure Active Directory 使用規定一](terms-of-use.md)文。

## <a name="next-steps"></a>後續步驟

- [條件式存取：會話控制項](concept-conditional-access-session.md)

- [條件式存取一般原則](concept-conditional-access-policy-common.md)

- [僅限報告模式](concept-conditional-access-report-only.md)
