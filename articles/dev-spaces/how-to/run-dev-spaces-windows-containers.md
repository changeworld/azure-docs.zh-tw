---
title: 與 Windows 容器互動
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: 瞭解如何使用 Windows 容器在現有的叢集上執行 Azure Dev Spaces
keywords: Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器、Windows 容器
ms.openlocfilehash: 51a91c9ff74c4d0d1c5bccff2f1033eb14cd9198
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972929"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>使用 Azure Dev Spaces 與 Windows 容器互動

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

您可以在新的和現有的 Kubernetes 命名空間上啟用 Azure Dev Spaces。 Azure Dev Spaces 將會執行並檢測在 Linux 容器上執行的服務。 這些服務也可以與在相同命名空間中的 Windows 容器上執行的應用程式互動。 本文說明如何使用 Dev Spaces，在具有現有 Windows 容器的命名空間中執行服務。 目前，您無法使用 Azure Dev Spaces 來進行調試或附加至 Windows 容器。

## <a name="set-up-your-cluster"></a>設定您的叢集

本文假設您已有具有 Linux 和 Windows 節點集區的叢集。 如果您需要建立具有 Linux 和 Windows 節點集區的叢集，您可以依照 [此處][windows-container-cli]的指示進行。

使用 [kubectl][kubectl]（Kubernetes 命令列用戶端）連接到您的叢集。 若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要驗證針對您叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```azurecli-interactive
kubectl get nodes
```

下列範例輸出顯示同時具有 Windows 和 Linux 節點的叢集。 請先確定每個節點的狀態都已 *就緒* ，再繼續進行。

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

將 [污點][using-taints] 套用至您的 Windows 節點。 Windows 節點上的污點可防止開發人員空間將 Linux 容器排程在 Windows 節點上執行。 下列命令範例命令會將污點套用至先前範例中的 *aksnpwin987654* Windows 節點。

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> 當您將污點套用到節點時，必須在服務的部署範本中設定相符的 >toleration，以在該節點上執行您的服務。 範例應用程式已經使用 [相符的 >toleration][sample-application-toleration-example] 來設定，以符合您在上一個命令中設定的污點。

## <a name="run-your-windows-service"></a>執行您的 Windows 服務

在 AKS 叢集上執行您的 Windows 服務，並確認它處於 *執行中* 狀態。 本文使用 [範例應用程式][sample-application] 來示範在您的叢集上執行的 Windows 和 Linux 服務。

從 GitHub 複製範例應用程式，並流覽至 `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` 目錄：

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

範例應用程式會使用 [Helm 3][helm-installed] ，在您的叢集上執行 Windows 服務。 流覽至 `charts` 目錄，並使用 Helm 執行 Windows 服務：

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

上述命令會使用 Helm，在 *dev* 命名空間中執行您的 Windows 服務。 如果您沒有名為 *dev*的命名空間，則會加以建立。

使用 `kubectl get pods` 命令來確認您的 Windows 服務正在叢集中執行。 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>啟用 Azure Dev Spaces

在您用來執行 Windows 服務的相同命名空間中啟用 Dev Spaces。 下列命令會在 *dev* 命名空間中啟用 dev Spaces：

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>更新開發人員空間的 Windows 服務

當您在具有已執行之容器的現有命名空間上啟用 Dev Spaces 時，Dev Spaces 預設會嘗試並檢測在該命名空間中執行的任何新容器。 開發人員空間也會嘗試並檢測為已在命名空間中執行的服務所建立的任何新容器。 若要防止 Dev Spaces 檢測在命名空間中執行的容器，請將 *無 proxy* 標頭新增至 `deployment.yaml` 。

新增 `azds.io/no-proxy: "true"` 至檔案 `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` ：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

使用 `helm list` 列出 Windows 服務的部署：

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

在上述範例中，您的部署名稱是 *windows 服務*。 使用新的設定來更新您的 Windows 服務 `helm upgrade` ：

```cmd
helm upgrade windows-service . --namespace dev
```

由於您已更新了 `deployment.yaml` ，因此開發人員空間將不會嘗試並檢測您的服務。

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 執行您的 Linux 應用程式

流覽至 `webfrontend` 目錄，並使用 `azds prep` 和 `azds up` 命令在您的叢集上執行您的 Linux 應用程式。

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

此 `azds prep --enable-ingress` 命令會為您的應用程式產生 Helm 圖表和 dockerfile。

> [!TIP]
> Azure Dev Spaces 會使用專案的 [Dockerfile 和 Helm 圖表](../how-dev-spaces-works-prep.md#prepare-your-code)來建置和執行您的程式碼，但如果您想要變更專案的建置和執行方式，可以修改這些檔案。

此 `azds up` 命令會在命名空間中執行您的服務。

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

您可以開啟公用 URL，以查看正在執行的服務，該 URL 會顯示在 azds up 命令的輸出中。 在此範例中，公用 URL 為 `http://dev.webfrontend.abcdef0123.eus.azds.io/`。 在瀏覽器中流覽至服務，然後按一下頂端的 [ *關於* ]。 確認您看到來自 *mywebapi* 服務的訊息，其中包含容器所使用的 Windows 版本。

![顯示 mywebapi 的 Windows 版本的範例應用程式](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>後續步驟

深入了解 Azure Dev Spaces 的運作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 如何運作](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
