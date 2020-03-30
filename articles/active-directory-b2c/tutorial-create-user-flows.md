---
title: 教程 - 創建使用者流 - Azure 活動目錄 B2C
description: 瞭解如何在 Azure 門戶中創建使用者流，以便在 Azure 活動目錄 B2C 中為應用程式啟用註冊、登錄和使用者設定檔編輯。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51adbb74635f66ca86347b536dc2607566dcb725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264241"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>教程：在 Azure 活動目錄 B2C 中創建使用者流

在應用程式中，您可能具有允許使用者註冊、登錄或管理其設定檔[的使用者流](user-flow-overview.md)。 您可以在 Azure 活動目錄 B2C （Azure AD B2C） 租戶中創建多個不同類型的使用者流，並根據需要在應用程式中使用它們。 使用者流程可以跨應用程式重複使用。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 建立註冊和登入使用者流程
> * 建立設定檔編輯使用者流程
> * 建立密碼重設使用者流程

本教學課程會示範如何使用 Azure 入口網站建立一些建議的使用者流程。 如果要查找有關如何在應用程式中設置資源擁有者密碼憑據 （ROPC） 流的資訊，請參閱[在 Azure AD B2C 中配置資源擁有者密碼憑據流](configure-ropc.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

[註冊應用程式](tutorial-register-applications.md) (屬於您想要建立的使用者流程的一部分)。

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>建立註冊和登入使用者流程

註冊和登入使用者流程會透過單一組態來處理註冊與登入體驗。 系統會視內容而定，將您應用程式的使用者引導到正確的路徑。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在門戶工具列中選擇**目錄 + 訂閱**圖示，然後選擇包含 Azure AD B2C 租戶的目錄。

    ![B2C 租戶、目錄和訂閱窗格、Azure 門戶](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. 在 Azure 門戶中，搜索並選擇**Azure AD B2C**。
1. 在 **"策略**"下，選擇**使用者流（策略），** 然後選擇 **"新使用者流**"。

    ![突出顯示了"新使用者流"按鈕的門戶中的使用者流頁](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. 在"**推薦"** 選項卡上，選擇"**註冊並登錄**使用者流"。

    ![選擇具有"註冊"並突出顯示的登錄流的使用者流頁](./media/tutorial-create-user-flows/signup-signin-type.png)

1. 輸入使用者流**的名稱**。 例如，*signupsignin1*。
1. 針對 [識別提供者]****，選取 [電子郵件註冊]****。

    ![在 Azure 門戶中創建具有突出顯示內容的使用者流頁](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. 針對 [使用者屬性與宣告]****，選擇在註冊期間您要收集和從使用者傳送的宣告和屬性。 例如，選擇 **"顯示更多**"，然後選擇**國家/區域**、**顯示名稱**和**郵遞區號**的屬性和聲明。 按一下 [確定]****。

    ![選擇三個聲明的屬性和聲明選擇頁](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. 按一下 [建立] **** 新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 選擇您為打開其概覽頁而創建的使用者流，然後選擇 **"運行使用者流**"。
1. 針對 [應用程式]****，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [執行使用者流程]****，然後選取 [立即註冊]****。

    ![在門戶中運行使用者流頁，並突出顯示運行使用者流按鈕](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. 輸入有效的電子郵件地址，按一下 **"發送驗證碼**"，輸入您收到的驗證碼，然後選擇 **"驗證碼**"。
1. 輸入新密碼並確認密碼。
1. 選擇您的國家/地區和區域，輸入要顯示的名稱，輸入郵遞區號，然後按一下"**創建**"。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。
1. 您現在可以再次執行使用者流程，且您應能夠使用您建立的帳戶登入。 返回的權杖包括您選擇的國家/地區、名稱和郵遞區號的聲明。

## <a name="create-a-profile-editing-user-flow"></a>建立設定檔編輯使用者流程

如果您要在您的應用程式中允許使用者編輯其設定檔，您可使用設定檔編輯使用者流程。

1. 在 Azure AD B2C 租戶概述頁的功能表中，選擇**使用者流（策略），** 然後選擇 **"新建使用者流**"。
1. 在"**推薦"** 選項卡上選擇 **"設定檔編輯**使用者流"。
1. 輸入使用者流**的名稱**。 例如，*profileediting1*。
1. 針對 [識別提供者]**** 選取 [本機帳戶登入]****。
1. 針對 [使用者屬性]****，選擇您要客戶能夠在其設定檔中編輯的屬性。 例如，選擇 **"顯示更多**"，然後為 **"顯示名稱**"和 **"作業標題**"同時選擇屬性和聲明。 按一下 [確定]****。
1. 按一下 [建立] **** 新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 選擇您為打開其概覽頁而創建的使用者流，然後選擇 **"運行使用者流**"。
1. 針對 [應用程式]****，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [執行使用者流程]****，然後使用您先前建立的帳戶登入。
1. 現在您有機會變更使用者的顯示名稱和職稱。 按一下 **[繼續]**。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。

## <a name="create-a-password-reset-user-flow"></a>建立密碼重設使用者流程

要使應用程式的使用者能夠重置其密碼，請使用密碼重設使用者流。

1. 在 Azure AD B2C 租戶概覽功能表中，選擇**使用者流（策略），** 然後選擇 **"新建使用者流**"。
1. 在"**推薦"** 選項卡上選擇**密碼重設**使用者流。
1. 輸入使用者流**的名稱**。 例如，*passwordreset1*。
1. 針對 [識別提供者]**** 啟用 [使用電子郵件地址重設密碼]****。
1. 在 [應用程式宣告] 底下，按一下 [顯示更多]****，並選擇您要以傳送回應用程式的授權權杖傳回的宣告。 例如，選取 [使用者的物件識別碼]****。
1. 按一下 [確定]****。
1. 按一下 [建立] **** 新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 選擇您為打開其概覽頁而創建的使用者流，然後選擇 **"運行使用者流**"。
1. 針對 [應用程式]****，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 **"運行使用者流**"，驗證您以前創建的帳戶的電子郵件地址，然後選擇"**繼續**"。
1. 您現在應該有機會變更使用者的密碼。 更改密碼並選擇"**繼續**"。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立註冊和登入使用者流程
> * 建立設定檔編輯使用者流程
> * 建立密碼重設使用者流程

接下來，瞭解如何向應用程式添加標識提供程式，以便與 Azure AD、亞馬遜、Facebook、GitHub、LinkedIn、微軟或 Twitter 等供應商進行使用者登錄。

> [!div class="nextstepaction"]
> [將標識提供程式添加到應用程式>](tutorial-add-identity-providers.md)