---
title: 手動新增帳戶至應用程式 - Azure Active Directory | Microsoft Docs
description: 如何將您的帳戶手動新增至 Microsoft Authenticator 應用程式，以進行雙因素驗證。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: b237f710866ad3960dd65902c0789228d8c391bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062314"
---
# <a name="manually-add-an-account-to-the-app"></a>手動新增帳戶至應用程式

如果您的相機無法擷取 QR 代碼，您可以將您的帳戶資訊手動新增至 Microsoft Authenticator 應用程式，以進行雙因素驗證。 此適用於公司或學校帳戶和非 Microsoft 帳戶。

為您帳戶提供的代碼不區分大小寫，而且在新增至 Microsoft Authenticator 應用程式時，不需要空格。

>[!Important]
>您必須先下載並安裝 Microsoft Authenticator 應用程式，才可新增帳戶。 如果尚未這麼做，請依照[下載並安裝應用程式](user-help-auth-app-download-install.md)文章中的步驟。

## <a name="add-your-work-or-school-account"></a>新增公司或學校帳戶

1. 在您的電腦上，請記下 [設定行動應用程式]**** 頁面上的**代碼**和 **Url** 資訊。 讓此頁面保持開啟狀態，以便查看代碼和 URL。

    ![提供 QR 代碼的畫面](./media/user-help-auth-app-add-account-manual/auth-app-barcode.png)

2. 開啟 Microsoft Authenticator 應用程式、從右上角的 [自訂和控制]**** 圖示中選取 [新增帳戶]****，然後選取 [公司或學校帳戶]****。

3. 選取 [或手動輸入代碼]****。

    ![掃描 QR 代碼的畫面](./media/user-help-auth-app-add-account-manual/auth-app-manual-code.png)

4. 輸入步驟 1 中的**代碼**和 **URL**，然後選取 [完成]****。

    ![輸入代碼和 URL 的畫面](./media/user-help-auth-app-add-account-manual/auth-app-code-url.png)

    應用程式的 [帳戶]**** 畫面會顯示您的帳戶名稱和六位數的驗證碼。 為了提高安全性，驗證碼每隔 30 秒就會變更，避免多次使用同一個驗證碼。

## <a name="add-your-google-account"></a>新增您的 Google 帳戶

1. 在您的電腦上，從 [設定 Authenticator]**** 頁面選取 [無法掃描]**** 和 QR 代碼。

    [無法掃描條碼]**** 頁面隨即顯示，並包含密碼。 讓此頁面保持開啟狀態，以便查看密碼。

2. 開啟 Microsoft Authenticator 應用程式，從右上角的 [自訂和控制]**** 圖示中依序選取 [新增帳戶]****、[其他帳戶 (Google、Facebook 等等)]****，然後選取 [或手動輸入代碼]****。

3. 輸入**帳戶名稱** (例如，Google)，並輸入步驟 1 中的**祕密金鑰**，然後選取 [完成]****。

4. 在電腦上的 [設定 Authenticator]**** 頁面中，為您的 Google 帳戶輸入應用程式提供的六位數驗證碼，然後選取 [驗證]****。

    應用程式的 [帳戶]**** 畫面會顯示您的帳戶名稱和六位數的驗證碼。 為了提高安全性，驗證碼每隔 30 秒就會變更，避免多次使用同一個驗證碼。

    >[!NOTE]
    >如需更多雙因素驗證和 Google 帳戶的相關資訊，請參閱[開啟雙步驟驗證](https://support.google.com/accounts/answer/185839)和[深入了解雙步驟驗證](https://www.google.com/landing/2step/help.html)。

## <a name="add-your-facebook-account"></a>新增您的 Facebook 帳戶

1. 在 [透過第三方 Authenticator 設定]**** 頁面中，其包含 QR 代碼，以及為了進入您的應用程式而編寫的程式碼。 讓此頁面保持開啟狀態，以便查看代碼。

2. 開啟 Microsoft Authenticator 應用程式，從右上角的 [自訂和控制]**** 圖示中依序選取 [新增帳戶]****、[其他帳戶 (Google、Facebook 等等)]****，然後選取 [或手動輸入代碼]****。

3. 輸入**帳戶名稱** (例如，Facebook)，並輸入步驟 1 中的**祕密金鑰**，然後選取 [完成]****。

4. 在電腦上的 [雙因素 Authenticator]**** 頁面中，為您的 Facebook 帳戶輸入應用程式提供的六位數驗證碼，然後選取 [驗證]****。

    應用程式的 [帳戶]**** 畫面會顯示您的帳戶名稱和六位數的驗證碼。 為了提高安全性，驗證碼每隔 30 秒就會變更，避免多次使用同一個驗證碼。

    >[!NOTE]
    >如需更多雙因素驗證和您 Facebook 帳戶的更多資訊，請參閱[什麼是雙重驗證，此機制如何運作？](https://www.facebook.com/help/148233965247823)。

## <a name="add-your-amazon-account"></a>新增您的 Amazon 帳戶

您可以藉由開啟雙因素驗證，然後將帳戶新增至應用程式，以新增您的 Amazon 帳戶。

1. 在您的電腦上，從 [選擇接收代碼的方式]**** 頁面 (包含 QR 代碼) 中，選取 [無法掃描條碼]****。

    [無法掃描條碼]**** 訊息隨即顯示，並包含密碼。 讓此訊息保持開啟狀態，以便查看密碼。

2. 開啟 Microsoft Authenticator 應用程式，從右上角的 [自訂和控制]**** 圖示中依序選取 [新增帳戶]****、[其他帳戶 (Google、Facebook 等等)]****，然後選取 [或手動輸入代碼]****。

3. 輸入**帳戶名稱** (例如，Amazon)，並輸入步驟 1 中的**祕密金鑰**，然後選取 [完成]****。

4. 完成其餘註冊程序，包括新增備份驗證方法 (例如簡訊)，然後選取 [傳送代碼]****。

5. 在您電腦的 [新增備份驗證方法]**** 頁面上，為您的 Amazon 帳戶輸入備份驗證方法所提供的六位數驗證碼，然後選取 [驗證代碼並繼續]****。

6. 在 [即將完成] 頁面上，決定是否要讓您的電腦成為受信任的裝置，然後選取 [**獲得]。開啟雙步驟驗證**。

    [進階安全性設定]**** 頁面隨即出現，顯示更新的雙因素驗證詳細資料。

    >[!NOTE]
    >如需更多雙因素驗證和 Amazon 帳戶的相關資訊，請參閱[關於雙步驟驗證](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330)和[使用雙步驟驗證登入](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)。    

## <a name="next-steps"></a>後續步驟

- 將您的帳戶新增至應用程式之後，即可在您的裝置上使用 Microsoft Authenticator 應用程式登入。 如需詳細資訊，請參閱[使用應用程式登入](user-help-auth-app-sign-in.md)。

- 如果您在取得個人 Microsoft 帳戶的驗證碼時遇到問題，請參閱[Microsoft 帳戶安全性資訊 & 驗證碼](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)一文中的**疑難排解驗證程式代碼問題**一節。

- 對於執行 iOS 的裝置，您也可以將您的帳戶認證和相關應用程式設定 (例如您的帳戶順序) 備份到雲端。 如需更多資訊，請參閱[使用 Microsoft Authenticator 應用程式備份和復原](user-help-auth-app-backup-recovery.md)。
