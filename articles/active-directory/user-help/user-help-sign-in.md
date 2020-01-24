---
title: 使用您的身分識別驗證資訊登入-Azure AD
description: 深入瞭解如何使用安全性資訊中的各種身分識別驗證方法進行登入。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.custom: user-help
ms.openlocfilehash: c7dea5acfce9ad964b050b6787f29e5d116a306b
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704590"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>使用雙步驟驗證或安全性資訊登入

設定雙步驟驗證或安全性資訊之後，您將能夠使用您指定的驗證方法來登入帳戶。

> [!Note]
> 如果您仍在使用雙步驟驗證體驗，則必須遵循[針對雙步驟驗證設定我的帳戶](multi-factor-authentication-end-user-first-time.md)一文中的指示來設定您的驗證方法。
>
> 如果您的系統管理員已開啟安全性資訊體驗，您將需要使用下列逐步文章來設定您的驗證方法：<ul><li>[將安全性資訊設定為使用驗證應用程式](security-info-setup-auth-app.md)</li><li>[將安全性資訊設定為使用簡訊](security-info-setup-text-msg.md)</li><li>[將安全性資訊設定為使用電話](security-info-setup-phone-number.md)</li><li>[將安全性資訊設定為使用安全性金鑰](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>在行動裝置上使用驗證器應用程式通知來登入

1. 使用您的使用者名稱和密碼來登入帳戶。

2. 從傳送到您行動裝置的核准通知中選取 [核准]。

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>在行動裝置上使用驗證器應用程式驗證碼來登入

1. 使用您的使用者名稱和密碼來登入帳戶。

2. 開啟您的驗證器應用程式，然後將針對您帳戶隨機產生的驗證碼輸入到 [輸入驗證碼] 方塊中。

## <a name="sign-in-using-your-phone-number"></a>使用您的電話號碼登入

1. 使用您的使用者名稱和密碼來登入帳戶。

2. 接聽來電並遵循指示。

## <a name="sign-in-using-a-text-message"></a>使用簡訊登入

1. 使用您的使用者名稱和密碼來登入帳戶。

2. 開啟簡訊，然後將簡訊中的代碼輸入到 [輸入驗證碼] 方塊中。

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>在鎖定畫面上使用安全性金鑰登入

1. 在您註冊安全性金鑰之後，請從 Windows 10 鎖定畫面中選取安全性金鑰映射。

2. 將您的安全性金鑰插入裝置的 USB 埠，並使用您的安全性金鑰 PIN 來登入 Windows。

    ![Windows 10 鎖定畫面上的安全性金鑰登入](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>使用安全性金鑰和 Microsoft Edge 瀏覽器登入

1. 在您註冊安全性金鑰之後，請開啟 Microsoft Edge 瀏覽器。

2. 當系統提示您登入時，請將您的安全性金鑰插入裝置的 USB 埠，並使用您的安全性金鑰 PIN 碼登入 Windows。

    ![使用 Microsoft Edge 瀏覽器的安全性金鑰登入](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >如需使用 Microsoft Authenticator 應用程式登入的詳細資訊，請參閱[使用 Microsoft Authenticator 應用程式登入您的帳戶](user-help-auth-app-sign-in.md)一文。

## <a name="sign-in-using-another-verification-method"></a>使用另一個驗證方法登入

如果基於某些原因而無法使用您的主要登入方法，您可以使用另一個先前設定的驗證方法。

1. 正常登入您的帳戶，然後在**雙步驟驗證**頁面上選擇 [**以另一種方式登入**] 連結。

    ![變更登入驗證方法](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >如果您沒有看到 [**以另一種方式登入**] 連結，表示您尚未設定任何其他驗證方法，而且您必須聯絡系統管理員，以協助登入您的帳戶。 當您的系統管理員協助您登入之後，請確定您已新增額外的驗證方法。 如需新增驗證方法的詳細資訊，請參閱[管理雙步驟驗證設定](multi-factor-authentication-end-user-manage-settings.md)一文。
    >
    >如果您看到 [**以另一種方式登入**] 連結，但仍看不到任何其他驗證方法，則必須聯絡系統管理員，以協助登入您的帳戶。

2. 選擇您的替代驗證方法，並繼續雙步驟驗證的程序。

3. 回到您的帳戶之後，您可以更新驗證方法 (如有必要)。 如需新增或變更方法的詳細資訊，請參閱[管理雙步驟驗證設定](multi-factor-authentication-end-user-manage-settings.md)一文。

## <a name="next-steps"></a>後續步驟

- 了解[安全性資訊 (預覽) 概觀](user-help-security-info-overview.md)一文中的安全性資訊。

- 了解[雙步驟驗證概觀](user-help-two-step-verification-overview.md)一文中的雙步驟驗證。

- 如果您遺失或忘記密碼，請從[密碼重設入口網站](https://passwordreset.microsoftonline.com/)重設密碼。

- 在[無法登入 Microsoft 帳戶](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中取得登入問題的疑難排解秘訣和說明。
