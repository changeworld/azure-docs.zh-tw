---
title: 設定部署認證
description: 了解 Azure App Service 中有哪些類型的部署認證，以及如何設定和使用這些認證。
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c6f7c2422e043da6df498fe81da938576687b916
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649141"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>設定 Azure App Service 的部署認證
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) 支援兩種認證類，用於[本機 Git 部署](deploy-local-git.md)和 [FTP/S 部署](deploy-ftp.md)。 這些認證與您的 Azure 訂用帳戶認證不同。

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>設定使用者層級認證

您可以在任何應用程式的[資源頁面](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)中設定使用者層級認證。 無論在哪一個應用程式中設定這些認證，它都會套用至所有應用程式和您的 Azure 帳戶中的所有訂用帳戶。 

### <a name="in-the-cloud-shell"></a>在 Cloud Shell 中

若要在 [Cloud Shell](https://shell.azure.com) 中設定部署使用者，請執行 [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 命令。 以部署使用者的使用者名稱和和密碼來取代「username」\<\<和「password」。 

- 使用者名稱在 Azure 服務及本機 Git 推送中都必須是唯一的，且不能包含 ‘@’ 符號。 
- 密碼長度必須至少為 8 個字元，包含下列三個元素其中兩個：字母、數字及符號。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 輸出會將密碼顯示為 `null`。 如果您收到 `'Conflict'. Details: 409` 錯誤，請變更使用者名稱。 如果您收到 `'Bad Request'. Details: 400` 錯誤，請使用更強的密碼。 

### <a name="in-the-portal"></a>在入口網站中

在 Azure 入口網站中，您必須至少具有一個應用程式，才能存取部署認證頁面。 設定使用者層級認證：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [應用程式服務] > [\<any_app>] > [部署中心] > [FTP] > [儀表板]。

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    或者，如果您已設定 Git 部署，請選取 [應用程式服務] > [&lt;any_app >] > [部署中心] > [FTP/認證]。

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. 選取 [使用者認證]、設定使用者名稱和密碼，然後選取 [儲存認證]。

一旦設定好您的部署認證，就可以在應用程式的 [概觀] 頁面中找到 *Git* 部署使用者名稱。

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

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

## <a name="next-steps"></a>後續步驟

了解如何使用這些認證從[本機 Git](deploy-local-git.md)或使用[FTP/S](deploy-ftp.md) 部署應用程式。
