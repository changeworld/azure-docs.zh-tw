---
title: 具有條件式存取的已核准用戶端應用程式-Azure Active Directory
description: 瞭解如何透過 Azure Active Directory 中的條件式存取，要求已核准的用戶端應用程式存取雲端應用程式。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a869f4fa82999192f75f2c114720151bae55680
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298423"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>如何：透過條件式存取要求使用已核准的用戶端應用程式存取雲端應用程式

人們會定期使用其行動裝置來進行個人和工作。 同時確保員工的生產力，組織也會想要防止可能不安全的應用程式遺失資料。 透過條件式存取，組織可以限制對已核准（支援新式驗證）用戶端應用程式的存取。

本文提供兩種案例來設定適用于 Office 365、Exchange Online 和 SharePoint Online 等資源的條件式存取原則。

- [案例1： Office 365 應用程式需要已核准的用戶端應用程式](#scenario-1-office-365-apps-require-an-approved-client-app)
- [案例2： Exchange Online 和 SharePoint Online 需要已核准的用戶端應用程式](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

在條件式存取中，這項功能稱為需要核准的用戶端應用程式。 如需核准的用戶端應用程式清單，請參閱[核准的用戶端應用程式需求](concept-conditional-access-grant.md#require-approved-client-app)。

## <a name="scenario-1-office-365-apps-require-an-approved-client-app"></a>案例1： Office 365 應用程式需要已核准的用戶端應用程式

在此案例中，Contoso 已決定使用行動裝置的使用者可以存取所有 Office 365 服務，只要他們使用已核准的用戶端應用程式（例如 Outlook mobile、OneDrive 和 Microsoft 團隊）即可。 他們的所有使用者都已使用 Azure AD 認證登入，並指派授權給他們，其中包括 Azure AD Premium P1 或 P2 及 Microsoft Intune。

組織必須完成下列三個步驟，才能要求在行動裝置上使用已核准的用戶端應用程式。

**步驟1：以 Android 和 iOS 為基礎的新式驗證用戶端原則，需要在存取 Exchange Online 時使用已核准的用戶端應用程式。**

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入**Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成]。
1. 在 [**雲端應用程式] 或 [動作** > **包括**] 底下，選取 **[Office 365 （預覽）** ]。
1. 在 [**條件**] 底下，選取 [**裝置平臺**]。
   1. 將 **[設定] 設為** **[是]** 。
   1. 包含**Android**和**iOS**。
1. 在 [**條件**] 底下，選取 **[用戶端應用程式（預覽）** ]。
   1. 將 **[設定] 設為** **[是]** 。
   1. 選取 [行動裝置**應用程式和桌面用戶端**] 和 [**新式驗證用戶端**]。
1. 在 **[** **存取控制** > 授與] 底下，選取 **[授與存取權**，**需要核准的用戶端應用程式**]，**然後選取**
1. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
1. 選取 [**建立**] 以建立並啟用您的原則。

**步驟2：使用 ActiveSync （EAS）設定適用于 Exchange Online 的 Azure AD 條件式存取原則**

1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成]。
1. 在 [**雲端應用程式] 或 [動作** > **包括**] 底下，選取 [ **Office 365 Exchange Online**]。
1. 在 [**條件**] 底下：
   1. **用戶端應用程式（預覽）** ：
      1. 將 **[設定] 設為** **[是]** 。
      1. 選取 [行動裝置**應用程式和桌面用戶端**] 和 [ **Exchange ActiveSync 用戶端**]。
1. 在 **[** **存取控制** > 授與] 底下，選取 **[授與存取權**，**需要核准的用戶端應用程式**]，**然後選取**
1. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
1. 選取 [**建立**] 以建立並啟用您的原則。

**步驟3：設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則。**

如需建立 Android 和 iOS 應用程式保護原則的步驟，請參閱[如何建立及指派應用程式保護原則](/intune/apps/app-protection-policies)一文。 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>案例2： Exchange Online 和 SharePoint Online 需要已核准的用戶端應用程式

在此案例中，Contoso 已決定使用者只能存取行動裝置上的電子郵件和 SharePoint 資料，只要他們使用 Outlook mobile 等核准的用戶端應用程式即可。 他們的所有使用者都已使用 Azure AD 認證登入，並指派授權給他們，其中包括 Azure AD Premium P1 或 P2 及 Microsoft Intune。

組織必須完成下列三個步驟，才能要求在行動裝置和 Exchange ActiveSync 用戶端上使用已核准的用戶端應用程式。

**步驟1：以 Android 和 iOS 為基礎的新式驗證用戶端原則，需要在存取 Exchange Online 和 SharePoint Online 時使用已核准的用戶端應用程式。**

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入**Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成]。
1. 在 [**雲端應用程式] 或 [動作** > **包括**] 底下，選取 [ **Office 365 Exchange Online**和**office 365 SharePoint online**]。
1. 在 [**條件**] 底下，選取 [**裝置平臺**]。
   1. 將 **[設定] 設為** **[是]** 。
   1. 包含**Android**和**iOS**。
1. 在 [**條件**] 底下，選取 **[用戶端應用程式（預覽）** ]。
   1. 將 **[設定] 設為** **[是]** 。
   1. 選取 [行動裝置**應用程式和桌面用戶端**] 和 [**新式驗證用戶端**]。
1. 在 **[** **存取控制** > 授與] 底下，選取 **[授與存取權**，**需要核准的用戶端應用程式**]，**然後選取**
1. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
1. 選取 [**建立**] 以建立並啟用您的原則。

**步驟2：需要使用已核准用戶端應用程式之 Exchange ActiveSync 用戶端的原則。**

1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成]。
1. 在 [**雲端應用程式] 或 [動作** > **包括**] 底下，選取 [ **Office 365 Exchange Online**]。
1. 在 [**條件**] 底下：
   1. **用戶端應用程式（預覽）** ：
      1. 將 **[設定] 設為** **[是]** 。
      1. 選取 [行動裝置**應用程式和桌面用戶端**] 和 [ **Exchange ActiveSync 用戶端**]。
1. 在 **[** **存取控制** > 授與] 底下，選取 **[授與存取權**，**需要核准的用戶端應用程式**]，**然後選取**
1. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
1. 選取 [**建立**] 以建立並啟用您的原則。

**步驟3：設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則。**

如需建立 Android 和 iOS 應用程式保護原則的步驟，請參閱[如何建立及指派應用程式保護原則](/intune/apps/app-protection-policies)一文。 

## <a name="next-steps"></a>後續步驟

[什麼是條件式存取？](overview.md)

[條件式存取元件](concept-conditional-access-policies.md)

[一般條件式存取原則](concept-conditional-access-policy-common.md)
