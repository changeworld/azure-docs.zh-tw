---
title: 更改雙因素驗證方法和設置 - Azure 活動目錄
description: 瞭解如何從"其他安全驗證"頁更改工作或學校帳戶的安全驗證方法和設置。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.openlocfilehash: 5949f04ecc28a88e340a9c2de530031793f193a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253243"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>更改雙因素驗證方法和設置

為工作或學校帳戶設置安全驗證方法後，您可以更新任何相關詳細資訊，包括：

- 預設安全驗證方法

- 安全驗證方法詳細資訊，如電話號碼

- 身份驗證器應用設置或從身份驗證器應用中刪除設備

## <a name="using-the-additional-security-verification-page"></a>使用"其他安全驗證"頁

如果您的組織提供了有關如何打開和管理雙因素驗證的具體步驟，則應首先按照這些說明操作。 否則，您可以通過["其他安全驗證](https://docs.microsoft.com/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time)"頁訪問安全驗證方法設置。

>[!Note]
>如果您在螢幕上看到的內容與本文仲介紹的內容不匹配，則意味著管理員已打開**安全資訊（預覽）** 體驗，或者您的組織提供了您自己的自訂門戶。 有關新的安全資訊體驗的詳細資訊，請參閱[安全資訊（預覽）概述](user-help-security-info-overview.md)。 有關組織自訂門戶的詳細資訊，您必須聯繫組織的説明台。

### <a name="to-get-to-the-additional-security-verification-page"></a>訪問"其他安全驗證"頁

您可以按照此連結訪問["其他安全驗證"頁](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)。

![其他安全驗證頁面，包含可用安全驗證方法詳細資訊](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

您還可以按照以下步驟訪問 **"其他安全驗證**"頁：

1. 登錄到[https://myapps.microsoft.com](https://myapps.microsoft.com)。

1. 在右上方選取您的帳戶名稱，然後選取 [設定檔]****。

1. 選擇**其他安全驗證**。  

    ![我的應用連結到"其他安全驗證"頁](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>有關使用**附加安全驗證**頁**的應用密碼**部分的資訊，請參閱[管理應用密碼進行雙重驗證](multi-factor-authentication-end-user-app-passwords.md)。 應用密碼應僅用於不支援雙重驗證的應用。

## <a name="change-your-default-security-verification-method"></a>更改預設安全驗證方法

使用使用者名和密碼登錄到工作或學校帳戶後，將自動顯示您選擇的安全驗證方法。 根據組織的要求，這可以是通過身份驗證器應用、短信或撥打電話的通知或驗證碼。

如果您決定要更改正在使用的預設安全驗證方法，可以在此處執行此操作。

### <a name="to-change-your-default-security-verification-method"></a>更改預設安全驗證方法

1. 在 **"其他安全驗證**"頁中，從 **"首選選項清單"** 中選擇要使用的方法。 您將看到所有選項，但只能選擇組織為您提供的選項。

    - **通過代理程式更新我**：您將通過您的驗證器代理程式更新您，您有等待驗證提示。

    - **撥打我的身份驗證電話**：您將在行動裝置上接到一個電話，要求您驗證您的資訊。

    - **文本代碼到我的身份驗證電話**：你會得到一個驗證碼作為短信的一部分在您的行動裝置上。 您必須在工作或學校帳戶的驗證提示中輸入此代碼。

    - **打電話給我的辦公室電話**：你會接到你的辦公室電話，要求您核實你的資訊。

    - **使用來自應用的驗證碼**：您將使用身份驗證器應用獲取驗證碼，您將從工作或學校帳戶中鍵入提示。

2. 選取 [儲存]****。

## <a name="add-or-change-your-phone-number"></a>添加或更改電話號碼

您可以在 **"其他安全驗證**"頁添加新電話號碼或更新現有號碼。

>[!Important]
>我們強烈建議您添加輔助電話號碼，以説明防止在主電話丟失或被盜或新手機丟失或被盜時被鎖定在帳戶之外，或者如果您收到新手機，並且不再有原始主電話號碼。

### <a name="to-change-your-phone-numbers"></a>更改電話號碼

1. 從"您希望**如何回應？"** **"附加安全驗證**"頁面部分中，更新**身份驗證電話**（主要行動裝置）和**Office 電話**的電話號碼資訊。

1. 選擇 **"備用身份驗證電話"** 選項旁邊的框，然後鍵入輔助電話號碼，如果無法訪問主設備，可以接收短信或撥打電話。

1. 選取 [儲存]****。

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>向 Microsoft 身份驗證器應用添加新帳戶

您可以在[適用于 Android](https://play.google.com/store/apps/details?id=com.azure.authenticator)或[iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)的 Microsoft 身份驗證器應用程式中設置您的工作或學校帳戶。

如果您已在 Microsoft 身份驗證器應用中設置了工作或學校帳戶，則無需再次執行此操作。

1. 在"**您希望如何回應"** 部分"**附加安全驗證**"頁面中，選擇 **"設置身份驗證器應用**"。

    ![在 Microsoft 身份驗證器應用中設置您的工作或學校帳戶](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. 按照螢幕上的說明操作，包括使用行動裝置掃描 QR 碼，然後選擇 **"下一步**"。

    系統將要求您通過 Microsoft 身份驗證器應用批准通知，以驗證您的資訊。

1. 選取 [儲存]****。

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>從 Microsoft 身份驗證器應用中刪除您的帳戶或設備

可以從 Microsoft 身份驗證器應用中刪除您的帳戶，也可以從工作或學校帳戶中刪除設備。 通常，您刪除設備以永久刪除帳戶中丟失、被盜或舊設備，然後刪除帳戶以嘗試修復某些連接問題或解決帳戶更改（如新使用者名）。

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>從工作或學校帳戶中刪除您的設備

1. 在"要**如何回應"** 部分"**附加安全驗證**"部分中，選擇"**設置身份驗證器應用**"按鈕。

1. 選取 [儲存]****。

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>從 Microsoft 身份驗證器應用中刪除您的帳戶

從 Microsoft 身份驗證器應用中，選擇要刪除的設備旁邊的 **"刪除**"按鈕。

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>在受信任的設備上打開雙因素驗證提示

根據組織的設置，您可能會看到一個核取方塊，該核取方塊顯示，當您在瀏覽器上執行雙重驗證時 **，不要再次詢問 X 天**。 如果您選擇了此選項來阻止雙因素驗證提示，然後您丟失了設備或您的設備可能受到威脅，則應重新打開雙因素驗證提示以説明保護您的帳戶。 您必須同時打開所有設備的提示。 遺憾的是，您無法僅為特定設備重新打開提示。

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>要為設備打開雙因素驗證提示

在[**"附加安全驗證**"頁](#to-get-to-the-additional-security-verification-page)中，選擇 **"還原以前受信任的設備上的多重要素驗證**"。 下次在任何設備上登錄時，系統將提示您執行雙重驗證。

## <a name="next-steps"></a>後續步驟

添加或更新雙因素驗證設置後，可以管理應用密碼、登錄或獲取有關某些常見的雙因素驗證相關問題的説明。

- [管理應用密碼，以便](multi-factor-authentication-end-user-app-passwords.md)對不支援雙因素驗證的任何應用進行雙重驗證。

- [如何使用雙因素驗證登錄](multi-factor-authentication-end-user-signin.md)

- [通過雙因素驗證解決常見問題](multi-factor-authentication-end-user-troubleshoot.md)
