---
title: 使用 Helm 在 Azure 庫伯奈斯服務 (AKS) 上開發
description: 將 Helm 與 AKS 和 Azure 容器註冊表一起使用,以打包和運行群集中的應用程式容器。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 77627ab846999ea5ba42fde7a9c49b9cc7559fba
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873429"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>快速入門:使用 Helm 開發 Azure 庫伯奈斯服務 (AKS)

[Helm][helm] 是開放原始碼的封裝工具，可協助您安裝和管理 Kubernetes 應用程式的生命週期。 Helm 類似於 *APT* 和 *Yum* 等 Linux 套件管理工具，可用於管理 Kubernetes 圖表 (即預先設定的 Kubernetes 資源套件)。

本文介紹如何使用 Helm 在 AKS 上打包和運行應用程式。 有關使用 Helm 安裝現有應用程式的詳細資訊,請參閱[在 AKS 中使用 Helm 安裝現有應用程式][helm-existing]。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果沒有 Azure 訂閱,則可以建立[免費帳戶](https://azure.microsoft.com/free)。
* [已安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 已安裝並設定 Docker。 Docker 提供可在 [Mac][docker-for-mac]、[Windows][docker-for-windows] 或 [Linux][docker-for-linux] 系統上設定 Docker 的套件。
* [已安裝 Helm v3][helm-install]。

## <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry
要使用 Helm 在 AKS 群集中運行應用程式,需要 Azure 容器註冊表來儲存容器映射。 下面的範例使用[az acr 創建][az-acr-create]在*MyResourceGroup*資源組中使用*基本*SKU 創建名為*MyHelmACR*的 ACR。 您應該提供您自己的唯一註冊表項名稱。 登錄名稱在 Azure 內必須是唯一的，且包含 5-50 個英數字元。 *基本* SKU 對開發用途而言是最符合成本效益的進入點，可在儲存體和輸送量之間取得平衡。

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

輸出類似於下列範例： 記下 ACR 的*登入伺服器*值,因為它將在後面的步驟中使用。 在下面的範例中 *,myhelmacr.azurecr.io*是*MyHelmACR*的*登入伺服器*。

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

要使用 ACR 實例,必須首先登錄。 使用[az acr 登錄命令][az-acr-login]登錄。 下面的範例將登錄到名為*MyHelmACR*的 ACR。

```azurecli
az acr login --name MyHelmACR
```

該命令在完成一次*返回"登錄成功"* 消息。

## <a name="create-an-azure-kubernetes-service-cluster"></a>建立 Azure Kubernetes Service 叢集

建立 AKS 叢集。 以下命令創建名為 MyAKS 的 AKS 群集並附加 MyHelmACR。

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

AKS 群集需要存取 ACR 以拉取容器映像並運行它們。 上述命令還授予*MyAKS*群集對*MyHelmACR* ACR 的訪問許可權。

## <a name="connect-to-your-aks-cluster"></a>連接到 AKS 叢集

若要從本機電腦連線至 Kubernetes 叢集，您應使用 [kubectl][kubectl] (Kubernetes 命令列用戶端)。

如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 您也可以使用 [az aks install-cli][] 命令將其安裝於本機：

```azurecli
az aks install-cli
```

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][] 命令。 下面的範例取得*MyResource 群組中*名為*MyAKS*的 AKS 叢集的認證 :

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>下載範例應用程式

此快速入門使用[Azure 開發人員空間範例儲存庫中的 node.js 應用程式][example-nodejs]範例。 從 GitHub 複製應用程式並導航`dev-spaces/samples/nodejs/getting-started/webfrontend`到 目錄。

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>建立 Dockerfile

使用以下內容建立新的*Dockerfile 檔案*:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>產生範例應用程式並將其推送到 ACR

使用[az acr 清單][az-acr-list]指令取得登入伺服器位址,並查詢*登入伺服器*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用 Docker 產生、標記範例應用程式容器並將其推送到 ACR:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>建立您的標頭

使用命令`helm create`生成 Helm 圖表。

```console
helm create webfrontend
```

對*Webfrontend/value*進行以下更新。

* 將 `image.repository` 變更為 `<acrLoginServer>/webfrontend`
* 將 `service.type` 變更為 `LoadBalancer`

例如：

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: <acrLoginServer>/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

更新`appVersion`到`v1` *webfrontend/Chart.yaml*。 例如：

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>執行您的標頭盔圖表

使用`helm create`命令使用 Helm 圖表安裝應用程式。

```console
helm install webfrontend webfrontend/
```

需要幾分鐘的時間，服務才能傳回公用 IP 位址。 若要監視進度，請使用 `kubectl get service` 命令搭配 watch** 參數：

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

使用`<EXTERNAL-IP>`查看範例應用程式的瀏覽器導航到應用程式的負載均衡器。

## <a name="delete-the-cluster"></a>選取叢集

當不再需要群集時,使用[az 組刪除][az-group-delete]命令刪除資源組、AKS 群集、容器註冊表、存儲在那裡的容器映射以及所有相關資源。

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱 [AKS 服務主體的考量和刪除][sp-delete]。 如果您使用受控識別，則身分識別會由平台負責管理，您不需要刪除。

## <a name="next-steps"></a>後續步驟

有關使用 Helm 的詳細資訊,請參閱 Helm 文檔。

> [!div class="nextstepaction"]
> [Helm 文件][helm-documentation]

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
