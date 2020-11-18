---
title: 快速入門：針對您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定屬性
description: 本快速入門會使用 Azure 入口網站來設定已向您 Azure Active Directory (Azure AD) 租用戶註冊的應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: 18e40d0e62a69954b0e3c1e5770a71c8b0bcba8b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656678"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>快速入門：針對您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定屬性

在先前的快速入門中，您已將應用程式新增至 Azure Active Directory (Azure AD) 租用戶。 當您新增應用程式時，您就會讓 Azure AD 租用戶知道這是應用程式的識別提供者。 現在您將為應用程式設定一些屬性。
 
## <a name="prerequisites"></a>必要條件

若要在您的 Azure AD 租用戶中設定應用程式的屬性，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 下列其中一個角色：全域管理員、雲端應用程式管理員、應用程式管理員或服務主體的擁有者。
- 選擇性：完成[檢視您的應用程式](view-applications-portal.md)。
- 選擇性：完成[新增應用程式](add-application-portal.md)。

>[!IMPORTANT]
>請使用非商業執行環境來測試本快速入門中的步驟。

## <a name="configure-app-properties"></a>設定應用程式屬性

在您將應用程式新增至 Azure AD 租用戶之後，概觀頁面就會出現。 如果您要設定已新增的應用程式，請查看第一個快速入門。 其中會逐步引導您檢視已新增至您租用戶的應用程式。 

若要編輯應用程式屬性：

1. 在 Azure AD 入口網站中，選取 [企業應用程式]。 然後尋找並選取您要設定的應用程式。
2. 在 [管理] 區段中，選取 [屬性] 來開啟 [屬性] 窗格以進行編輯。
3. 請花點時間了解可用的選項。 可用的選項將取決於應用程式與 Azure AD 整合的方式。 例如，使用 SAML 型單一登入的應用程式將具有 [使用者存取 URL] 之類的欄位，而使用 OIDC 型單一登入的應用程式則沒有這類欄位。 另請注意，透過 Azure Active Directory > [應用程式註冊] 新增的應用程式預設為 OIDC 型應用程式。 然而透過 Azure Active Directory > [企業應用程式] 新增的應用程式可以使用任何一種單一登入標準。 所有應用程式都有欄位可供設定應用程式出現並可使用的時機。 這些欄位包括：
    - [為使用者啟用登入？] 可決定指派給應用程式的使用者是否可以登入。
    - [需要使用者指派？] 可決定未指派給應用程式的使用者是否可以登入。
    - [可讓使用者看見？] 可決定指派給應用程式的使用者是否可在[我的應用程式](https://myapps.microsoft.com)和 Microsoft 365 應用程式啟動器中看見應用程式。 (請參閱 Microsoft 365 網站左上角的鬆餅功能表。)
    
    > [!TIP]
    > 指派使用者會在導覽的 [使用者和群組] 區段中進行。

    這三個選項可彼此獨立切換，而產生的行為不一定顯而易見。 以下資料表可能會有幫助：
    
    | 為使用者啟用登入？ | 需要使用者指派？ | 可讓使用者看見？ | 使用者已指派給應用程式與否的行為。 |
    |---|---|---|---|
    | 是 | 是 | 是 | 已指派的使用者可以看到應用程式並登入。<br>未指派的使用者看不到應用程式，且無法登入。 |
    | 是 | 是 | 否  | 已指派的使用者看不到應用程式，但可以登入。<br>未指派的使用者看不到應用程式，且無法登入。 |
    | 是 | 否  | 是 | 已指派的使用者可以看到應用程式並登入。<br>未指派的使用者看不到應用程式，但可以登入。 |
    | 是 | 否  | 否  | 已指派的使用者看不到應用程式，但可以登入。<br>未指派的使用者看不到應用程式，但可以登入。 |
    | 否  | 是 | 是 | 已指派的使用者看不到應用程式，且無法登入。<br>未指派的使用者看不到應用程式，且無法登入。 |
    | 否  | 是 | 否  | 已指派的使用者看不到應用程式，且無法登入。<br>未指派的使用者看不到應用程式，且無法登入。 |
    | 否  | 否  | 是 | 已指派的使用者看不到應用程式，且無法登入。<br>未指派的使用者看不到應用程式，且無法登入。 |
    | 否  | 否  | 否  | 已指派的使用者看不到應用程式，且無法登入。<br>未指派的使用者看不到應用程式，且無法登入。 |

4. 完成之後，選取 [儲存]。

## <a name="use-a-custom-logo"></a>使用自訂標誌

使用自訂標誌：

1. 建立 215 x 215 像素的標誌，並將其儲存為 .png 格式。
2. 在 Azure AD 入口網站中，選取 [企業應用程式]。 然後尋找並選取您要設定的應用程式。
3. 在 [管理] 區段中，選取 [屬性] 來開啟 [屬性] 窗格以進行編輯。 
4. 選取圖示以上傳標誌。
5. 完成之後，選取 [儲存]。

    ![[屬性] 畫面的螢幕擷取畫面，其中會說明如何變更標誌。](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > 這個 [屬性] 窗格上顯示的縮圖不會立即更新。 您可以關閉 [屬性] 窗格再重新加以開啟，以查看更新的圖示。


> [!TIP]
> 您可以使用圖形 API 自動管理應用程式，請參閱[使用 Microsoft Graph API 自動管理應用程式](/graph/application-saml-sso-configure-api)。

## <a name="add-notes"></a>新增附註

您可以使用附註欄位，新增任何與 Azure AD 中的應用程式管理有關的資訊。 附註是一個任意文字欄位，大小上限為 1024 個字元。

1. 在 Azure AD 入口網站中，選取 [企業應用程式]。 然後尋找並選取您要設定的應用程式。
2. 在 [管理] 區段中，選取 [屬性] 來開啟 [屬性] 窗格以進行編輯。
3. 更新 [附註] 欄位，然後選取 [儲存]。

    ![[屬性] 畫面的螢幕擷取畫面，其中說明如何變更附註](media/add-application-portal/notes-application.png)

    
## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續進行快速入門系列，請考慮刪除應用程式以清除您的測試租用戶。 本系列的最後一個快速入門涵蓋了刪除應用程式的說明，請參閱[刪除應用程式](delete-application-portal.md)。

## <a name="next-steps"></a>後續步驟

前往下一篇文章，了解如何將使用者指派至應用程式。
> [!div class="nextstepaction"]
> [將使用者指派至應用程式](add-application-portal-assign-users.md)