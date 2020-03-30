---
title: 設定部署認證
description: 瞭解 Azure 應用服務中的部署憑據類型以及如何配置和使用它們。
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a9d875e2c3899fa91b9cc41c0ee3b5a93ec5b8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266074"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>設定 Azure App Service 的部署認證
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) 支援兩種認證類，用於[本機 Git 部署](deploy-local-git.md)和 [FTP/S 部署](deploy-ftp.md)。 這些憑據與 Azure 訂閱憑據不同。

* **使用者層級認證**︰一組用於整個 Azure 帳戶的認證。 它可以用來將任何應用程式部署至 Azure 帳戶有權存取的任何訂用帳戶的 App Service 中。 其為呈現在入口網站 GUI 中的預設集合 (例如應用程式[資源頁面](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)的 [概觀]**** 和 [屬性]****)。 當使用者通過基於角色的存取控制 （RBAC） 或共同管理員許可權授予應用存取權限時，該使用者可以使用自己的使用者級憑據，直到取消存取權限。 請勿與其他 Azure 使用者共用這些認證。

* **應用程式層級認證**︰一組用於單個 應用程式的認證。 它只可以用來部署該應用程式。 每個應用程式的認證都會在建立應用程式時自動產生。 您無法手動設定它們，但可隨時重設。 要授予使用者通過 （RBAC） 訪問應用級憑據，該使用者必須是應用上的貢獻者或更高版本（包括網站參與者內置角色）。 讀者不允許發佈且無法存取那些認證。

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>配置使用者級憑據

您可以在任何應用程式的[資源頁面](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)中設定使用者層級認證。 無論在哪一個應用程式中設定這些認證，它都會套用至所有應用程式和您的 Azure 帳戶中的所有訂用帳戶。 

### <a name="in-the-cloud-shell"></a>在雲殼中

要在[雲外殼](https://shell.azure.com)中配置部署使用者，請運行[az Webapp 部署使用者集](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set)命令。 以部署使用者的使用者名稱和和密碼來取代「username」\<\<和「password」。 

- 使用者名稱在 Azure 服務及本機 Git 推送中都必須是唯一的，且不能包含 ‘@’ 符號。 
- 密碼長度必須至少為 8 個字元，包含下列三個元素其中兩個：字母、數字及符號。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 輸出會將密碼顯示為 `null`。 如果您收到 `'Conflict'. Details: 409` 錯誤，請變更使用者名稱。 如果您收到 `'Bad Request'. Details: 400` 錯誤，請使用更強的密碼。 

### <a name="in-the-portal"></a>在門戶中

在 Azure 門戶中，必須至少有一個應用才能訪問部署憑據頁。 設定使用者層級認證：

1. 在[Azure 門戶](https://portal.azure.com)中，從左側功能表中選擇**應用服務** > **\<any_app>** > **部署中心** > **FTP** > **儀表板**。

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    或者，如果您已經配置了 Git 部署，請選擇**應用服務** > **&lt;any_app>** > **部署中心** > **FTP/憑據**。

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. 選擇 **"使用者憑據**"，配置使用者名和密碼，然後選擇 **"保存憑據**"。

設置部署憑據後，您可以在應用的 **"概述"** 頁中找到*Git*部署使用者名，

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

如果配置了 Git 部署，則頁面將顯示**Git/部署使用者名**;否則 **，FTP/部署使用者名**。

> [!NOTE]
> Azure 不會顯示您的使用者層級部署密碼。 如果您忘記密碼，可依照本節的步驟來重設認證。
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>將使用者級憑據與 FTP/FTPS 一起使用

使用使用者級憑據對 FTP/FTPS 終結點進行身份驗證需要以以下格式進行使用者名：`<app-name>\<user-name>`

由於使用者級憑據連結到使用者而不是特定資源，因此使用者名必須採用此格式才能將登錄操作定向到正確的應用終結點。

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>設定及重設應用程式層級的認證
若要取得應用程式層級的認證：

1. 在[Azure 門戶](https://portal.azure.com)中，從左側功能表中選擇**應用服務** > **&lt;any_app>** > **部署中心** > **FTP/憑據**。

2. 選擇**應用憑據**，然後選擇 **"複製**"連結以複製使用者名或密碼。

要重置應用級憑據，請在同一對話方塊中選擇 **"重置憑據**"。

## <a name="next-steps"></a>後續步驟

了解如何使用這些認證從[本機 Git](deploy-local-git.md)或使用[FTP/S](deploy-ftp.md) 部署應用程式。
