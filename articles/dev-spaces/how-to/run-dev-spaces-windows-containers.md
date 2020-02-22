---
title: 與 Windows 容器互動
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: 瞭解如何在具有 Windows 容器的現有叢集上執行 Azure Dev Spaces
keywords: Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器，Windows 容器
ms.openlocfilehash: d376aca45778060c8913924fd2a44031109390d2
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538784"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>使用 Azure Dev Spaces 與 Windows 容器互動

您可以在新的和現有的 Kubernetes 命名空間上啟用 Azure Dev Spaces。 Azure Dev Spaces 將會執行並檢測在 Linux 容器上執行的服務。 這些服務也可以與在相同命名空間中的 Windows 容器上執行的應用程式互動。 本文說明如何在具有現有 Windows 容器的命名空間中，使用 Dev Spaces 來執行服務。 目前，您無法使用 Azure Dev Spaces 來進行調試或附加至 Windows 容器。

## <a name="set-up-your-cluster"></a>設定您的叢集

本文假設您已經有一個具有 Linux 和 Windows 節點集區的叢集。 如果您需要使用 Linux 和 Windows 節點集區建立叢集，您可以依照[此處][windows-container-cli]的指示進行。

使用[kubectl][kubectl]（Kubernetes 命令列用戶端）連接到您的叢集。 若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要驗證針對您叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```azurecli-interactive
kubectl get nodes
```

下列範例輸出顯示同時具有 Windows 和 Linux 節點的叢集。 請確定每個節點的狀態都是 [*就緒*]，然後再繼續。

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

將[污點][using-taints]套用至您的 Windows 節點。 Windows 節點上的污點可防止 Dev Spaces 排程 Linux 容器，以在您的 Windows 節點上執行。 下列命令範例命令會將污點套用至上一個範例中的 [ *aksnpwin987654* Windows] 節點。

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> 當您將污點套用至節點時，您必須在服務的部署範本中設定相符的 toleration，才能在該節點上執行您的服務。 範例應用程式已經使用[對應的 toleration][sample-application-toleration-example]來設定您在上一個命令中設定的污點。

## <a name="run-your-windows-service"></a>執行您的 Windows 服務

在 AKS 叢集上執行您的 Windows 服務，並確認它處於*執行中*狀態。 本文使用[範例應用程式][sample-application]來示範在您的叢集上執行的 Windows 和 Linux 服務。

從 GitHub 複製範例應用程式，並流覽至 `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` 目錄：

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

範例應用程式會使用[Helm 3][helm-installed]來執行叢集上的 Windows 服務。 流覽至 `charts` 目錄，並使用 Helm 執行 Windows 服務：

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

上述命令會使用 Helm 在*dev*命名空間中執行您的 Windows 服務。 如果您沒有名為*dev*的命名空間，則會建立它。

使用 `kubectl get pods` 命令來確認您的 Windows 服務正在叢集中執行。 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>啟用 Azure Dev Spaces

在您用來執行 Windows 服務的相同命名空間中啟用 Dev Spaces。 下列命令會在*dev*命名空間中啟用 dev Spaces：

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>更新適用于 Dev Spaces 的 Windows 服務

當您使用已在執行的容器在現有命名空間上啟用 Dev Spaces 時，根據預設，Dev Spaces 會嘗試並檢測在該命名空間中執行的任何新容器。 開發人員空間也會嘗試並檢測為已在命名空間中執行的服務所建立的任何新容器。 若要避免 Dev Spaces 檢測在您的命名空間中執行的容器，請將*無 proxy*標頭新增至 `deployment.yaml`。

將 `azds.io/no-proxy: "true"` 新增至 `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` 檔案：

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

在上述範例中，您的部署名稱是*windows 服務*。 使用 `helm upgrade`，以新的設定更新您的 Windows 服務：

```cmd
helm upgrade windows-service . --namespace dev
```

由於您已更新 `deployment.yaml`，因此 Dev Spaces 不會嘗試和檢測您的服務。

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 執行您的 Linux 應用程式

流覽至 `webfrontend` 目錄，然後使用 `azds prep` 和 `azds up` 命令，在叢集上執行您的 Linux 應用程式。

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

`azds prep --enable-ingress` 命令會為您的應用程式產生 Helm 圖表和 Dockerfile。

> [!TIP]
> Azure Dev Spaces 會使用專案的 [Dockerfile 和 Helm 圖表](../how-dev-spaces-works.md#prepare-your-code)來建置和執行您的程式碼，但如果您想要變更專案的建置和執行方式，可以修改這些檔案。

`azds up` 命令會在命名空間中執行您的服務。

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

您可以藉由開啟公用 URL （顯示在 azds up 命令的輸出中），查看執行中的服務。 在此範例中，公用 URL 是 `http://dev.webfrontend.abcdef0123.eus.azds.io/`。 在瀏覽器中流覽至服務，然後按一下頂端的 [*關於*]。 確認您看到來自*mywebapi*服務的訊息，其中包含容器所使用的 Windows 版本。

![顯示來自 mywebapi 之 Windows 版本的範例應用程式](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [在 Azure Dev Spaces 中進行小組開發][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
