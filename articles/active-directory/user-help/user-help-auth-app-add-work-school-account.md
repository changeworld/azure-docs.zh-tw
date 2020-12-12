---
title: 新增公司或學校帳戶至 Microsoft Authenticator 應用程式 - Azure AD
description: 將公司和學校帳戶新增至 Microsoft Authenticator 應用程式，以便使用雙因素驗證來驗證身分識別。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04c9bc429d9663f7ac36b6ba8f40abf225eb71c6
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359097"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>新增公司或學校帳戶至 Microsoft Authenticator 應用程式

如果您的組織使用雙因素驗證，您可以設定公司或學校帳戶，使其使用 Microsoft Authenticator 應用程式作為其中一種驗證方法。

>[!Important]
>您必須先下載並安裝 Microsoft Authenticator 應用程式，才可新增帳戶。 如果尚未這麼做，請依照[下載並安裝應用程式](user-help-auth-app-download-install.md)文章中的步驟。

## <a name="add-your-work-or-school-account"></a>新增公司或學校帳戶

您可以執行下列其中一項動作，以將您的工作或學校帳戶新增至 Microsoft Authenticator 應用程式：

- 使用您的工作或學校帳戶登入認證 (預覽) 
- 掃描 QR 代碼

### <a name="sign-in-with-your-credentials"></a>使用您的認證登入

>[!Note]
>這項功能僅供系統管理員使用驗證器應用程式來啟用手機登入的使用者使用。

使用您的認證登入您的公司或學校帳戶，以新增帳戶：

1. 開啟 Microsoft Authenticator 應用程式，並選取 **+** 按鈕，然後按一下 [ **新增工作或學校帳戶**]。 選取 [登入]。

1. 輸入您的工作或學校帳號憑證。 如果您有暫時存取權 (請按一下) 可以用來登入。 至此，您可能會因為下列其中一個狀況而無法繼續進行：

   - 如果您的帳戶沒有足夠的驗證方法可取得增強式驗證權杖，您將無法繼續新增帳戶。

   - 如果您收到訊息 `You might be signing in from a location that is restricted by your admin` ，系統會封鎖您，且需要系統管理員在 [安全性資訊](https://mysignins.microsoft.com/security-info)中將您解除封鎖。

   - 如果您的系統管理員未封鎖使用驗證器應用程式進行手機登入，您將能夠完成裝置註冊，以設定無密碼手機登入和 Azure Multi-Factor Authentication (MFA) 。

1. 此時，系統可能會要求您掃描組織所提供的 QR 代碼，以在應用程式中設定內部部署的多重要素驗證帳戶。 只有當您的組織使用內部部署 MFA Server 時，才需要這麼做。

1. 在您的裝置上，按一下該帳戶，並在全螢幕視圖中確認您的帳戶正確，而且有相關聯的六位數驗證碼。 為了提高安全性，驗證碼每隔 30 秒就會變更，避免多次使用同一個驗證碼。

## <a name="sign-in-with-a-qr-code"></a>使用 QR 代碼登入

若要透過掃描 QR 代碼來新增帳戶，請執行下列動作：

1. 在您的電腦上，移至 **其他安全性驗證** 頁面。

   >[!Note]
   >如果您未看到 [其他安全性驗證] 頁面，有可能是您的系統管理員已開啟安全性資訊 (預覽) 體驗。 若是如此，您即應依照[將安全性資訊設定為使用驗證器應用程式](security-info-setup-auth-app.md)一節中的指示操作。 若非如此，您必須連絡組織的技術支援人員以尋求協助。 如需安全性資訊的詳細資訊，請參閱 [從登入提示字元設定您的安全性資訊](security-info-setup-signin.md)。

1. 選取 [驗證器應用程式] 旁的核取方塊，然後選取 [ **設定**]。 [設定行動應用程式] 頁面隨即出現。

   ![提供 QR 代碼的畫面](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. 開啟 Microsoft Authenticator 應用程式，選取加號圖示 ![ 選取 iOS 或 Android 裝置上的加號圖示，然後選取 [ ](media/user-help-auth-app-add-work-school-account/plus-icon.png) **新增帳戶**]，然後選取 [ **公司或學校帳戶]，** 接著 **掃描 QR 代碼**。
   如果您未在驗證器應用程式中設定帳戶，您會看到一個大型的藍色按鈕，指出 [ **新增帳戶**]。

如果系統未提示您使用相機來掃描 QR 代碼，請在手機的設定中，確定驗證器應用程式可以存取電話攝影機。

## <a name="next-steps"></a>後續步驟

- 將您的帳戶新增至應用程式之後，您即可在裝置上使用 Authenticator 應用程式登入。 如需詳細資訊，請參閱[使用應用程式登入](user-help-auth-app-sign-in.md)。

- 對於執行 iOS 的裝置，您也可以將您的帳戶認證和相關應用程式設定 (例如您的帳戶順序) 備份到雲端。 如需更多資訊，請參閱[使用 Microsoft Authenticator 應用程式備份和復原](user-help-auth-app-backup-recovery.md)。
