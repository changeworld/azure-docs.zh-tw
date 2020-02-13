---
title: 將行動裝置設定為雙因素驗證方法 - Azure Active Directory | Microsoft Docs
description: 了解如何將行動裝置設定為您的雙因素驗證方法。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: b0bfaa87c77ba9fff9f6605c1989e48ffbc3fb35
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062501"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>將行動裝置設定為您的雙因素驗證方法

您可以將行動裝置設定為您的雙因素驗證方法。 您的行動電話可以接收包含驗證碼的文字簡訊或接聽電話。

>[!Note]
> 如果驗證電話選項呈現灰色，很可能您的組織不允許您使用電話號碼或文字簡訊進行驗證。 在這種情況下，您將必須選取其他方法，或連絡您的系統管理員以取得更多協助。

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>將您的行動裝置設定為使用文字簡訊作為驗證方法

1. 在 [其他安全性驗證]  頁面上，選取 [驗證電話]  作為 **[步驟 1：我們應該如何聯繫您]** 區域的內容，從下拉式清單中選取您的國家或地區，然後輸入您的行動裝置電話號碼。

2. 從 [方法]  區域中選取 [透過文字簡訊傳送代碼給我]  ，然後選取 [下一步]  。

    ![包含驗證電話和文字簡訊的 [其他安全性驗證] 頁面](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. 將 Microsoft 所傳送文字簡訊中的驗證碼輸入 **[步驟 2：我們已將文字簡訊傳送到您的手機]** 區域，然後選取 [驗證]  。

    ![包含驗證電話和文字簡訊的 [其他安全性驗證] 頁面](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. 從 **[步驟 3：繼續使用現有的應用程式]** 區域中，複製所提供的應用程式密碼，並將其貼到安全的位置。

    ![其他安全性驗證頁面的應用程式密碼區域](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >如需如何搭配舊有應用程式使用應用程式密碼的詳細資訊，請參閱[管理應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)。 只有在您要繼續使用的舊有應用程式不支援雙因素驗證時，您才需要使用應用程式密碼。

5. 選取 [完成]  。

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>設定行動裝置以接聽電話

1. 在 [其他安全性驗證]  頁面上，選取 [驗證電話]  作為 **[步驟 1：我們應該如何聯繫您]** 區域的內容，從下拉式清單中選取您的國家或地區，然後輸入您的行動裝置電話號碼。

2. 在 [方法]  區域中選取 [打電話給我]  ，然後選取 [下一步]  。

    ![包含驗證電話和撥打電話的 [其他安全性驗證] 頁面](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. 您會收到來自 Microsoft 的電話，要求您按下行動裝置上的井字號 (#) 來驗證您的身分識別。

    ![測試指定的電話號碼](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. 從 **[步驟 3：繼續使用現有的應用程式]** 區域中，複製所提供的應用程式密碼，並將其貼到安全的位置。

    ![其他安全性驗證頁面的應用程式密碼區域](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >如需如何搭配舊有應用程式使用應用程式密碼的詳細資訊，請參閱[管理應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)。 只有在您要繼續使用的舊有應用程式不支援雙因素驗證時，您才需要使用應用程式密碼。

5. 選取 [完成]  。

## <a name="next-steps"></a>後續步驟

設定您的雙因素驗證方法之後，您可以新增其他方法、管理設定和應用程式密碼、登入，或取得一些常見雙因素驗證相關問題的協助。

- [管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)

- [管理應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)

- [使用雙因素驗證來登入](multi-factor-authentication-end-user-signin.md)

- [取得雙因素驗證的協助](multi-factor-authentication-end-user-troubleshoot.md)
