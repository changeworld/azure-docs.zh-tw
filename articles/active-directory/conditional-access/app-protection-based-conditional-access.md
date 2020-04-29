---
title: 具有條件式存取的應用程式保護原則-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 中使用條件式存取來要求應用程式保護原則以進行雲端應用程式存取。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b282962cc713487b8ee5113b02b8533a1538fff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80631887"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>作法：需要應用程式保護原則和核准的用戶端應用程式，以使用條件式存取來存取雲端應用程式

人們會定期使用其行動裝置來進行個人和工作。 同時確保員工的生產力，組織也會想要防止可能不安全的應用程式遺失資料。 透過條件式存取，組織可以限制對已套用 Intune 應用程式保護原則的已核准（具備新式驗證功能）用戶端應用程式的存取權。

本文提供兩種案例來設定適用于 Office 365、Exchange Online 和 SharePoint Online 等資源的條件式存取原則。

- [案例1： Office 365 應用程式需要具有應用程式保護原則的已核准應用程式](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [案例2： Exchange Online 和 SharePoint Online 需要已核准的用戶端應用程式和應用程式保護原則](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

在條件式存取中，這些用戶端應用程式已知會受到應用程式保護原則的保護。 應用程式保護原則的詳細資訊可在[應用程式保護原則總覽](/intune/apps/app-protection-policy)一文中找到。

如需合格用戶端應用程式的清單，請參閱[應用程式保護原則需求](concept-conditional-access-grant.md)。

> [!NOTE]
>    在原則中使用或子句，讓使用者能夠利用支援「**需要應用程式保護原則**」或「**需要核准的用戶端應用程式**授與控制」的應用程式。 如需哪些應用程式支援「**需要應用程式保護原則**」授與控制的詳細資訊，請參閱[應用程式保護原則需求](concept-conditional-access-grant.md)。

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>案例1： Office 365 應用程式需要具有應用程式保護原則的已核准應用程式

在此案例中，Contoso 已決定所有 Office 365 資源的行動存取都必須使用核准的用戶端應用程式，例如 Outlook mobile、OneDrive 和 Microsoft 小組在收到存取權之前受到應用程式保護原則的保護。 他們的所有使用者都已使用 Azure AD 認證登入，並指派授權給他們，其中包括 Azure AD Premium P1 或 P2 及 Microsoft Intune。

組織必須完成下列步驟，才能要求在行動裝置上使用已核准的用戶端應用程式。

**步驟1：設定 Office 365 的 Azure AD 條件式存取原則**

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入**Azure 入口網站**。
1. 流覽至**Azure Active Directory** > **安全性** > **條件式存取**。
1. 選取 [新增原則]****。
1. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成]  。
1. 在 [**雲端應用程式] 或 [動作** > **包括**] 底下，選取 **[Office 365 （預覽）**]。
1. 在 [**條件**] 底下，選取 [**裝置平臺**]。
   1. 將 **[設定] 設為** **[是]**。
   1. 包含**Android**和**iOS**。
1. 在 [**條件**] 底下，選取 **[用戶端應用程式（預覽）**]。
   1. 將 **[設定] 設為** **[是]**。
   1. 選取 [行動應用程式與桌面用戶端]  和 [新式驗證用戶端]  。
1. 在 [**存取控制** > **授**與] 底下，選取下列選項：
   - **需要經過核准的用戶端應用程式**
   - **需要應用程式保護原則 (預覽)**
   - **需要所有選取的控制項**
1. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
1. 選取 [**建立**] 以建立並啟用您的原則。

**步驟2：使用 ActiveSync （EAS）設定適用于 Exchange Online 的 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，請設定下列元件：

1. 流覽至**Azure Active Directory** > **安全性** > **條件式存取**。
1. 選取 [新增原則]****。
1. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成]  。
1. 在 [**雲端應用程式] 或 [動作** > **包括**] 底下，選取 [ **Office 365 Exchange Online**]。
1. 在 [**條件**] 底下：
   1. **用戶端應用程式（預覽）**：
      1. 將 **[設定] 設為** **[是]**。
      1. 選取 [行動裝置**應用程式和桌面用戶端**] 和 [ **Exchange ActiveSync 用戶端**]。
