---
title: 快速入門：將應用程式新增到您的 Azure Active Directory (Azure AD) 租用戶
description: 本快速入門會使用 Azure 入口網站，將資源庫應用程式新增至 Azure Active Directory (Azure AD) 租用戶。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: 2946590cbb4c5e8f495a1f6ee4aac65929cd4d0e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91305719"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>快速入門：將應用程式新增到您的 Azure Active Directory (Azure AD) 租用戶

Azure Active Directory (Azure AD) 有一個資源庫，其中包含數千個預先整合的應用程式。 您組織使用的許多應用程式可能已存在資源庫中。

將應用程式新增至 Azure AD 租用戶之後，您可以：

- 設定應用程式的屬性。
- 使用條件式存取原則來管理應用程式的使用者存取。
- 設定單一登入，讓使用者可以使用其 Azure AD 認證來登入應用程式。

## <a name="prerequisites"></a>必要條件

若要將應用程式新增至 Azure AD 租用戶，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 下列其中一個角色：全域管理員、雲端應用程式管理員、應用程式管理員或服務主體的擁有者。
- (選擇性：完成[檢視您的應用程式](view-applications-portal.md))。

>[!IMPORTANT]
>若要測試本教學課程中的步驟，我們建議您使用非實際執行環境。

## <a name="add-an-app-to-your-azure-ad-tenant"></a>將應用程式新增至您的 Azure AD 租用戶

若要將應用程式新增至 Azure AD 租用戶：

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，選取 [Azure Active Directory]。
2. 在 [Azure Active Directory] 視格中，選取 [企業應用程式]。 [所有應用程式] 窗格會隨即開啟，並顯示您 Azure AD 租用戶中應用程式的隨機樣本。
3. 在 [企業應用程式] 窗格上，選取 [新增應用程式]。 
    ![選取 [新增應用程式] 以將資源庫應用程式新增至您的租用戶](media/add-application-portal/new-application.png)
4. 切換至新的資源庫預覽體驗：在 [新增應用程式] 頁面頂端的橫幅中，選取顯示為 [按一下這裡試用改良過的新版應用程式資源庫] 的連結。
5. [瀏覽 Azure AD 資源庫 (預覽)] 窗格隨即開啟，並顯示雲端平台、內部部署應用程式和精選應用程式的圖格。 [精選應用程式] 區段中所列的應用程式，會有圖示指出其是否支援同盟單一登入 (SSO) 和佈建。 
    ![依名稱或類別搜尋應用程式](media/add-application-portal/browse-gallery.png)
6. 您可以瀏覽您要新增之應用程式的資源庫，或在搜尋方塊中輸入應用程式的名稱加以搜尋。 然後，從結果中選取應用程式。 
7. 下一步取決於應用程式開發人員實作單一登入 (SSO) 的方式。 應用程式開發人員可以採取四種方式實作單一登入。 四種方式為 SAML、OpenID Connect、密碼和 Linked。 新增應用程式時，您可以選擇只使用特定的 SSO 實作來篩選和查看應用程式，如螢幕擷取畫面所示。 例如，實作 SSO 的熱門標準稱為安全性聲明標記語言 (SAML)。 另一個熱門的標準稱為 OpenId Connect (OIDC)。 使用這些標準設定 SSO 的方式會有所不同，因此請記下所新增應用程式執行的 SSO 類型。

    :::image type="content" source="media/add-application-portal/sso-types.png" alt-text="此螢幕擷取畫面顯示 SSO 類型選取器。" lightbox="media/add-application-portal/sso-types.png":::

    - 如果應用程式的開發人員已將 **OIDC 標準**用於 SSO，請選取 [註冊]。 設定頁面隨即出現。 接下來，移至設定 OIDC 型單一登入的快速入門。
    :::image type="content" source="media/add-application-portal/sign-up-oidc-sso.png" alt-text="此螢幕擷取畫面顯示 SSO 類型選取器。":::

    - 如果應用程式的開發人員已將 **SAML 標準**用於 SSO，請選取 [建立]。 [開始使用] 頁面會隨即顯示，其中包含可為您組織設定應用程式的選項。 在表單中，您可以編輯應用程式名稱，以符合您的組織需求。 接下來，移至設定 SAML 型單一登入的快速入門。
    :::image type="content" source="media/add-application-portal/create-application.png" alt-text="此螢幕擷取畫面顯示 SSO 類型選取器。":::


> [!IMPORTANT]
> SAML 型和 OIDC 型 SSO 實作之間有一些主要的差異。 使用 SAML 型應用程式，您可以新增同一應用程式的多個執行個體。 例如，GitHub1、GitHub2 等。 若為 OIDC 型應用程式，您只能新增一個應用程式執行個體。 如果您已新增 OIDC 型應用程式，並嘗試再次新增相同的應用程式並提供兩次同意，則不會在租用戶中再次新增該應用程式。

如果您要尋找的應用程式不在資源庫中，可以選取連結**建立您自己的應用程式**，然後在**您想要對應用程式執行哪些動作？** 下選擇 [整合您在資源庫中找不到的任何其他應用程式]。 Microsoft 已經與許多應用程式開發人員合作，將他們預先設定為可使用 Azure AD。 預先設定的應用程式會顯示在資源庫中。 但如果未列出您要新增的應用程式，您可以建立新的一般應用程式，然後自行設定，或使用建立者的開發人員指導方針來設定。

您已完成新增應用程式。 下一個快速入門將示範如何為應用程式變更標誌和編輯其他屬性。

> [!TIP]
> 您可以使用圖形 API 自動管理應用程式，請參閱[使用 Microsoft Graph API 自動管理應用程式](https://docs.microsoft.com/graph/application-saml-sso-configure-api)。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續進行快速入門系列，請考慮刪除應用程式以清除您的測試租用戶。 本系列的最後一個快速入門涵蓋了刪除應用程式的說明，請參閱[刪除應用程式](delete-application-portal.md)。

## <a name="next-steps"></a>後續步驟

請前往下一篇文章，了解如何設定應用程式。
> [!div class="nextstepaction"]
> [設定應用程式](add-application-portal-configure.md)
