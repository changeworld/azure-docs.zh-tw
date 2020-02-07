---
title: 管理驗證 |Microsoft Azure 對應
description: 使用 Azure 入口網站來管理 Microsoft Azure 對應中的驗證。
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 30eb637e9117818758ed4ab0e3adef9db29cc698
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057306"
---
# <a name="manage-authentication-in-azure-maps"></a>管理 Azure 地圖服務中的驗證

建立 Azure 地圖服務帳戶之後，會建立用戶端識別碼和金鑰，以支援 Azure Active Directory （Azure AD）驗證和共用金鑰驗證。

## <a name="view-authentication-details"></a>檢視驗證詳細資料

建立 Azure 地圖服務帳戶之後，就會產生主要和次要金鑰。 當您[使用共用金鑰驗證來呼叫 Azure 地圖服務](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)時，建議您使用主要金鑰做為訂用帳戶金鑰。 您可以在像是輪流金鑰變更的情況下使用次要金鑰。 如需詳細資訊，請參閱[Azure 地圖服務中的驗證](https://aka.ms/amauth)。

您可以在 Azure 入口網站中查看驗證詳細資料。 在您的帳戶中，選取 [**設定**] 功能表上的 [**驗證**]。

![驗證詳細資料](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>註冊並設定 Azure AD 應用程式

1. 在 [Azure 入口網站] 的 [Azure 服務] 清單中，選取 [ **Azure Active Directory** > **應用程式註冊** > **新增註冊**]。  

    ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

1. 如果您已註冊您的應用程式，請繼續進行下一個步驟。 如果您尚未註冊您的應用程式，請輸入**名稱**，選擇**支援帳戶類型**，然後選取 [**註冊**]。  

    ![應用程式註冊詳細資料](./media/how-to-manage-authentication/app-create.png)

1. 若要將委派的 API 許可權指派給 Azure 地圖服務，請移至應用程式。 然後在 **應用程式註冊** 底下，選取  **API 許可權** > **新增許可權**。 在 [**我的組織使用的 api**] 底下，搜尋並選取 [ **Azure 地圖服務**]。

    ![新增應用程式 API 許可權](./media/how-to-manage-authentication/app-permissions.png)

1. 選取 [**存取 Azure 地圖服務**] 旁的核取方塊，然後選取 [**新增許可權**]。

    ![選取應用程式 API 許可權](./media/how-to-manage-authentication/select-app-permissions.png)

1. 視您的驗證方法而定，完成下列其中一個步驟。 

    * 如果您的應用程式使用 Azure 地圖服務 Web SDK 的使用者權杖驗證，則請啟用 `oauth2AllowImplicitFlow`。 若要啟用它，請在應用程式註冊的 [**資訊清單**] 區段中，將 `oauth2AllowImplicitFlow` 設定為 [true]。 
    
       ![應用程式資訊清單](./media/how-to-manage-authentication/app-manifest.png)

    * 如果您的應用程式使用伺服器或應用程式驗證，請在您的應用程式註冊頁面上，移至 [憑證] [ **& 密碼**]。 然後，藉由選取 [**新增用戶端密碼**] 來上傳公開金鑰憑證或建立密碼。 
    
       ![建立用戶端密碼](./media/how-to-manage-authentication/app-keys.png)

        如果您建立密碼，則在您選取 [**新增**] 之後，請複製並安全地儲存密碼。 您將使用此密碼從 Azure AD 取得權杖。

       ![新增用戶端密碼](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>將以角色為基礎的存取控制授與 Azure 地圖服務

將 Azure 地圖服務帳戶與您的 Azure AD 租使用者建立關聯之後，您就可以授與存取控制。 您可以藉由將使用者、群組或應用程式指派給一或多個 Azure 地圖服務存取控制角色，來授與*角色型存取控制*（RBAC）。 

1. 移至您的**Azure 地圖服務帳戶**。 選取**存取控制（IAM）**  > **角色指派**。

    ![授與 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. 在 [**角色指派**] 索引標籤的 [**角色**] 底下，選取 **[Azure 地圖服務日期讀取器（預覽）** ]。 在 [存取權指派對象為] 底下，選取 [Azure AD 使用者、群組或服務主體]。 選取使用者或應用程式。 然後選取 [儲存]。

    ![新增角色指派](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>檢視可用的 Azure 地圖服務 RBAC 角色

若要查看可用於 Azure 地圖服務的 RBAC 角色，請移至 **[存取控制（IAM）** ]。 選取 [**角色**]，然後搜尋以*Azure 地圖服務*開頭的角色。 這些 Azure 地圖服務角色是您可以授與存取權的角色。

![檢視可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure 地圖服務 RBAC 的觀點

RBAC 提供更細微的存取控制。

若要查看已授與 Azure 地圖服務之 RBAC 的使用者和應用程式，請移至**存取控制（IAM）** 。 在該處選取 [**角色指派**]，然後依**Azure 地圖服務**篩選。

![查看已授與 RBAC 的使用者和應用程式](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>要求 Azure 地圖服務的權杖

註冊應用程式並將它與 Azure 地圖服務產生關聯之後，您就可以要求存取權杖。

如果您的應用程式使用 Azure 地圖服務 Web SDK 的使用者權杖驗證，請使用 Azure 地圖服務用戶端識別碼和 Azure AD 應用程式識別碼來設定您的 HTML 網頁。

如果您的應用程式使用伺服器或應用程式驗證，請從 Azure AD token 端點 `https://login.microsoftonline.com`要求權杖。 在您的要求中，使用下列詳細資料： 

* Azure AD 資源識別碼 `https://atlas.microsoft.com/`
* Azure 地圖服務用戶端識別碼
* Azure AD 應用程式識別碼
* Azure AD 應用程式註冊密碼或憑證

| Azure 環境   | Azure AD token 端點 | Azure 資源識別碼 |
| --------------------|-------------------------|-------------------|
| Azure 公用雲端        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government 雲端   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

如需從 Azure AD 針對使用者和服務主體要求存取權杖的詳細資訊，請參閱[Azure AD 的驗證案例](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[Azure AD 和 Azure 地圖服務 WEB SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。

尋找 Azure 地圖服務帳戶的 API 使用計量：
> [!div class="nextstepaction"] 
> [檢視使用計量](how-to-view-api-usage.md)

探索示範如何將 Azure AD 與 Azure 地圖服務整合的範例：

> [!div class="nextstepaction"]
> [Azure AD 驗證範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
