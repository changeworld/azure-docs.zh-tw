---
title: 與 Windows 容器互動
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: 瞭解如何使用 Windows 容器在現有群集上運行 Azure 開發人員空間
keywords: Azure 開發空間、開發空間、Docker、庫伯奈斯、Azure、AKS、Azure 庫伯奈斯服務、容器、Windows 容器
ms.openlocfilehash: 0b3f221c9e62343a02ba8742e4cf988c7cf26c12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240493"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>使用 Azure 開發空間與 Windows 容器交互

您可以在新的 Kubernetes 命名空間上啟用 Azure 開發空間。 Azure 開發空間將運行並檢測在 Linux 容器上運行的服務。 這些服務還可以與在同一命名空間中的 Windows 容器上運行的應用程式進行交互。 本文介紹如何使用開發人員空間在現有 Windows 容器的命名空間中運行服務。 此時，您無法調試或附加到具有 Azure 開發空間的 Windows 容器。

## <a name="set-up-your-cluster"></a>設置群集

本文假定您已經有一個同時具有 Linux 和 Windows 節點池的群集。 如果需要創建具有 Linux 和 Windows 節點池的群集，可以[按照此處][windows-container-cli]的說明操作。

使用庫貝內特斯命令列用戶端[庫布埃特爾][kubectl]連接到群集。 若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要驗證針對您叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```azurecli-interactive
kubectl get nodes
```

以下示例輸出顯示了同時具有 Windows 和 Linux 節點的群集。 在繼續操作之前，請確保每個節點的狀態*已就緒*。

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

對 Windows 節點應用[污點][using-taints]。 Windows 節點上的污點阻止開發人員空間將 Linux 容器安排在 Windows 節點上運行。 以下命令示例命令對上一個示例中的*aksnpwin987654* Windows 節點應用污點。

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> 將污點應用於節點時，必須在服務的部署範本中配置匹配的容忍，才能在該節點上運行服務。 應用程式範例已配置了與上一命令中配置的污點[匹配的容忍][sample-application-toleration-example]。

## <a name="run-your-windows-service"></a>運行 Windows 服務

在 AKS 群集上運行 Windows 服務，並驗證其處於 *"正在運行"* 狀態。 本文使用[應用程式範例][sample-application]來演示群集上運行的 Windows 和 Linux 服務。

從 GitHub 克隆應用程式範例並導航到`dev-spaces/samples/existingWindowsBackend/mywebapi-windows`目錄中：

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

應用程式範例使用[Helm 3][helm-installed]在群集上運行 Windows 服務。 導航到`charts`目錄並使用 Helm 運行 Windows 服務：

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

上述命令使用 Helm 在*開發命名*空間中運行 Windows 服務。 如果沒有名為*dev*的命名空間，它將創建它。

使用`kubectl get pods`命令驗證您的 Windows 服務在群集中運行。 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>啟用 Azure 開發空間

在用於運行 Windows 服務的相同命名空間中啟用開發空間。 以下命令支援*開發空間中的開發*空間：

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>更新開發空間的 Windows 服務

預設情況下，當您在現有命名空間上啟用 Dev Spaces 時，該容器已運行，預設情況下，開發人員空間將嘗試並檢測該命名空間中運行的任何新容器。 開發空間還將嘗試檢測為已在命名空間中運行的服務創建的任何新容器。 為防止開發人員空間檢測在命名空間中運行的容器，請將*非代理*標頭添加到 。 `deployment.yaml`

添加到`azds.io/no-proxy: "true"``existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml`檔：

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

用於`helm list`列出 Windows 服務的部署：

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

在上面的示例中，部署的名稱是*視窗服務*。 使用 更新 Windows 服務，使用`helm upgrade`：

```cmd
helm upgrade windows-service . --namespace dev
```

由於您更新了`deployment.yaml`，開發空間將不會嘗試並檢測您的服務。

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>使用 Azure 開發空間運行 Linux 應用程式

導航到目錄`webfrontend`，並使用`azds prep`和`azds up`命令在群集上運行 Linux 應用程式。

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

該`azds prep --enable-ingress`命令為您的應用程式生成 Helm 圖表和 Dockerfile。

> [!TIP]
> Azure Dev Spaces 會使用專案的 [Dockerfile 和 Helm 圖表](../how-dev-spaces-works-prep.md#prepare-your-code)來建置和執行您的程式碼，但如果您想要變更專案的建置和執行方式，可以修改這些檔案。

該`azds up`命令在命名空間中運行服務。

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

您可以通過打開公共 URL 來查看服務運行，該 URL 顯示在 azds up 命令的輸出中。 在此範例中，公用 URL 為 `http://dev.webfrontend.abcdef0123.eus.azds.io/`。 在瀏覽器中導航到服務，然後按一下"*關於*頂部"。 驗證您看到來自*mywebapi*服務的消息，其中包含容器正在使用的 Windows 版本。

![示例應用，顯示來自 mywebapi 的 Windows 版本](../media/run-dev-spaces-windows-containers/sample-app.png)

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
