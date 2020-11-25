---
title: 使用 Helm 在 Azure Kubernetes Service (AKS) 上進行開發
description: 使用 Helm 搭配 AKS 和 Azure Container Registry，在叢集中封裝和執行應用程式容器。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 0ca2d7ccc863e2208db1212ef3d3f10fa709d069
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006639"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>快速入門：使用 Helm 在 Azure Kubernetes Service (AKS) 上進行開發

[Helm][helm] 是開放原始碼的封裝工具，可協助您安裝和管理 Kubernetes 應用程式的生命週期。 如同 Linux 套件管理員（例如 *APT* 和 *Yum*），Helm 可用來管理 Kubernetes 圖表，也就是預先設定的 Kubernetes 資源套件。

本文說明如何使用 Helm 在 AKS 上封裝和執行應用程式。 如需使用 Helm 安裝現有應用程式的詳細資訊，請參閱 [在 AKS 中使用 Helm 安裝現有的應用][helm-existing]程式。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* [已安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [已安裝 Helm v3][helm-install]。

## <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry
若要使用 Helm 在 AKS 叢集中執行您的應用程式，您需要 Azure Container Registry 來儲存您的容器映射。 下列範例會使用 [az acr create][az-acr-create] ，以 *基本* SKU 在 *MyResourceGroup* 資源群組中建立名為 *MyHelmACR* 的 acr。 您應該提供自己的唯一登錄名稱。 登錄名稱在 Azure 內必須是唯一的，且包含 5-50 個英數字元。 *基本* SKU 對開發用途而言是最符合成本效益的進入點，可在儲存體和輸送量之間取得平衡。

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

輸出類似於下列範例： 記下您 ACR 的 *loginServer* 值，因為它將在稍後的步驟中使用。 在下列範例中， *myhelmacr.azurecr.io* 是 *myhelmacr* 的 *loginServer* 。

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="create-an-azure-kubernetes-service-cluster"></a>建立 Azure Kubernetes Service 叢集

建立 AKS 叢集。 下列命令會建立名為 MyAKS 的 AKS 叢集，並附加 MyHelmACR。

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

您的 AKS 叢集需要存取您的 ACR，才能提取容器映射並加以執行。 上述命令也會將 *MyAKS* 叢集存取權授與您的 *MyHelmACR* ACR。

## <a name="connect-to-your-aks-cluster"></a>連接到您的 AKS 叢集

若要從本機電腦連線至 Kubernetes 叢集，您應使用 [kubectl][kubectl] (Kubernetes 命令列用戶端)。

如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 您也可以使用 [az aks install-cli][] 命令將其安裝於本機：

```azurecli
az aks install-cli
```

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][] 命令。 下列範例會取得 *MyResourceGroup* 中名為 *MyAKS* 之 AKS 叢集的認證：

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>下載範例應用程式

本快速入門會使用 [Azure Dev Spaces 範例存放庫中 Node.js 應用程式範例][example-nodejs]。 從 GitHub 複製應用程式，並流覽至該 `dev-spaces/samples/nodejs/getting-started/webfrontend` 目錄。

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>建立 Dockerfile

使用下列內容建立新的 *Dockerfile* 檔案：

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>建立範例應用程式並將其推送至 ACR

使用 [az acr build][az-acr-build] 命令來建立映射，並使用上述 Dockerfile 將其推送至登錄。 命令結尾處的 `.` 會設定 Dockerfile 的位置，在此案例中為目前的目錄。

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>建立您的 Helm 圖

使用命令產生您的 Helm 圖 `helm create` 。

```console
helm create webfrontend
```

對 *webfrontend/values. yaml* 進行下列更新。 以您在先前步驟中記下的登錄 loginServer 取代，例如 *myhelmacr.azurecr.io*：

* 將 `image.repository` 變更為 `<loginServer>/webfrontend`
* 將 `service.type` 變更為 `LoadBalancer`

例如：

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: *myhelmacr.azurecr.io*/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

`appVersion` `v1` 在 *webfrontend/Chart. yaml* 中更新至。 例如：

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>執行您的 Helm 圖表

使用 `helm install` 命令來安裝您的應用程式，並使用您的 Helm 圖。

```console
helm install webfrontend webfrontend/
```

需要幾分鐘的時間，服務才能傳回公用 IP 位址。 若要監視進度，請使用 `kubectl get service` 命令搭配 watch 參數：

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

使用瀏覽器流覽至應用程式的負載平衡器， `<EXTERNAL-IP>` 以查看範例應用程式。

## <a name="delete-the-cluster"></a>選取叢集

當不再需要叢集時，請使用 [az group delete][az-group-delete] 命令來移除資源群組、AKS 叢集、容器登錄、儲存在該處的容器映射，以及所有相關資源。

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱 [AKS 服務主體的考量和刪除][sp-delete]。 如果您使用受控識別，則身分識別會由平台負責管理，您不需要刪除。

## <a name="next-steps"></a>後續步驟

如需有關使用 Helm 的詳細資訊，請參閱 Helm 檔。

> [!div class="nextstepaction"]
> [Helm 文件][helm-documentation]

[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
