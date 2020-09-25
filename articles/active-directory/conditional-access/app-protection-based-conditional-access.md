---
title: 條件式存取的應用程式保護原則 - Azure Active Directory
description: 瞭解如何在 Azure Active Directory 中透過條件式存取要求雲端應用程式存取的應用程式防護原則。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03069bc37333cbd26dfe4f40ce4496f3afe768ed
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91266037"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>如何：透過條件式存取需要應用程式防護原則和已核准的用戶端應用程式，才能存取雲端應用程式

一般人平常使用其行動裝置來處理個人和工作事務。 組織在確保員工生產力的同時，也希望能防止可能不安全的應用程式所造成的資料遺失。 透過條件式存取，組織可以限制對已套用 Intune 應用程式保護原則的已核准 (具備新式驗證功能) 用戶端應用程式進行的存取。

本文提供三種案例來為資源（例如 Microsoft 365、Exchange Online 和 SharePoint）設定條件式存取原則。

- [案例1： Microsoft 365 apps 需要具有應用程式保護原則的已核准應用程式](#scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies)
- [案例 2：瀏覽器應用程式需要具有應用程式保護原則的已核准應用程式](#scenario-2-browser-apps-require-approved-apps-with-app-protection-policies)
- [案例3： Exchange Online 和 SharePoint 需要核准的用戶端應用程式和應用程式保護原則](#scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy)

在條件式存取中，這些用戶端應用程式已知會受到應用程式防護原則的保護。 如需應用程式保護原則的詳細資訊，請參閱[應用程式保護原則概觀](/intune/apps/app-protection-policy)一文

> [!WARNING]
> 並非所有應用程式都受核准的應用程式支援，也不支援應用程式保護原則。 如需合格用戶端應用程式的清單，請參閱[應用程式防護原則需求](concept-conditional-access-grant.md#require-app-protection-policy)。

> [!NOTE]
> Grant 控制項下的「需要其中一個選取的控制項」就像 OR 子句一樣。 這可在原則中使用，讓使用者能夠利用支援「 **需要應用程式保護」原則** 或 **需要核准的用戶端應用程式** 授與控制的應用程式。 如果兩個原則都支援應用程式，則需要強制執行**應用程式保護原則**。 如需哪些應用程式支援**需要應用程式防護原則**授與控制的詳細資訊，請參閱[應用程式防護原則需求](concept-conditional-access-grant.md#require-app-protection-policy)。

## <a name="scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies"></a>案例1： Microsoft 365 apps 需要具有應用程式保護原則的已核准應用程式

在此案例中，Contoso 已決定 Microsoft 365 資源的所有行動存取都必須使用已核准的用戶端應用程式（例如 Outlook mobile 和 OneDrive），以在接收存取權之前受到應用程式保護原則保護。 他們的所有使用者都已使用 Azure AD 認證登入，並指派授權給他們，其中包括 Azure AD Premium P1 或 P2 及 Microsoft Intune。

組織必須完成下列步驟，才能要求在行動裝置上使用已核准的用戶端應用程式。

**步驟1：設定 Microsoft 365 的 Azure AD 條件式存取原則**

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下，選取 [使用者和群組]
   1. 在 [包括] 底下，選取 [所有使用者] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成] 。
1. 在 [**雲端應用程式] 或 [動作**  >  **包括**] 下，選取 [ **Office 365**]。
1. 在 [條件] 下，選取 [裝置平台]。
   1. 將 [設定] 設定為 [是]。
   1. 包含 **Android** 和 **iOS**。
1. 在 [ **條件**] 底下，選取 [ **用戶端應用程式**]。
   1. 將 [設定] 設定為 [是]。
   1. 選取行動裝置 **應用程式和桌面用戶端** ，並取消選取其他專案。
1. 在 [存取控制] > [授與] 底下，選取下列選項：
   - [需要已核准的用戶端應用程式]
   - [需要應用程式防護原則 (預覽)]
   - [需要所有選取的控制項]
1. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
1. 選取 [建立] 以建立以啟用您的原則。

**步驟 2：使用 Active Sync (EAS)** 設定 Exchange Online 的 Azure AD 條件式存取原則

對於此步驟中的條件式存取原則，設定下列元件：

1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下，選取 [使用者和群組]
   1. 在 [包括] 底下，選取 [所有使用者] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成] 。
1. 在 [雲端應用程式或動作] > [包含] 底下，選取 [Office 365 Exchange Online]。
1. 在 [ **條件**] 底下，選取 [ **用戶端應用程式**]：
   1. 將 [設定] 設定為 [是]。
   1. 選取 **Exchange ActiveSync 用戶端** ，並取消選取其他所有專案。
1. 在 [存取控制] > [授與] 底下，選取 [授與存取權]、[需要應用程式防護原則]，然後選取 [選取]。
1. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
1. 選取 [建立] 以建立以啟用您的原則。

**步驟 3：設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**

如需建立適用於 Android 和 iOS 的應用程式保護原則的步驟，請參閱[如何建立和指派應用程式保護原則](/intune/apps/app-protection-policies)文章。 

## <a name="scenario-2-browser-apps-require-approved-apps-with-app-protection-policies"></a>案例 2：瀏覽器應用程式需要具有應用程式保護原則的已核准應用程式

在此案例中，Contoso 決定了 Microsoft 365 資源的所有行動網頁流覽存取權，都必須使用已核准的用戶端應用程式（例如 iOS 和 Android 的 Edge），並在接收存取權之前，受到應用程式保護原則的保護。 他們的所有使用者都已使用 Azure AD 認證登入，並指派授權給他們，其中包括 Azure AD Premium P1 或 P2 及 Microsoft Intune。

組織必須完成下列步驟，才能要求在行動裝置上使用已核准的用戶端應用程式。

**步驟1：設定 Microsoft 365 的 Azure AD 條件式存取原則**

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下，選取 [使用者和群組]
   1. 在 [包括] 底下，選取 [所有使用者] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成] 。
1. 在 [**雲端應用程式] 或 [動作**  >  **包括**] 下，選取 [ **Office 365**]。
1. 在 [條件] 下，選取 [裝置平台]。
   1. 將 [設定] 設定為 [是]。
   1. 包含 **Android** 和 **iOS**。
1. 在 [ **條件**] 底下，選取 [ **用戶端應用程式**]。
   1. 將 [設定] 設定為 [是]。
   1. 選取 [ **瀏覽器** ]，並取消選取其他專案。
1. 在 [存取控制] > [授與] 底下，選取下列選項：
   - [需要已核准的用戶端應用程式]
   - [需要應用程式防護原則 (預覽)]
   - [需要所有選取的控制項]
1. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
1. 選取 [建立] 以建立以啟用您的原則。

**步驟 2：設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**

如需建立適用於 Android 和 iOS 的應用程式保護原則的步驟，請參閱[如何建立和指派應用程式保護原則](/intune/apps/app-protection-policies)文章。 

## <a name="scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy"></a>案例3： Exchange Online 和 SharePoint 需要核准的用戶端應用程式和應用程式保護原則

在此案例中，Contoso 已決定使用者只能存取行動裝置上的電子郵件和 SharePoint 資料，只要他們使用經核准的用戶端應用程式 (例如，在接收存取權之前，受應用程式防護原則保護的 Outlook Mobile) 即可。 他們的所有使用者都已使用 Azure AD 認證登入，並指派授權給他們，其中包括 Azure AD Premium P1 或 P2 及 Microsoft Intune。

組織必須完成下列步驟，才能要求在行動裝置上使用已核准的用戶端應用程式和 Exchange ActiveSync 用戶端。

**步驟1：在存取 Exchange Online 和 SharePoint 時，需要使用已核准用戶端應用程式和應用程式保護原則的 Android 和 iOS 型新式驗證用戶端原則。**

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下，選取 [使用者和群組]
   1. 在 [包括] 底下，選取 [所有使用者] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成] 。
1. 在 [雲端應用程式或動作] > [包含] 底下，選取 [Office 365 Exchange Online] 和 [Office 365 SharePoint Online]。
1. 在 [條件] 下，選取 [裝置平台]。
   1. 將 [設定] 設定為 [是]。
   1. 包含 **Android** 和 **iOS**。
1. 在 [ **條件**] 底下，選取 [ **用戶端應用程式**]。
   1. 將 [設定] 設定為 [是]。
   1. 選取行動裝置 **應用程式和桌面用戶端** ，並取消選取其他專案。
1. 在 [存取控制] > [授與] 底下，選取下列選項：
   - [需要已核准的用戶端應用程式]
   - [需要應用程式防護原則 (預覽)]
   - [需要其中一個選取的控制項]
1. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
1. 選取 [建立] 以建立以啟用您的原則。

**步驟 2：需要使用已核准用戶端應用程式的 Exchange ActiveSync 用戶端原則。**

1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下，選取 [使用者和群組]
   1. 在 [包括] 底下，選取 [所有使用者] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成] 。
1. 在 [雲端應用程式或動作] > [包含] 底下，選取 [Office 365 Exchange Online]。
1. 在 [ **條件**] 底下，選取 [ **用戶端應用程式**]：
   1. 將 [設定] 設定為 [是]。
   1. 選取 **Exchange ActiveSync 用戶端** ，並取消選取其他所有專案。
1. 在 [存取控制] > [授與] 底下，選取 [授與存取權]、[需要應用程式防護原則]，然後選取 [選取]。
1. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
1. 選取 [建立] 以建立以啟用您的原則。

**步驟 3：設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則。**

如需建立適用於 Android 和 iOS 的應用程式保護原則的步驟，請參閱[如何建立和指派應用程式保護原則](/intune/apps/app-protection-policies)文章。 

## <a name="next-steps"></a>後續步驟

[何謂條件式存取？](overview.md)

[條件存取元件](concept-conditional-access-policies.md)

[一般條件式存取原則](concept-conditional-access-policy-common.md)

