---
title: 使用身份身份驗證資訊登錄 - Azure AD
description: 瞭解如何在安全資訊中使用各種身份驗證方法登錄。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 7d1d27cede6c593150d3937bb3aa0ca59394eea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062161"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>使用雙步驟驗證或安全性資訊登入

設置兩步驗證或安全資訊後，您可以使用指定的驗證方法登錄您的帳戶。

> [!Note]
> 如果您仍在使用兩步驗證體驗，則需要按照"[設置我的帳戶進行兩步驗證](multi-factor-authentication-end-user-first-time.md)"文章中的說明來設置驗證方法。
>
> 如果管理員已打開安全資訊體驗，則需要使用以下分步文章設置驗證方法：<ul><li>[將安全性資訊設定為使用驗證應用程式](security-info-setup-auth-app.md)</li><li>[將安全性資訊設定為使用簡訊](security-info-setup-text-msg.md)</li><li>[將安全性資訊設定為使用電話](security-info-setup-phone-number.md)</li><li>[將安全資訊設置為使用安全金鑰](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>在行動裝置上使用驗證器應用程式通知來登入

1. 使用您的使用者名稱和密碼來登入帳戶。

2. 從傳送到您行動裝置的核准通知中選取 [核准]****。

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>在行動裝置上使用驗證器應用程式驗證碼來登入

1. 使用您的使用者名稱和密碼來登入帳戶。

2. 開啟您的驗證器應用程式，然後將針對您帳戶隨機產生的驗證碼輸入到 [輸入驗證碼]**** 方塊中。

## <a name="sign-in-using-your-phone-number"></a>使用您的電話號碼登入

1. 使用您的使用者名稱和密碼來登入帳戶。

2. 接聽來電並遵循指示。

## <a name="sign-in-using-a-text-message"></a>使用簡訊登入

1. 使用您的使用者名稱和密碼來登入帳戶。

2. 開啟簡訊，然後將簡訊中的代碼輸入到 [輸入驗證碼]**** 方塊中。

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>使用鎖屏介面的安全金鑰登錄

1. 註冊安全金鑰後，請從 Windows 10 鎖屏介面中選擇安全金鑰圖像。

2. 將安全金鑰插入設備的 USB 埠，並使用安全金鑰 PIN 登錄到 Windows。

    ![Windows 10 鎖屏介面上的安全金鑰登錄](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>使用安全金鑰和 Microsoft 邊緣瀏覽器登錄

1. 註冊安全金鑰後，打開 Microsoft 邊緣瀏覽器。

2. 當提示您登錄時，請將安全金鑰插入設備的 USB 埠，並使用安全金鑰 PIN 登錄到 Windows。

    ![使用 Microsoft 邊緣瀏覽器登錄安全金鑰](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >有關使用 Microsoft 身份驗證器應用登錄的資訊，請參閱文章，請使用[Microsoft 身份驗證器應用登錄到您的帳戶](user-help-auth-app-sign-in.md)。

## <a name="sign-in-using-another-verification-method"></a>使用另一個驗證方法登入

如果由於某種原因無法使用主登錄方法，則可以使用其他以前設置的驗證方法。

1. 正常登錄您的帳戶，然後在 **"兩步驗證**"頁上選擇 **"以另一種方式登錄"** 連結。

    ![變更登入驗證方法](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >如果您沒有看到 **"以其他方式登錄"** 連結，則意味著您尚未設置任何其他驗證方法，並且您必須聯繫管理員以獲取登錄帳戶的説明。 管理員説明您登錄後，請確保添加其他驗證方法。 如需新增驗證方法的詳細資訊，請參閱[管理雙步驟驗證設定](multi-factor-authentication-end-user-manage-settings.md)一文。
    >
    >如果您看到 **"以其他方式登錄"** 連結，但仍看不到任何其他驗證方法，則必須聯繫管理員以獲取登錄帳戶的説明。

2. 選擇您的替代驗證方法，並繼續雙步驟驗證的程序。

3. 回到您的帳戶之後，您可以更新驗證方法 (如有必要)。 如需新增或變更方法的詳細資訊，請參閱[管理雙步驟驗證設定](multi-factor-authentication-end-user-manage-settings.md)一文。

## <a name="next-steps"></a>後續步驟

- 了解[安全性資訊 (預覽) 概觀](user-help-security-info-overview.md)一文中的安全性資訊。

- 了解[雙步驟驗證概觀](user-help-two-step-verification-overview.md)一文中的雙步驟驗證。

- 如果您遺失或忘記密碼，請從[密碼重設入口網站](https://passwordreset.microsoftonline.com/)重設密碼。

- 在[無法登入 Microsoft 帳戶](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中取得登入問題的疑難排解秘訣和說明。
