---
title: 電話號碼的 SMS 登入使用者體驗 (預覽) - Azure AD
description: 深入了解新的或現有電話號碼的 SMS 登入使用者體驗
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: a4dcb6774990c03ce3d55fb6c519b0600a1086f9
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536880"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>使用您的電話號碼做為使用者名稱 (預覽)

註冊裝置可讓您的手機存取貴組織的服務，而不允許貴組織存取您的手機。 如果您是系統管理員，可以在[設定及啟用 SMS 型驗證的使用者](../authentication/howto-authentication-sms-signin.md)中找到詳細資訊。

如果貴組織尚未提供 SMS 登入，則在向您的帳戶註冊手機時，將不會看到此選項。  

## <a name="when-you-have-a-new-phone-number"></a>當您有新的電話號碼時

如果您取得新手機或新號碼，且向可使用 SMS 登入的組織進行註冊，您會遇到一般的手機註冊程序：

1. 選取 [新增方法]。
1. 選取 [手機]。
1. 輸入電話號碼，然後選取 [以簡訊傳送代碼給我]。
1. 輸入代碼之後，請選取 [下一步]。
1. 您會看到提示指出「SMS 已驗證。 已成功註冊您的手機。」

> [!Important]
> 由於預覽版中的已知問題，在短時間內，新增電話號碼不會註冊 SMS 登入的號碼。 您必須使用已新增的號碼登入，然後遵循提示來註冊 SMS 登入的號碼。

### <a name="when-the-phone-number-is-in-use"></a>當電話號碼有人使用時

如果您嘗試使用貴組織中其他人正在使用的電話號碼，會看到下列訊息：

![當您的電話號碼已有人使用時出現的錯誤訊息](media/sms-sign-in-explainer/sms-sign-in-error.png)

請與您的系統管理員聯繫，以補救問題。

## <a name="when-you-have-an-existing-number"></a>當您有現有的號碼時

如果您已在組織中使用電話號碼，並使用您的電話號碼做為使用者名稱，則下列步驟可協助您登入。

1. 當 SMS 登入可供使用時，會顯示橫幅，詢問您是否要啟用 SMS 登入的電話號碼：

    :::image type="content" source="media/sms-sign-in-explainer/sms-sign-in-banner.png" alt-text="顯示旗標的螢幕擷取畫面，可針對已選取 [啟用] 動作的電話號碼啟用 SMS 登入。" lightbox="media/sms-sign-in-explainer/sms-sign-in-banner.png":::

1. 此外，如果您在 [電話方法] 磚上選取插入號，就會出現 [啟用] 按鈕：

    [![用來讓 SMS 登入電話號碼的橫幅。](media/sms-sign-in-explainer/sms-sign-in-phone-method.png)](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. 若要啟用方法，請選取 [啟用]。 系統會提示您確認此動作：

    ![用來啟用電話號碼的 SMS 登入確認對話方塊](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. 選取 [啟用]。

## <a name="when-you-remove-your-phone-number"></a>當您移除電話號碼時

1. 若要刪除電話號碼，請選取 [SMS 登入電話方法] 磚上的 [刪除] 按鈕。

    [![橫幅可刪除電話號碼的 SMS 登入。](media/sms-sign-in-explainer/sms-sign-in-delete-method.png)](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. 出現確認動作的提示時，請選取 [確定]。

您無法移除使用中做為預設登入方法的電話號碼。 若要移除號碼，您必須變更預設的登入方法，然後再次移除電話號碼。
