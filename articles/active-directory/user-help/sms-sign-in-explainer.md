---
title: 電話號碼的 SMS 登入使用者體驗（預覽）-Azure AD
description: 深入瞭解新的或現有電話號碼的 SMS 登入使用者體驗
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378830"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>使用您的電話號碼作為使用者名稱（預覽）

註冊裝置可讓您的電話存取貴組織的服務，而不允許您的組織存取您的手機。 如果您是系統管理員，您可以在[針對 SMS 型驗證設定和啟用使用者](../authentication/howto-authentication-sms-signin.md)中找到詳細資訊。

如果您的組織未提供 SMS 登入，在向您的帳戶註冊電話時，您將不會看到此選項。  

## <a name="when-you-have-a-new-phone-number"></a>當您有新的電話號碼時

如果您收到新的電話或新號碼，並向可使用 SMS 登入的組織註冊它，您會遇到一般的電話註冊程式：

1. 選取 [**新增方法**]。
1. 選取 [**電話**]。
1. 輸入電話號碼，然後選取 [文字] [**我的程式碼**]。
1. 輸入程式碼之後，請選取 **[下一步]**。
1. 您會看到提示指出「SMS 已驗證。 已成功註冊您的電話。」

> [!Important]
> 由於預覽版中的已知問題，在短時間內，新增電話號碼不會註冊 SMS 登入的號碼。 您必須使用已新增的號碼登入，然後遵循提示來註冊 SMS 登入的號碼。

### <a name="when-the-phone-number-is-in-use"></a>當使用中的電話號碼時

如果您嘗試使用組織中其他人正在使用的電話號碼，您會看到下列訊息：

![當您的電話號碼已用完時，出現錯誤訊息](media/sms-sign-in-explainer/sms-sign-in-error.png)

請與您的系統管理員聯繫，以補救問題。

## <a name="when-you-have-an-existing-number"></a>當您有現有的數位時

如果您已在組織中使用電話號碼，並使用您的電話號碼作為使用者名稱，則下列步驟可協助您登入。

1. 當 SMS 登入可供使用時，會顯示橫幅，詢問您是否要啟用 SMS 登入的電話號碼：

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. 此外，如果您在 [電話方法] 磚上選取插入號，則會出現 [**啟用**] 按鈕：

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. 若要啟用方法，請選取 [**啟用**]。 系統會提示您確認此動作：

    ![用來啟用 SMS 登入電話號碼的確認對話方塊](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. 選取 [啟用]****。

## <a name="when-you-remove-your-phone-number"></a>當您移除電話號碼時

1. 若要刪除電話號碼，請選取 [SMS 登入電話方法] 磚上的 [刪除] 按鈕。

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. 當系統提示您確認動作時，請選取 **[確定]**。

您無法移除做為預設登入方法使用的電話號碼。 若要移除數位，您必須變更預設的登入方法，然後再次移除電話號碼。
