---
title: 具有條件式存取的已核准用戶端應用程式-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 中使用條件式存取，要求已核准的用戶端應用程式存取雲端應用程式。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a3cdb851ca00300d995bcf2075252a360242197
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601957"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>操作說明：需要經過核准的用戶端應用程式使用條件式存取來存取雲端應用程式

一般人平常使用其行動裝置來處理個人和工作事務。 組織在確保員工生產力的同時，也希望能防止可能不安全的應用程式所造成的資料遺失。 使用條件式存取時，組織可以) 用戶端應用程式來限制對已核准 (新式驗證的存取權。

本文提供兩種案例來為資源（例如 Microsoft 365、Exchange Online 和 SharePoint Online）設定條件式存取原則。

- [案例1： Microsoft 365 apps 需要核准的用戶端應用程式](#scenario-1-microsoft-365-apps-require-an-approved-client-app)
- [案例2： Exchange Online 和 SharePoint Online 需要核准的用戶端應用程式](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

在條件式存取中，這項功能稱為需要核准的用戶端應用程式。 如需核准的用戶端應用程式清單，請參閱[核准的用戶端應用程式需求](concept-conditional-access-grant.md#require-approved-client-app)。

> [!NOTE]
> 為了需要適用于 iOS 和 Android 裝置的已核准用戶端應用程式，這些裝置必須先在 Azure AD 註冊。

## <a name="scenario-1-microsoft-365-apps-require-an-approved-client-app"></a>案例1： Microsoft 365 apps 需要核准的用戶端應用程式

在此案例中，Contoso 已決定使用行動裝置的使用者只要使用已核准的用戶端應用程式（例如 Outlook mobile、OneDrive 和 Microsoft 小組），就可以存取所有 Microsoft 365 服務。 他們的所有使用者都已使用 Azure AD 認證登入，並指派授權給他們，其中包括 Azure AD Premium P1 或 P2 及 Microsoft Intune。

組織必須完成下列三個步驟，才能要求在行動裝置上使用已核准的用戶端應用程式。

**步驟1：在存取 Exchange Online 時，需要使用已核准用戶端應用程式的 Android 和 iOS 型新式驗證用戶端原則。**

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下，選取 [使用者和群組]
   1. 在 [包括] 底下，選取 [所有使用者] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成] 。
1. 在 [雲端應用程式或動作] > [包含] 底下，選取 [Office 365 (預覽)]。
1. 在 [條件] 下，選取 [裝置平台]。
   1. 將 [設定] 設定為 [是]。
   1. 包含 **Android** 和 **iOS**。
1. 在 [條件] 下，選取 [用戶端應用程式 (預覽)]。
   1. 將 [設定] 設定為 [是]。
   1. 選取 [行動應用程式與桌面用戶端] 和 [新式驗證用戶端]。
1. 在 [**存取控制**  >  **授**與] 底下，選取 **[授與存取權**， ** ****要求已核准用戶端應用程式**]，然後選取
1. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
1. 選取 [建立] 以建立以啟用您的原則。

**步驟 2：使用 Active Sync (EAS)** 設定 Exchange Online 的 Azure AD 條件式存取原則

1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下，選取 [使用者和群組]
   1. 在 [包括] 底下，選取 [所有使用者] 或您想要套用此原則的特定**使用者和群組**。 
   1. 選取 [完成] 。
1. 在 [雲端應用程式或動作] > [包含] 底下，選取 [Office 365 Exchange Online]。
1. 在 [條件] 底下：
   1. **用戶端應用程式 (預覽)** ：
      1. 將 [設定] 設定為 [是]。
      1. 選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。
1. 在 [**存取控制**  >  **授**與] 底下，選取 **[授與存取權**， ** ****要求已核准用戶端應用程式**]，然後選取
1. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
1. 選取 [建立] 以建立以啟用您的原則。

**步驟 3：設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則。**

如需建立適用於 Android 和 iOS 的應用程式保護原則的步驟，請參閱[如何建立和指派應用程式保護原則](/intune/apps/app-protection-policies)文章。 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>案例2： Exchange Online 和 SharePoint Online 需要核准的用戶端應用程式

在此案例中，Contoso 已決定使用者只能存取行動裝置上的電子郵件和 SharePoint 資料，只要他們使用已核准的用戶端應用程式（例如 Outlook mobile）即可。 他們的所有使用者都已使用 Azure AD 認證登入，並指派授權給他們，其中包括 Azure AD Premium P1 或 P2 及 Microsoft Intune。

組織必須完成下列步驟，才能要求在行動裝置上使用已核准的用戶端應用程式和 Exchange ActiveSync 用戶端。

**步驟1：在存取 Exchange Online 和 SharePoint Online 時，需要使用已核准用戶端應用程式的 Android 和 iOS 型新式驗證用戶端原則。**

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
1. 在 [條件] 下，選取 [用戶端應用程式 (預覽)]。
   1. 將 [設定] 設定為 [是]。
   1. 選取 [行動應用程式與桌面用戶端] 和 [新式驗證用戶端]。
1. 在 [**存取控制**  >  **授**與] 底下，選取 **[授與存取權**， ** ****要求已核准用戶端應用程式**]，然後選取
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
1. 在 [條件] 底下：
   1. **用戶端應用程式 (預覽)** ：
      1. 將 [設定] 設定為 [是]。
      1. 選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。
1. 在 [**存取控制**  >  **授**與] 底下，選取 **[授與存取權**， ** ****要求已核准用戶端應用程式**]，然後選取
1. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
1. 選取 [建立] 以建立以啟用您的原則。

**步驟 3：設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則。**

如需建立適用於 Android 和 iOS 的應用程式保護原則的步驟，請參閱[如何建立和指派應用程式保護原則](/intune/apps/app-protection-policies)文章。 

## <a name="next-steps"></a>後續步驟

[何謂條件式存取？](overview.md)

[條件存取元件](concept-conditional-access-policies.md)

[一般條件式存取原則](concept-conditional-access-policy-common.md)
