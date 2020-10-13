---
title: 設定部署認證
description: 了解 Azure App Service 中有哪些類型的部署認證，以及如何設定和使用這些認證。
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 50b3cae00110a64e4d95171822bf1d2a282d2cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715407"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>設定 Azure App Service 的部署認證
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) 支援兩種認證類，用於[本機 Git 部署](deploy-local-git.md)和 [FTP/S 部署](deploy-ftp.md)。 這些認證與您的 Azure 訂用帳戶認證不同。

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>設定使用者層級認證

您可以在任何應用程式的[資源頁面](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)中設定使用者層級認證。 無論在哪一個應用程式中設定這些認證，它都會套用至所有應用程式和您的 Azure 帳戶中的所有訂用帳戶。 

### <a name="in-the-cloud-shell"></a>在 Cloud Shell 中

若要在 [Cloud Shell](https://shell.azure.com) 中設定部署使用者，請執行 [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 命令。 以部署使用者的使用者名稱和密碼取代 \<username> 和 \<password>。 

- 使用者名稱在 Azure 服務及本機 Git 推送中都必須是唯一的，且不能包含 ‘@’ 符號。 
- 密碼長度必須至少為 8 個字元，包含下列三個元素其中兩個：字母、數字及符號。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 輸出會將密碼顯示為 `null`。 如果您收到 `'Conflict'. Details: 409` 錯誤，請變更使用者名稱。 如果您收到 `'Bad Request'. Details: 400` 錯誤，請使用更強的密碼。 

### <a name="in-the-portal"></a>在入口網站中

在 Azure 入口網站中，您必須至少具有一個應用程式，才能存取部署認證頁面。 設定使用者層級認證：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取左側功能表中的 [**應用程式服務**  >  **\<any_app>**  >  **部署中心**  >  **FTP**  >  **儀表板**]。

    ![顯示如何在 Azure App Services 的部署中心選取 FTP 儀表板。](./media/app-service-deployment-credentials/access-no-git.png)

    或者，如果您已設定 Git 部署，請選取 [應用程式服務] > [&lt;any_app >] > [部署中心] > [FTP/認證]。

    ![示範如何從部署中心為您所設定的 Git 部署 Azure App 服務中選取 FTP 儀表板。](./media/app-service-deployment-credentials/access-with-git.png)

2. 選取 [使用者認證]、設定使用者名稱和密碼，然後選取 [儲存認證]。

一旦設定好您的部署認證，就可以在應用程式的 [概觀] 頁面中找到 *Git* 部署使用者名稱。

![示範如何在您的應用程式總覽頁面上尋找 Git 部署使用者名稱。](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

如果已設定 Git 部署，則頁面會顯示 [Git/部署使用者名稱]；否則，顯示 [FTP/部署使用者名稱]。

> [!NOTE]
> Azure 不會顯示您的使用者層級部署密碼。 如果您忘記密碼，可依照本節的步驟來重設認證。
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>搭配 FTP/FTPS 使用使用者層級認證

使用使用者層級認證向 FTP/FTPS 端點進行驗證時，需要下列格式的使用者名稱：`<app-name>\<user-name>`

由於使用者層級認證會連結至使用者，而不是特定資源，因此使用者名稱必須採用此格式，才能將登入動作導向至正確的應用程式端點。

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>設定及重設應用程式層級的認證
若要取得應用程式層級的認證：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [應用程式服務] > [&lt;any_app>] > [部署中心] > [FTP/認證]。

2. 選取 [應用程式認證]，然後選取 [複製] 連結來複製使用者名稱或密碼。

若要重設應用程式層級認證，請選取同一個對話方塊中的 [重設認證]。

## <a name="disable-basic-authentication"></a>停用基本驗證

某些組織必須符合安全性需求，而會停用透過 FTP 或 WebDeploy 的存取。 如此一來，組織的成員只能透過 Azure Active Directory (Azure AD) 所控制的 Api 來存取其應用程式服務。

### <a name="ftp"></a>FTP

若要停用對網站的 FTP 存取，請執行下列 CLI 命令。 將預留位置取代為您的資源群組和網站名稱。 

```bash
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

若要確認 FTP 存取遭到封鎖，您可以嘗試使用 FTP 用戶端（例如 FileZilla）進行驗證。 若要取出發行認證，請移至網站的總覽分頁，然後按一下 [下載發行設定檔]。 使用檔案的 FTP 主機名稱、使用者名稱和密碼來進行驗證，您會收到401錯誤回應，指出您未獲授權。

### <a name="webdeploy-and-scm"></a>WebDeploy 和 SCM

若要停用 WebDeploy 埠和 SCM 網站的基本驗證存取，請執行下列 CLI 命令。 將預留位置取代為您的資源群組和網站名稱。 

```bash
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

若要確認 WebDeploy 上的發行設定檔認證遭到封鎖，請嘗試 [使用 Visual Studio 2019 發佈 web 應用程式](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)。

### <a name="disable-access-to-the-api"></a>停用對 API 的存取

上一節中的 API 是 azure RBAC)  (azure 角色型存取控制，這表示您可以 [建立自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles#steps-to-create-a-custom-role) ，並將較低 priveldged 的使用者指派給角色，讓他們無法在任何網站上啟用基本驗證。 若要設定自訂角色，請 [遵循下列指示](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)。

您也可以使用 [Azure 監視器](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) 來審核任何成功的驗證要求，並使用 [Azure 原則](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) 對訂用帳戶中的所有網站強制執行此設定。

## <a name="next-steps"></a>後續步驟

了解如何使用這些認證從[本機 Git](deploy-local-git.md)或使用[FTP/S](deploy-ftp.md) 部署應用程式。
