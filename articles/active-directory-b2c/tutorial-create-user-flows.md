---
title: 教學課程 - 建立使用者流程 - Azure Active Directory B2C
description: 了解如何在 Azure 入口網站中建立使用者流程，以在 Azure Active Directory B2C 中啟用應用程式的註冊、登入和使用者設定檔編輯功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc6673615c85b34975d6743da6da88ca841bcf35
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005356"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>教學課程：在 Azure Active Directory B2C 中建立使用者流程

在您的應用程式中，您可能會有[使用者流程](user-flow-overview.md)可讓使用者註冊、登入或管理其設定檔。 您可以在 Azure Active Directory B2C (Azure AD B2C) 租用戶中建立多個不同類型的使用者流程，並視需要在您的應用程式中使用。 使用者流程可以跨應用程式重複使用。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 建立註冊和登入使用者流程
> * 建立設定檔編輯使用者流程
> * 建立密碼重設使用者流程

本教學課程會示範如何使用 Azure 入口網站建立一些建議的使用者流程。 如果您正在尋找有關如何在應用程式中設定資源擁有者密碼認證 (ROPC) 流程的資訊，請參閱[在 Azure AD B2C 中設定資源擁有者密碼認證流程](configure-ropc.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

[註冊應用程式](tutorial-register-applications.md) (屬於您想要建立的使用者流程的一部分)。

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>建立註冊和登入使用者流程

註冊和登入使用者流程會透過單一組態來處理註冊與登入體驗。 系統會視內容而定，將您應用程式的使用者引導到正確的路徑。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。

    ![B2C 租用戶、[目錄和訂用帳戶] 窗格、Azure 入口網站](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 在 [原則] 底下，選取 [使用者流程]，然後選取 [新使用者流程]。

    ![入口網站中的 [使用者流程] 頁面，已醒目提示 [新增使用者流程] 按鈕](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. 在 [建議] 索引標籤上，選取 [註冊並登入] 使用者流程。

    ![選取已醒目提示 [註冊並登入] 流程的 [使用者流程] 頁面](./media/tutorial-create-user-flows/signup-signin-type.png)

1. 輸入使用者流程的 [名稱]。 例如，*signupsignin1*。
1. 針對 [識別提供者]，選取 [電子郵件註冊]。

    ![Azure 入口網站中的 [建立使用者流程] 頁面，已醒目提示屬性](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. 針對 [使用者屬性與宣告]，選擇在註冊期間您要收集和從使用者傳送的宣告和屬性。 例如，選取 [顯示更多]，然後選擇 [國家/地區]、[顯示名稱]，及 [郵遞區號] 的屬性和宣告。 按一下 [確定]。

    ![已選取三個宣告的 [屬性和宣告選取] 頁面](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. 按一下 [建立]  新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 選取您建立的使用者流程來開啟其 [概觀] 頁面，然後選取 [執行使用者流程]。
1. 針對 [應用程式]，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [執行使用者流程]，然後選取 [立即註冊]。

    ![入口網站中的 [執行使用者流程] 頁面，已醒目提示 [執行使用者流程] 按鈕](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. 輸入有效的電子郵件地址、按一下 [傳送驗證碼]、輸入您收到的驗證碼，然後選取 [驗證碼]。
1. 輸入新密碼並確認密碼。
1. 選取您的國家/地區和區域、輸入您想要顯示的名稱、輸入郵遞區號，然後按一下 [建立]。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。
1. 您現在可以再次執行使用者流程，且您應能夠使用您建立的帳戶登入。 傳回的權杖包含您所選國家/地區、名稱和郵遞區號的宣告。

## <a name="create-a-profile-editing-user-flow"></a>建立設定檔編輯使用者流程

如果您要在您的應用程式中允許使用者編輯其設定檔，您可使用設定檔編輯使用者流程。

1. 在 Azure AD B2C 租用戶 [概觀] 頁面的功能表中，選取 [使用者流程]，然後選取 [新增使用者流程]。
1. 在 [建議] 索引標籤上選取 [設定檔編輯] 使用者流程。
1. 輸入使用者流程的 [名稱]。 例如，*profileediting1*。
1. 針對 [識別提供者] 選取 [本機帳戶登入]。
1. 針對 [使用者屬性]，選擇您要客戶能夠在其設定檔中編輯的屬性。 例如，選取 [顯示更多]，然後選擇 [顯示名稱] 和 [職稱] 的屬性和宣告。 按一下 [確定]。
1. 按一下 [建立]  新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 選取您建立的使用者流程來開啟其 [概觀] 頁面，然後選取 [執行使用者流程]。
1. 針對 [應用程式]，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [執行使用者流程]，然後使用您先前建立的帳戶登入。
1. 現在您有機會變更使用者的顯示名稱和職稱。 按一下 **[繼續]** 。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。

## <a name="create-a-password-reset-user-flow"></a>建立密碼重設使用者流程

若要讓您應用程式的使用者能夠重設其密碼，請使用密碼重設使用者流程。

1. 在 Azure AD B2C 租用戶的 [概觀] 功能表中，選取 [使用者流程]，然後選取 [新增使用者流程]。
1. 在 [建議] 索引標籤上，選取 [密碼重設] 使用者流程。
1. 輸入使用者流程的 [名稱]。 例如，*passwordreset1*。
1. 針對 [識別提供者]啟用 [使用電子郵件地址重設密碼]。
1. 在 [應用程式宣告] 底下，按一下 [顯示更多]，並選擇您要以傳送回應用程式的授權權杖傳回的宣告。 例如，選取 [使用者的物件識別碼]。
1. 按一下 [確定]。
1. 按一下 [建立]  新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 選取您建立的使用者流程來開啟其 [概觀] 頁面，然後選取 [執行使用者流程]。
1. 針對 [應用程式]，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [執行使用者流程]、確認先前所建立帳戶的電子郵件地址，然後選取 [繼續]。
1. 您現在應該有機會變更使用者的密碼。 變更密碼，然後選取 [繼續]。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立註冊和登入使用者流程
> * 建立設定檔編輯使用者流程
> * 建立密碼重設使用者流程

接下來，請了解如何將識別提供者新增至您的應用程式，以便讓使用者可以使用 Azure AD、Amazon、Facebook、GitHub、LinkedIn、Microsoft 或 Twitter 等提供者來登入。

> [!div class="nextstepaction"]
> [將識別提供者新增至您的應用程式 >](tutorial-add-identity-providers.md)
