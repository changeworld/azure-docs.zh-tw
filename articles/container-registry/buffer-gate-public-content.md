---
title: 管理私人容器登錄中的公用內容
description: Azure Container Registry 中的實務和工作流程，可從 Docker Hub 和其他公開內容管理公用影像的相依性
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 10/29/2020
ms.openlocfilehash: def1c3a9b8a1086f453c7e71d766ab0dd89b0c2d
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347517"
---
# <a name="manage-public-content-with-azure-container-registry"></a>使用 Azure Container Registry 管理公用內容

本文概述使用本機登錄（例如 [Azure container registry](container-registry-intro.md) ）來維護公用內容複本（例如 Docker Hub 中的容器映射）的實務和工作流程。 


## <a name="risks-with-public-content"></a>公開內容的風險

您的環境可能相依于公開內容，例如公用容器映射、 [Helm 圖](https://helm.sh/)、 [開啟原則代理程式](https://www.openpolicyagent.org/) (OPA) 原則或其他成品。 例如，您可以執行 [nginx](https://hub.docker.com/_/nginx) 進行服務路由，或 `docker build FROM alpine` 直接從 Docker Hub 或其他公用登錄中提取映射。 

若沒有適當的控制項，擁有公用登錄內容的相依性可能會導致您的映射開發和部署工作流程產生風險。 若要降低風險，請盡可能保留公用內容的本機複本。 如需詳細資訊，請參閱 [Open Container 方案的 blog](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/)。 

## <a name="authenticate-with-docker-hub"></a>使用 Docker Hub 進行驗證

第一個步驟是，如果您目前從 Docker Hub 提取公用映射作為組建或部署工作流程的一部分，建議您 [使用 Docker Hub 帳戶進行驗證](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests) ，而不是發出匿名提取要求。

> [!NOTE]
> 自2020年11月2日起， [下載速率限制](https://docs.docker.com/docker-hub/download-rate-limit) 適用于從 Docker 免費方案帳戶 Docker Hub 的匿名和已驗證要求，且分別由 IP 位址和 Docker 識別碼強制執行。 
>
> 當您在評估提取要求的數目時，請考慮在使用雲端提供者服務或在公司 NAT 後方工作時，會將多個使用者呈現 Docker Hub 在匯總中，做為 IP 位址的子集。 將 Docker 付費帳戶驗證新增至對 Docker Hub 提出的要求，將可避免因速率限制節流而造成的服務中斷。
>
> 如需詳細資訊，請參閱 [docker 定價和](https://www.docker.com/pricing) 訂用帳戶和 [docker 服務條款](https://www.docker.com/legal/docker-terms-service)。

### <a name="docker-hub-access-token"></a>Docker Hub 存取權杖

Docker Hub 在向 Docker Hub 進行驗證時，支援 [個人存取權杖](https://docs.docker.com/docker-hub/access-tokens/) 作為 Docker 密碼的替代方案。 針對從 Docker Hub 提取映射的自動化服務，建議使用權杖。 您可以為不同的使用者或服務產生多個權杖，並在不再需要時撤銷權杖。

若要 `docker login` 使用權杖進行驗證，請省略命令列上的密碼。 當系統提示您輸入密碼時，請改為輸入權杖。 如果您已啟用 Docker Hub 帳戶的雙重要素驗證，則必須在從 Docker CLI 登入時使用個人存取權杖。

### <a name="authenticate-from-azure-services"></a>從 Azure 服務進行驗證

多項 Azure 服務（包括 App Service 和 Azure 容器實例）支援從公用登錄（例如容器部署的 Docker Hub）提取映射。 如果您需要從 Docker Hub 部署映射，建議您將設定設定為使用 Docker Hub 帳戶進行驗證。 範例：

**App Service**

* **映射來源** ： Docker Hub
* **儲存機制存取** ：私用
* **登** 入： \<Docker Hub username>
* **密碼** ：\<Docker Hub token>

如需詳細資訊，請參閱 [App Service Docker Hub 已驗證的提取](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html)。

**Azure 容器執行個體**

* **映射來源** ： Docker Hub 或其他登錄
* **映射類型** ：私用
* **映射登錄登入伺服器** ： docker.io
* **映射登錄使用者名稱** ： \<Docker Hub username>
* **映射登錄密碼** ： \<Docker Hub token>
* **映射** ： docker.io/ \<repo name\> ：\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>將映射匯入到 Azure container registry
 
若要開始管理公用映射的複本，您可以建立 Azure container registry （如果您還沒有的話）。 使用 [Azure CLI](container-registry-get-started-azure-cli.md)、 [Azure 入口網站](container-registry-get-started-portal.md)、 [Azure PowerShell](container-registry-get-started-powershell.md)或其他工具來建立登錄。 

作為建議的一次性步驟，請將基底映射和其他公用內容匯 [入](container-registry-import-images.md) 至您的 Azure container registry。 Azure CLI 中的 [az acr import](/cli/azure/acr#az_acr_import) 命令支援從公用登錄（例如 Docker Hub 和 Microsoft container Registry，以及來自其他私用容器登錄）進行映射匯入。 

`az acr import` 不需要本機 Docker 安裝。 您可以使用 Azure CLI 的本機安裝或直接在 Azure Cloud Shell 中執行它。 它支援任何 OS 類型、多重架構影像或 OCI 成品（例如 Helm 圖）的映射。

範例：

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

視組織的需求而定，您可以匯入至專屬的登錄或共用登錄中的存放庫。

## <a name="automate-application-image-updates"></a>自動更新應用程式映射

應用程式映射的開發人員應該確定其程式碼會參考其控制項底下的本機內容。 例如， `Docker FROM` Dockerfile 中的語句應該參考私用基底映射登錄中的映射，而不是公用登錄。 

展開映射匯入時，設定 [Azure Container Registry](container-registry-tasks-overview.md) 工作，以在基底映射更新時自動執行應用程式映射組建。 自動化組建工作可以追蹤 [基底映射更新](container-registry-tasks-base-images.md) 和 [原始程式碼更新](container-registry-tasks-overview.md#trigger-task-on-source-code-update)。

如需詳細範例，請參閱 [如何使用 Azure Container Registry 工作來取用和維護公開內容](tasks-consume-public-content.md)。 

> [!NOTE]
> 單一預先設定的工作可以自動重建參考相依基礎映射的每個應用程式映射。 
 
## <a name="next-steps"></a>後續步驟
 
* 深入瞭解在 Azure 中建立、執行、推送和修補容器映射的 [ACR 工作](container-registry-tasks-overview.md) 。
* 瞭解如何使用 Azure Container Registry 工作的自動化控制工作流程來取用 [和維護公開內容](tasks-consume-public-content.md) ，以將基礎映射更新至您的環境。 
* 如需將映射組建和更新自動化的範例，請參閱 [ACR 工作教學](container-registry-tutorial-quick-task.md) 課程。
