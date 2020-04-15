---
title: 電話號碼(預覽)的簡訊登入使用者體驗 - Azure AD
description: 瞭解有關新或現有電話號碼的 SMS 登入使用者體驗
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e5369247fc1e31bafb158e83a54bf8badf721d0d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378830"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>使用電話號碼作為使用者名稱(預覽)

註冊設備使您的手機可以訪問組織的服務,並且不允許您的組織訪問您的手機。 如果您是管理員,您可以在[「設定」中找到更多資訊,並啟用使用者進行基於 SMS 的身份驗證](../authentication/howto-authentication-sms-signin.md)。

如果您的組織尚未提供 SMS 登錄,則在帳戶中註冊手機時,您看不到該選項。  

## <a name="when-you-have-a-new-phone-number"></a>當您有新電話號碼時

如果您收到新電話或新號碼,並且將其註冊到有 SMS 登錄的組織,則您可能會遇到正常的電話註冊過程:

1. 選擇 **「添加方法**」。
1. 選擇**電話**。
1. 輸入電話號碼並選擇 **「給我發簡訊」 程式碼**。
1. 輸入代碼後,選擇 **「下一步**」。。
1. 您將看到一個提示,提示顯示"已驗證的 SMS"。 您的手機已成功註冊。

> [!Important]
> 由於預覽中出現已知問題,在短時間內添加電話號碼將不會註冊 SMS 登錄號碼。 您必須使用添加的號碼登錄,然後按照提示註冊 SMS 登錄號碼。

### <a name="when-the-phone-number-is-in-use"></a>使用電話號碼時

如果您試著使用組織中其他人正在使用的電話號碼,您將看到以下訊息:

![已使用電話號碼時發生錯誤訊息](media/sms-sign-in-explainer/sms-sign-in-error.png)

聯繫您的管理員以修復問題。

## <a name="when-you-have-an-existing-number"></a>當您有現有號碼時

如果您已經在組織中使用電話號碼,並且將電話號碼用作用戶名將變為可用,以下步驟可以説明您登錄。

1. 當 SMS 登入可用時,會顯示一個橫幅,詢問您是否要啟用 SMS 登入的電話號碼:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. 此外,如果在電話方法磁貼上選擇「**啟用**」按鈕,則會出現啟用按鈕:

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. 要啟用該方法,請選擇 **"啟用**"。 系統將提示您確定操作:

    ![確認對話框,用於為電話號碼開啟 SMS 登入](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. 選取 [啟用]****。

## <a name="when-you-remove-your-phone-number"></a>刪除電話號碼時

1. 要刪除電話號碼,請選擇 SMS 登錄電話方法磁貼上的刪除按鈕。

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. 當提示確認操作時,選擇 **「確定**」。。

不能刪除用作預設登錄方法的電話號碼。 要刪除該號碼,必須更改默認登錄方法,然後再次刪除電話號碼。