1. 在 **[存取控制** > **授**與] 底下，選取 **[授與存取權**，**需要應用程式保護原則**]，然後選取 [**選取**
1. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
1. 選取 [**建立**] 以建立並啟用您的原則。

**步驟3：設定適用于 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**

如需建立 Android 和 iOS 應用程式保護原則的步驟，請參閱[如何建立及指派應用程式保護原則](/intune/apps/app-protection-policies)一文。 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>案例2： Exchange Online 和 SharePoint Online 需要已核准的用戶端應用程式和應用程式保護原則

在此案例中，Contoso 已決定使用者只能存取行動裝置上的電子郵件和 SharePoint 資料，只要他們使用經核准的用戶端應用程式（例如，在接收存取權之前，受應用程式保護原則保護的 Outlook mobile）即可。 他們的所有使用者都已使用 Azure AD 認證登入，並指派授權給他們，其中包括 Azure AD Premium P1 或 P2 及 Microsoft Intune。

組織必須完成下列三個步驟，才能要求在行動裝置和 Exchange ActiveSync 用戶端上使用已核准的用戶端應用程式。

**步驟1：以 Android 和 iOS 為基礎的新式驗證用戶端原則，需要在存取 Exchange Online 和 SharePoint Online 時使用已核准的用戶端應用程式和應用程式保護原則。**

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入**Azure 入口網站**。
1. 流覽至**Azure Active Directory** > **安全性** > **條件式存取**。
1. 選取 [新增原則]****。
1. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成]  。
1. 在 [**雲端應用程式] 或 [動作** > **包括**] 底下，選取 [ **Office 365 Exchange online**和**office 365 SharePoint online**]。
1. 在 [**條件**] 底下，選取 [**裝置平臺**]。
   1. 將 **[設定] 設為** **[是]**。
   1. 包含**Android**和**iOS**。
1. 在 [**條件**] 底下，選取 **[用戶端應用程式（預覽）**]。
   1. 將 **[設定] 設為** **[是]**。
   1. 選取 [行動應用程式與桌面用戶端]  和 [新式驗證用戶端]  。
1. 在 [**存取控制** > **授**與] 底下，選取下列選項：
   - **需要經過核准的用戶端應用程式**
   - **需要應用程式保護原則 (預覽)**
   - **需要其中一個選取的控制項**
1. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
1. 選取 [**建立**] 以建立並啟用您的原則。

**步驟2：需要使用已核准用戶端應用程式之 Exchange ActiveSync 用戶端的原則。**

1. 流覽至**Azure Active Directory** > **安全性** > **條件式存取**。
1. 選取 [新增原則]****。
1. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成]  。
1. 在 [**雲端應用程式] 或 [動作** > **包括**] 底下，選取 [ **Office 365 Exchange Online**]。
1. 在 [**條件**] 底下：
   1. **用戶端應用程式（預覽）**：
      1. 將 **[設定] 設為** **[是]**。
      1. 選取 [行動裝置**應用程式和桌面用戶端**] 和 [ **Exchange ActiveSync 用戶端**]。
1. 在 **[存取控制** > **授**與] 底下，選取 **[授與存取權**，**需要應用程式保護原則**]，然後選取 [**選取**
1. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
1. 選取 [**建立**] 以建立並啟用您的原則。

**步驟3：設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則。**

如需建立 Android 和 iOS 應用程式保護原則的步驟，請參閱[如何建立及指派應用程式保護原則](/intune/apps/app-protection-policies)一文。 

## <a name="next-steps"></a>後續步驟

[何謂條件式存取？](overview.md)

[條件式存取元件](concept-conditional-access-policies.md)

[一般條件式存取原則](concept-conditional-access-policy-common.md)

