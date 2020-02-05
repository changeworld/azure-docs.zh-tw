---
title: 管理驗證 |Microsoft Azure 對應
description: 您可以使用 Azure 入口網站來管理 Microsoft Azure 對應中的驗證。
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: f856aebe5e3acaca142e460d18ec8c6498b18787
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989175"
---
# <a name="manage-authentication-in-azure-maps"></a>管理 Azure 地圖服務中的驗證

建立 Azure 地圖服務帳戶之後，會建立用戶端識別碼和金鑰，以支援 Azure Active Directory （Azure AD）和共用金鑰驗證。

## <a name="view-authentication-details"></a>檢視驗證詳細資料

建立 Azure 地圖服務帳戶之後，就會產生主要和次要金鑰。 當使用[共用金鑰驗證](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)呼叫 Azure 地圖服務時，建議您使用主要金鑰做為訂用帳戶金鑰。 次要金鑰可用於輪流金鑰變更之類的案例中。 若要深入瞭解，請參閱[使用 Azure 地圖服務進行驗證](https://aka.ms/amauth)。

您可以在 Azure 入口網站上查看驗證詳細資料。 移至您的帳戶，然後選取 [**設定**] 功能表上的 [**驗證**]。

![驗證詳細資料](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="configure-azure-ad-app-registration"></a>設定 Azure AD 應用程式註冊

1. 從 Azure 入口網站的 Azure 服務清單中選取 [ **Azure Active Directory** ]。  選取**應用程式註冊**，然後按一下 [**新增註冊**]。  接著， 輸入**名稱**，選擇 [**支援帳戶類型**]，然後按一下 [**註冊**]。  如果您已經有已註冊的應用程式，請繼續進行步驟2。 

    ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

    ![應用程式註冊詳細資料](./media/how-to-manage-authentication/app-create.png)

2. 若要將委派的 API 許可權指派給 Azure 地圖服務，請移至**應用程式註冊**之下的應用程式。 接著，選取 [ **API 許可權**]，然後選取 [**新增許可權**]。 搜尋並選取 [我的**組織使用的 api**] 底下的**Azure 地圖服務**。

    ![應用程式 API 許可權](./media/how-to-manage-authentication/app-permissions.png)

3. 檢查 [**存取 Azure 地圖服務**]，然後按一下 [**新增許可權**]。

    ![選取應用程式 API 許可權](./media/how-to-manage-authentication/select-app-permissions.png)

4. 完成步驟 a 或 b，視您的驗證方法而定。 

    1. 如果您的應用程式使用 Azure 地圖服務 Web SDK 的使用者權杖驗證，請啟用 `oauth2AllowImplicitFlow`。 若要啟用 `oauth2AllowImplicitFlow`，請在應用程式註冊的 [資訊清單] 區段中將它設定為 true。 
    
       ![應用程式資訊清單](./media/how-to-manage-authentication/app-manifest.png)

    2. 如果您的應用程式使用伺服器/應用程式驗證，請移至應用程式註冊頁面中的 [**憑證 & 秘密**] 分頁，然後按一下 [**新增用戶端密碼**] 或 [將公開金鑰憑證上傳至應用程式註冊] 來建立密碼。 如果您建立密碼，請在按一下 [**新增**] 之後複製密碼以供稍後使用，並加以安全地儲存。 您將使用此密碼從 Azure AD 取得權杖。

       ![應用程式金鑰](./media/how-to-manage-authentication/app-keys.png)

       ![新增金鑰](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>將角色型存取控制（RBAC）授與 Azure 地圖服務

將 Azure 地圖服務帳戶與您的 Azure AD 租使用者建立關聯之後，您就可以授與存取控制。 您可以將使用者、群組或應用程式指派給一或多個 Azure 地圖服務存取控制角色，以授與存取控制。

1. 移至您的**Azure 地圖服務帳戶**。 選取 **[存取控制（IAM）** ]，然後選取 [**角色指派**]。

    ![授與 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 [**角色指派**] 視窗的 [**角色**] 底下，選取 **[Azure 地圖服務日期讀取器（預覽）** ]。 在 [**指派存取**權] 底下 **，選取 Azure AD 的使用者、群組或服務主體**。 選取使用者或應用程式。 選取 [儲存]。

    ![新增角色指派](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>檢視可用的 Azure 地圖服務 RBAC 角色

若要查看可用於 Azure 地圖服務的角色型存取控制（RBAC）角色，請移至 **[存取控制（IAM）** ]，選取 [**角色**]，然後搜尋以**Azure 地圖服務**開頭的角色。 這些角色是您可以授與存取權的角色。

![檢視可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure 地圖服務 RBAC 的觀點

RBAC 提供更細微的存取控制。

若要查看已為 Azure 地圖服務授與 RBAC 的使用者和應用程式，請移至 **[存取控制（IAM）** ]，選取 [**角色指派**]，然後依**Azure 地圖服務**進行篩選。

![查看已授與 RBAC 的使用者和應用程式](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>要求 Azure 地圖服務的權杖

註冊應用程式並將它與 Azure 地圖服務相關聯之後，您就可以要求存取權杖。

* 如果您的應用程式使用 Azure 地圖服務 Web SDK 的使用者權杖驗證，您必須使用 Azure 地圖服務用戶端識別碼和 Azure AD 應用程式識別碼來設定您的 HTML 網頁。

* 如果您的應用程式使用伺服器/應用程式驗證，您需要向 Azure AD token 端點 `https://login.microsoftonline.com` 要求權杖，其 Azure AD 資源識別碼 `https://atlas.microsoft.com/`、Azure 地圖服務用戶端識別碼、Azure AD 應用程式識別碼，以及 Azure AD 應用程式註冊密碼或憑證。

| Azure 環境   | Azure AD token 端點 | Azure 資源識別碼 |
| --------------------|-------------------------|-------------------|
| Azure 公用        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure 政府機構    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

如需從 Azure AD 要求存取權杖的詳細資訊，請參閱使用者和服務主體[Azure AD 的驗證案例](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。


## <a name="next-steps"></a>後續步驟

若要深入了解 Azure AD 驗證和 Azure 地圖服務 Web SDK，請參閱 [Azure AD 和 Azure 地圖服務 Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。

了解如何查看 Azure 地圖服務帳戶的 API 使用計量：
> [!div class="nextstepaction"] 
> [檢視使用計量](how-to-view-api-usage.md)

如需顯示如何整合 Azure Active Directory （AAD）與 Azure 地圖服務的範例清單，請參閱：

> [!div class="nextstepaction"]
> [Azure AD 驗證範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
