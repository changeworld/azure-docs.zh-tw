---
title: 在 Azure Kubernetes Service (AKS) 中以動態方式建立適用於多個 Pod 的檔案磁碟區
description: 了解如何透過 Azure 檔案服務以動態方式建立永續性磁碟區，以搭配 Azure Kubernetes Service (AKS) 中的多個平行 Pod 使用
services: container-service
ms.topic: article
ms.date: 09/12/2019
ms.openlocfilehash: ef9ef10a5523bd91b346e16e105c5ff5cd9cb669
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897717"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中以動態方式建立和使用 Azure 檔案服務的永續性磁碟區

永續性磁碟區代表一塊已佈建來與 Kubernetes Pod 搭配使用的儲存體。 永續性磁碟區可供一個或多個 Pod 使用，且可以動態或靜態方式佈建。 如果多個 pod 需要平行存取相同的存放磁片區，您可以使用 Azure 檔案儲存體，以使用[伺服器訊息區（SMB）通訊協定][smb-overview]進行連接。 本文會示範如何在 Azure Kubernetes Service (AKS) 叢集中以動態方式建立 Azure 檔案共用，以供多個 Pod 使用。

如需有關 Kubernetes 磁片區的詳細資訊，請參閱[AKS 中應用程式的儲存體選項][concepts-storage]。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您也需要安裝並設定 Azure CLI 版本2.0.59 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="create-a-storage-class"></a>建立儲存體類別

儲存體類別可用來定義 Azure 檔案共用的建立方式。 系統會自動在[節點資源群組][node-resource-group]中建立儲存體帳戶，以搭配儲存類別來存放 Azure 檔案共用。 選擇下列適用于*skuName*的[Azure 儲存體冗余][storage-skus]：

* *Standard_LRS* - 標準本地備援儲存體 (LRS)
* *Standard_GRS* - 標準異地備援儲存體 (GRS)
* *Standard_ZRS* -標準區域冗余儲存體（GRS）
* *Standard_RAGRS* - 標準讀取權限異地備援儲存體 (RA-GRS)
* *Premium_LRS* -Premium 本機多餘儲存體（LRS）
* *Premium_ZRS* -Premium 區域冗余儲存體（GRS）

> [!NOTE]
> Azure 檔案儲存體在執行 Kubernetes 1.13 或更高版本的 AKS 叢集中支援 premium 儲存體，最低 premium 檔案共用為100GB

如需 Azure 檔案儲存體的 Kubernetes 儲存體類別的詳細資訊，請參閱[Kubernetes 儲存體類別][kubernetes-storage-classes]。

建立名為 `azure-file-sc.yaml` 的檔案，然後將下列資訊清單範例複製進來。 如需*mountOptions*的詳細資訊，請參閱[掛接選項][mount-options]一節。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

使用[kubectl apply][kubectl-apply]命令來建立儲存體類別：

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>建立永續性磁碟區宣告

永續性磁碟區宣告 (PVC) 會使用儲存體類別物件，以動態方式佈建 Azure 檔案共用。 下列 YAML 可以用來建立具有*ReadWriteMany*存取權的持續性磁片區索取*5 GB*大小。 如需有關存取模式的詳細資訊，請參閱[Kubernetes 持續性磁片][access-modes]區檔。

現在，建立名為 `azure-file-pvc.yaml` 的檔案，然後將下列 YAML 複製進來。 請確定 storageClassName 與最後一個步驟中建立的儲存體類別相符。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> 如果您的儲存體類別使用*Premium_LRS* sku，則*儲存體*的最小值必須是*100Gi*。

使用[kubectl apply][kubectl-apply]命令來建立持續性磁片區宣告：

```console
kubectl apply -f azure-file-pvc.yaml
```

完成之後，便會建立檔案共用。 此外，也會建立 Kubernetes 祕密，其中包含連線資訊和認證。 您可以使用[kubectl get][kubectl-get]命令來查看 PVC 的狀態：

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>使用永續性磁碟區

下列 YAML 所建立的 Pod，會使用永續性磁碟區宣告 azurefile，將 Azure 檔案共用裝載在 /mnt/azure 路徑。 若是 Windows Server 容器（目前在 AKS 中處於預覽狀態），請使用 Windows 路徑慣例指定*mountPath* ，例如「 *d：* 」。

建立名為 `azure-pvc-files.yaml` 的檔案，然後將下列 YAML 複製進來。 請確定claimName 與最後一個步驟中建立的 PVC 相符。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

使用[kubectl apply][kubectl-apply]命令建立 pod。

```console
kubectl apply -f azure-pvc-files.yaml
```

您現在已有一個 Azure 檔案共用掛接在 /mnt/azure 目錄中的執行中 Pod。 當您透過 `kubectl describe pod mypod` 檢查 Pod 時，可以看到這項設定。 下列扼要範例輸出顯示容器中掛接的磁碟區：

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>掛接選項

*適用于*Kubernetes 版本1.13.0 和以上的*dirMode*的預設值為*0777* 。 如果以儲存類別動態建立持續性磁片區，則可以在儲存類別物件上指定掛接選項。 下列範例會設定 0777：

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>後續步驟

如需相關的最佳作法，請參閱[AKS 中儲存和備份的最佳作法][operator-best-practices-storage]。

使用「Azure 檔案」來深入了解 Kubernetes 永續性磁碟區。

> [!div class="nextstepaction"]
> [適用於 Azure 檔案的 Kubernetes 外掛程式][kubernetes-files] \(英文\)

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
