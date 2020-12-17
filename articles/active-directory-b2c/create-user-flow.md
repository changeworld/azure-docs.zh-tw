---
title: 建立使用者流程 - Azure Active Directory B2C
description: 了解如何在 Azure 入口網站中建立使用者流程，以在 Azure Active Directory B2C 中啟用應用程式的註冊、登入和使用者設定檔編輯功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 418446e0d465b606b8d580297cebd73c466d4841
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109007"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中建立使用者流程

您可以在 Azure Active Directory B2C (Azure AD B2C) 租用戶中建立不同類型的[使用者流程](user-flow-overview.md)，並視需要在您的應用程式中使用。 使用者流程可以跨應用程式重複使用。

> [!IMPORTANT]
> 我們已變更使用者流程版本的參考方式。 以前，我們提供了 V1 (可用於生產環境) 版本，以及 V1.1 和 V2 (預覽) 版本。 現在，我們已將使用者流程合併到 **建議** (新一代預覽) 和 **標準** (正式推出) 版本。 所有 V1.1 和 V2 的舊版預覽使用者流程都即將於 **2021 年 8 月 1 日** 淘汰。 如需詳細資訊，請參閱 [Azure AD B2C 中的使用者流程版本](user-flow-versions.md)。

## <a name="before-you-begin"></a>開始之前

- **註冊** 您要用來測試新使用者流程的應用程式。 如需範例，請參閱[教學課程：在 Azure AD B2C 中註冊 Web 應用程式](tutorial-register-applications.md)。
- 如果您要讓使用者可以使用 Azure AD、Amazon、Facebook、GitHub、LinkedIn、Microsoft 或 Twitter 等提供者進行登入，請 **新增外部識別提供者**。 如需範例，請參閱[教學課程：將識別提供者新增至 Azure AD B2C 中的應用程式](tutorial-add-identity-providers.md)。
- **設定本機帳戶識別提供者**，以指定您要為租用戶中的本機帳戶支援的身分識別類型 (電子郵件、使用者名稱、電話號碼)。 然後，當您建立個別使用者流程時，即可從這些支援的身分識別類型中選擇。 使用者完成使用者流程時，會在您的 Azure AD B2C 目錄中建立本機帳戶，而您的 **本機帳戶** 識別提供者會驗證使用者的資訊。 請使用下列步驟來設定租用戶的本機帳戶識別提供者：

   1. 登入 [Azure 入口網站](https://portal.azure.com/)。 
   2. 在頂端功能表中選取 [目錄 + 訂閱] 篩選，並選擇包含您 Azure AD B2C 租用戶的目錄。
   3. 在 Azure 入口網站頂端的搜尋列中，搜尋並選取 [Azure AD B2C]。
   4. 在 [管理] 底下，選取 [識別提供者]。
   5. 在識別提供者清單中，選取 [本機帳戶]。
   6. 在 [設定本機 IDP] 頁面上，選取您要支援的所有身分識別類型。 選取此處的選項，只會使其可用於您稍後建立的使用者流程：
      - **電話** (預覽)：允許使用者輸入電話號碼，此號碼會在註冊時受到驗證，並成為使用者識別碼。
      - **電子郵件** (預設值)：允許使用者輸入電子郵件地址，此地址會在註冊時受到驗證，並成為使用者識別碼。
      - **使用者名稱**：允許使用者建立其本身的唯一使用者識別碼。 系統會向使用者收集電子郵件地址並進行驗證。
    7. 選取 [儲存]。

## <a name="create-a-user-flow"></a>建立使用者流程

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。

    ![B2C 租用戶、[目錄和訂用帳戶] 窗格、Azure 入口網站](./media/create-user-flow/directory-subscription-pane.png)

3. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
4. 在 [原則] 底下，選取 [使用者流程]，然後選取 [新使用者流程]。

    ![入口網站中的 [使用者流程] 頁面，已醒目提示 [新增使用者流程] 按鈕](./media/create-user-flow/signup-signin-user-flow.png)

5. 在 [建立使用者流程] 頁面上，選取您想要建立的使用者流程類型 (如需概觀，請參閱 [Azure AD B2C 中的使用者流程](user-flow-overview.md))。

    ![選取已醒目提示註冊和登入流程的 [使用者流程] 頁面](./media/create-user-flow/select-user-flow-type.png)

6. 在 [選取版本] 底下，選取 [建議]，然後選取 [建立]。 ([深入了解](user-flow-versions.md)使用者流程版本。)

    ![Azure 入口網站中的 [建立使用者流程] 頁面，已醒目提示屬性](./media/create-user-flow/select-version.png)

7. 輸入使用者流程的 **名稱** (例如 *signupsignin1*、*profileediting1*、*passwordreset1*)。
8. 在 [識別提供者] 底下，根據您要建立的使用者流程類型選擇適當選項：

   - **本機帳戶**。 如果您想要允許使用者在您的 Azure AD B2C 租用戶中建立本機帳戶，請選取您要讓他們使用的識別碼類型 (例如電子郵件、使用者識別碼或電話)。 只有設定於[本機帳戶識別提供者](#before-you-begin)設定中的身分識別類型，才會列出。

   - **社交識別提供者**。 如果您想要允許使用者使用[您已新增的社交識別提供者登入](tutorial-add-identity-providers.md) (例如 Azure AD、Amazon、Facebook、GitHub、LinkedIn、Microsoft 或 Twitter)，請從清單中選取提供者。

9. 針對 [使用者屬性與宣告]，選擇在註冊期間您要收集和從使用者傳送的宣告和屬性。 選取 [顯示更多]。 選取屬性和宣告，然後選取 [確定]。

    ![已選取三個宣告的 [屬性和宣告選取] 頁面](./media/create-user-flow/signup-signin-attributes.png)

10. 選取 [建立]  以新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 選取 [原則] > [使用者流程]，然後選取您所建立的使用者流程。 在使用者流程的概觀頁面上，選取 [執行使用者流程]。
1. 針對 [應用程式]，選取您在步驟 1 中註冊的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 選取 [執行使用者流程]。
2. 根據您要測試的使用者流程類型，使用有效的電子郵件地址進行註冊，並依照註冊流程操作，或使用您先前建立的帳戶登入。

    ![入口網站中的 [執行使用者流程] 頁面，已醒目提示 [執行使用者流程] 按鈕](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. 依照使用者流程提示操作。 完成使用者流程後，權杖會傳回到 `https://jwt.ms`，且應該會向您顯示。

> [!NOTE]
> 「執行使用者流程」體驗目前與使用授權碼流程的 SPA 回覆 URL 類型不相容。 若要使用「執行使用者流程」體驗搭配這幾種應用程式，請註冊 Web 類型的回覆 URL，並依照[這裡](tutorial-register-spa.md)所述的方式啟用隱含流程。

## <a name="next-steps"></a>後續步驟

- [將條件式存取新增至 Azure AD B2C 使用者流程](conditional-access-user-flow.md)
- [在 Azure AD B2C 使用者流程中自訂使用者介面](customize-ui-with-html.md)
