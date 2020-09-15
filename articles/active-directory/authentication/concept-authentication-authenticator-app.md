---
title: Microsoft Authenticator 應用程式驗證方法-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 中使用 Microsoft Authenticator 應用程式來協助改善和保護登入事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79870bcfc3b2cacb856141841a1f018eb1c50641
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532618"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Azure Active Directory Microsoft Authenticator 應用程式中的驗證方法

Microsoft Authenticator 的應用程式可為您的 Azure AD 工作或學校帳戶或您的 Microsoft 帳戶提供額外的安全性層級，且適用于 [Android](https://go.microsoft.com/fwlink/?linkid=866594)、 [iOS](https://go.microsoft.com/fwlink/?linkid=866594)和 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。 使用 Microsoft Authenticator 應用程式，使用者可以在登入期間以無密碼方式進行驗證，或在自助式密碼重設 (SSPR) 或 Azure 多重要素驗證事件期間作為額外的驗證選項。

使用者可透過行動應用程式收到通知，以供進行核准或拒絕，或使用 Authenticator 應用程式來產生可在登入介面中輸入的 OATH 驗證碼。 如果您同時啟用通知和驗證碼，則註冊 Authenticator 應用程式的使用者可使用任一種方法來驗證其身分識別。

若要在登入提示字元中使用 Authenticator 應用程式，而不是使用者名稱和密碼組合，請參閱[使用 Microsoft Authenticator 應用程式 (預覽) 啟用無密碼登入](howto-authentication-passwordless-phone.md)。

> [!NOTE]
> 使用者在啟用 SSPR 時，沒有註冊其行動應用程式的選項。 相反地，使用者可以在 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)，或在 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 的合併安全性資訊註冊中，註冊其行動應用程式。

## <a name="passwordless-sign-in"></a>無密碼登入

在輸入使用者名稱之後，從 Microsoft Authenticator 應用程式啟用手機登入的使用者會看到一則訊息，以在其應用程式中顯示數位，而不會在輸入使用者名稱時看到提示輸入密碼。 選取正確的數位時，登入程式就會完成。

![要求使用者核准登入的瀏覽器登入範例](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

此驗證方法可提供高層級的安全性，而不需要使用者在登入時提供密碼。 使用 Microsoft Authenticator 應用程式的無密碼登入目前為預覽狀態。

若要開始使用無密碼登入，請參閱 [使用 Microsoft Authenticator 應用程式啟用無密碼登入](howto-authentication-passwordless-phone.md)。

## <a name="notification-through-mobile-app"></a>行動應用程式的通知

Authenticator 應用程式可協助防止未經授權即存取帳戶，並藉由推播通知到您的手機或平板電腦來停止詐騙交易。 使用者需檢視通知，如果合法，則選取 [驗證]。 否則，他們可以選取 [拒絕]。

![範例網頁瀏覽器提示的螢幕擷取畫面，其中顯示用以完成登入程序的 Authenticator 應用程式通知](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> 如果您的組織有員工任職或前往中國，則在 Android 裝置上透過行動裝置 *應用程式* 方法的通知，在該國家/地區中無法使用 Google play services (包括在區域中封鎖) 的推播通知。 不過，iOS 通知會正常運作。 針對 Android 裝置，應讓這些使用者使用替代驗證方法。

## <a name="verification-code-from-mobile-app"></a>行動應用程式傳回的驗證碼

Authenticator 應用程式可以作為軟體權杖來產生 OATH 驗證碼。 輸入您的使用者名稱和密碼之後，請在登入介面中輸入 Authenticator 應用程式所提供的驗證碼。 驗證碼提供第二種形式的驗證。

使用者可設定最多 5 個 OATH 硬體權杖或驗證器應用程式 (例如 Microsoft Authenticator 應用程式) 的組合，以在任何時間點使用。

> [!WARNING]
> 當重設只需要一種方法時，若要確保自助式密碼重設的最高層級安全性，驗證程式碼是使用者唯一可用的選項。
>
> 需要兩種方法時，除了任何其他已啟用的方法之外，使用者將可使用通知或驗證碼進行重設。

## <a name="next-steps"></a>後續步驟

若要開始使用無密碼登入，請參閱 [使用 Microsoft Authenticator 應用程式啟用無密碼登入](howto-authentication-passwordless-phone.md)。

深入瞭解如何使用 [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)來設定驗證方法。
