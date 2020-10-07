---
title: 快速入門：將使用者指派給使用 Azure Active Directory 作為識別提供者的應用程式
description: 本快速入門將逐步解說如何讓使用者使用以 Azure AD 作為識別提供者的應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: kenwith
ms.openlocfilehash: 889ad3b74a2f7c0baa006a0c45e3f14664d6cd0c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325873"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>快速入門：將使用者指派給以 Azure AD 作為識別提供者的應用程式

在先前的快速入門中，您已設定應用程式的屬性。 設定屬性時，您已為已指派和未指派的使用者設定了體驗。 本快速入門會逐步解說將使用者指派給應用程式的程序。

## <a name="prerequisites"></a>先決條件

若要將使用者指派給已新增至 Azure AD 租用戶的應用程式，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 下列其中一個角色：全域管理員、雲端應用程式管理員、應用程式管理員或服務主體的擁有者。
- 選擇性：完成[檢視您的應用程式](view-applications-portal.md)。
- 選擇性：完成[新增應用程式](add-application-portal.md)。
- 選擇性：完成[設定應用程式](add-application-portal-configure.md)。

>[!IMPORTANT]
>請使用非商業執行環境來測試本快速入門中的步驟。

## <a name="assign-users-to-an-app"></a>將使用者指派至應用程式
1. 在 Azure AD 入口網站中，選取 [企業應用程式]。 然後尋找並選取您要設定的應用程式。
2. 在左側導覽功能表中，選取 [使用者和群組]。
   > [!NOTE]
   > 某些 Microsoft 365 應用程式需要使用 PowerShell。 
3. 選取 [新增使用者] 按鈕。
4. 在 [新增指派] 頁面上，選取 [使用者和群組]。
5. 選取您要指派給應用程式的使用者或群組。 您也可以在搜尋方塊中，開始輸入使用者或群組的名稱。 您可以選擇多個使用者和群組，而您的選取項目將會出現在 [選取的項目] 之下。
    > [!IMPORTANT]
    > 當您將群組指派給應用程式時，只有群組中的使用者才有存取權。 指派不會串聯成巢狀群組。

    > [!NOTE]
    > 以群組為基礎的指派需要 Azure Active Directory Premium P1 或 P2 版本。 以群組為基礎的指派僅支援安全性群組。 目前不支援巢狀群組成員資格和 Microsoft 365 群組。 如需本文所討論功能的詳細授權需求，請參閱 [Azure Active Directory 定價頁面](https://azure.microsoft.com/pricing/details/active-directory)。 
6. 完成時，選擇 [選取]。
   ![將使用者或群組指派給應用程式](./media/assign-user-or-group-access-portal/assign-users.png)
7. 在 [使用者和群組] 窗格上，從清單中選取一或多個使用者或群組，然後選擇窗格底部的 [選取] 按鈕。
8. 如果應用程式提供支援，您可將角色指派給使用者或群組。 在 [新增指派] 窗格上，選擇 [選取角色]。 然後，在 [選取角色] 窗格上，選擇要套用到所選使用者或群組的角色，然後選取窗格底部的 [確定]。 
    > [!NOTE]
    > 如果應用程式不支援角色選取，則會指派預設存取角色。 在此情況下，應用程式會管理使用者所擁有的存取層級。
9. 在 [新增指派] 窗格上，選取窗格底部的 [指派] 按鈕。

您可以使用相同的程序來取消指派使用者或群組。 選取您要取消指派的使用者或群組，然後選取 [移除]。 某些 Microsoft 365 和 Office 365 應用程式需要使用 PowerShell。 

## <a name="clean-up-resources"></a>清除資源

完成本快速入門之後，請考慮刪除應用程式。 如此一來，您就可以讓測試租用戶保持乾淨。 本系列的最後一個快速入門涵蓋了刪除應用程式的說明，請參閱[刪除應用程式](delete-application-portal.md)。

## <a name="next-steps"></a>後續步驟

前往下一篇文章，了解如何設定應用程式單一登入。
> [!div class="nextstepaction"]
> [設定 SAML 型單一登入](add-application-portal-setup-sso.md)

或者

> [!div class="nextstepaction"]
> [設定 OIDC 型單一登入](add-application-portal-setup-oidc-sso.md)
