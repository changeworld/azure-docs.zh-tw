---
title: 在 Kubernetes 上開發應用程式
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: 本快速入門說明如何使用 Azure Dev Spaces 和命令列，在 Azure Kubernetes Service 上開發應用程式
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
manager: gwallace
ms.custom: devx-track-javascript
ms.openlocfilehash: 82e7e73a91b07770881353465af063497deae085
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414797"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>快速入門：在 Kubernetes 上開發應用程式 - Azure Dev Spaces
在本指南中，您將了解如何：

- 使用 Azure 中受管理的 Kubernetes 叢集，設定 Azure Dev Spaces。
- 使用命令列在容器中開發和執行程式碼。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
- [已安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-an-azure-kubernetes-service-cluster"></a>建立 Azure Kubernetes Service 叢集

您必須在[支援的區域][supported-regions]中建立 AKS 叢集。 下列命令會建立名為 MyResourceGroup  的資源群組與名為 MyAKS  的 AKS 叢集。

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>在 AKS 叢集上啟用 Azure Dev Spaces

使用 `use-dev-spaces` 命令在 AKS 叢集上啟用 Dev Spaces，並遵循提示來進行。 下列命令會在 MyResourceGroup  群組中的 MyAKS  叢集上啟用 Dev Spaces，並建立「預設」  開發空間。

> [!NOTE]
> `use-dev-spaces` 命令也會安裝 Azure Dev Spaces CLI (如果尚未安裝)。 您無法在 Azure Cloud Shell 中安裝 Azure Dev Spaces CLI。

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>取得應用程式範例的程式碼

在本文中，您會使用 [Azure Dev Spaces 應用程式範例](https://github.com/Azure/dev-spaces)來示範如何使用 Azure Dev Spaces。

請複製 GitHub 中的應用程式，然後瀏覽至 dev-spaces/samples/nodejs/getting-started/webfrontend  目錄：

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>準備應用程式

若要在 Azure Dev Spaces 上執行您的應用程式，您需要 Dockerfile 和 Helm 圖表。 對於某些語言，例如 [Java][java-quickstart]、[.NET core][netcore-quickstart] 和 [Node.js][nodejs-quickstart]，Azure Dev Spaces 用戶端工具可以產生您需要的所有資產。 對於其他許多語言 (例如 Go、PHP 和 Python)，只要您可以提供有效的 Dockerfile，用戶端工具就可以產生 Helm 圖表。

使用 `azds prep` 命令，產生用於在 Kubernetes 中執行應用程式的 Docker 和 Helm 圖表資產：

```cmd
azds prep --enable-ingress
```

您必須從 dev-spaces/samples/nodejs/getting-started/webfrontend  目錄執行 `prep` 命令，以正確產生 Docker 和 Helm 圖表資產。

> [!TIP]
> `prep` 命令會嘗試為您的專案產生 [Dockerfile 和 Helm 圖表](how-dev-spaces-works-prep.md#prepare-your-code)。 Azure Dev Spaces 會使用這些檔案來建置和執行您的程式碼，但如果您想要變更專案的建置和執行方式，可以修改這些檔案。

## <a name="build-and-run-code-in-kubernetes"></a>在 Kubernetes 中建置及執行程式碼

使用 `azds up` 命令在 AKS 中建置和執行程式碼：

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at `http://webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

您可以藉由開啟公用 URL (顯示於 `azds up` 命令所產生的輸出中)，來查看服務的執行。 在此範例中，公用 URL 為 `http://webfrontend.1234567890abcdef1234.eus.azds.io/`  。

> [!NOTE]
> 當您在執行 `azds up` 同時瀏覽至您的服務時，HTTP 要求追蹤也會顯示在 `azds up` 命令的輸出中。 這些追蹤可協助您針對服務進行疑難排解及偵錯。 當執行 `azds up` 時，您可以使用 `--disable-http-traces` 來停用這些追蹤。

如果您使用 Ctrl+c  停止 `azds up` 命令，則服務會繼續在 AKS 中執行，且公用 URL 會維持可供使用的狀態。

## <a name="update-code"></a>更新程式碼

若要部署更新過的服務版本，您可以在專案中更新任何檔案，然後重新執行 `azds up` 命令。 例如：

1. 如果 `azds up` 仍在執行，請按 Ctrl+c  。
1. 將 [`server.js` 中的第 13 行](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) \(英文\) 更新為：
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. 儲存您的變更。
1. 重新執行 `azds up` 命令：

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. 瀏覽至執行中的服務，然後觀察您的變更。
1. 按 Ctrl+c  來停止 `azds up` 命令。

## <a name="clean-up-your-azure-resources"></a>清除 Azure 資源

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [在 Azure Dev Spaces 中進行小組開發][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service