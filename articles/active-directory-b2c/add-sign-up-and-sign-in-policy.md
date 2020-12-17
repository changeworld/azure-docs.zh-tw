---
title: 設定註冊和登入流程
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure Active Directory B2C 中設定註冊和登入流程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 29dd67e9e6e15aaafec0cc47d89da32cbf369938
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618715"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定註冊和登入流程

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>註冊和登入流程

註冊和登入原則可讓使用者： 

* 使用本機帳戶註冊
* 使用本機帳戶登入
* 使用社交帳戶註冊或登入
* 密碼重設

![設定檔編輯流程](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>Prerequisites

如果您尚未這麼做，請 [在 Azure Active Directory B2C 中註冊 web 應用程式](tutorial-register-applications.md)。

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>建立註冊和登入使用者流程

註冊和登入使用者流程會透過單一組態來處理註冊與登入體驗。 系統會視內容而定，將您應用程式的使用者引導到正確的路徑。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 在 [原則] 底下，選取 [使用者流程]，然後選取 [新使用者流程]。

    ![入口網站中的 [使用者流程] 頁面，已醒目提示 [新增使用者流程] 按鈕](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. 在 [建立使用者流程] 頁面上，選取 [註冊和登入] 使用者流程。

    ![選取已醒目提示 [註冊並登入] 流程的 [使用者流程] 頁面](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. 在 [選取版本] 底下，選取 [建議]，然後選取 [建立]。 ([深入了解](user-flow-versions.md)使用者流程版本。)

    ![Azure 入口網站中的 [建立使用者流程] 頁面，已醒目提示屬性](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. 輸入使用者流程的 [名稱]。 例如，*signupsignin1*。
1. 針對身分 **識別提供者**，選取 [ **電子郵件註冊**]。
1. 針對 [使用者屬性與宣告]，選擇在註冊期間您要收集和從使用者傳送的宣告和屬性。 例如，選取 [顯示更多]，然後選擇 [國家/地區]、[顯示名稱]，及 [郵遞區號] 的屬性和宣告。 按一下 [確定]。

    ![已選取三個宣告的 [屬性和宣告選取] 頁面](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. 按一下 [建立]  新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 選取您建立的使用者流程來開啟其 [概觀] 頁面，然後選取 [執行使用者流程]。
1. 針對 [應用程式]，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [執行使用者流程]，然後選取 [立即註冊]。

    ![入口網站中的 [執行使用者流程] 頁面，已醒目提示 [執行使用者流程] 按鈕](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.PNG)

1. 輸入有效的電子郵件地址、按一下 [傳送驗證碼]、輸入您收到的驗證碼，然後選取 [驗證碼]。
1. 輸入新密碼並確認密碼。
1. 選取您的國家/地區和區域、輸入您想要顯示的名稱、輸入郵遞區號，然後按一下 [建立]。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。
1. 您現在可以再次執行使用者流程，且您應能夠使用您建立的帳戶登入。 傳回的權杖包含您所選國家/地區、名稱和郵遞區號的宣告。

> [!NOTE]
> 「執行使用者流程」體驗目前與使用授權碼流程的 SPA 回覆 URL 類型不相容。 若要使用「執行使用者流程」體驗搭配這幾種應用程式，請註冊 Web 類型的回覆 URL，並依照[這裡](tutorial-register-spa.md)所述的方式啟用隱含流程。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>建立註冊與登入原則

自訂原則是一組您上傳至 Azure AD B2C 租使用者以定義使用者旅程的 XML 檔案。 我們提供的入門套件包含數個預先建立的原則，包括：註冊和登入、密碼重設和設定檔編輯原則。 如需詳細資訊，請參閱 [Azure AD B2C 中的開始使用自訂原則](custom-policy-get-started.md)。

::: zone-end

## <a name="next-steps"></a>後續步驟

* 新增 [具有社交識別提供者](add-identity-provider.md)的登入。
* 設定 [密碼重設流程](add-password-reset-policy.md)。
