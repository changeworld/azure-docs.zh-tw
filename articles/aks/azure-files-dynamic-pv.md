---
title: 動態建立 Azure 檔案分享
titleSuffix: Azure Kubernetes Service
description: 了解如何透過 Azure 檔案服務以動態方式建立永續性磁碟區，以搭配 Azure Kubernetes Service (AKS) 中的多個平行 Pod 使用
services: container-service
ms.topic: article
ms.date: 09/12/2019
ms.openlocfilehash: 59b773cd4608187fedb24358eac57715e1c271ea
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803529"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中以動態方式建立和使用 Azure 檔案服務的永續性磁碟區

永續性磁碟區代表一塊已佈建來與 Kubernetes Pod 搭配使用的儲存體。 永續性磁碟區可供一個或多個 Pod 使用，且可以動態或靜態方式佈建。 如果有多個 Pod 需要並行存取相同的儲存體磁碟區，您可以透過[伺服器訊息區 (SMB) 通訊協定][smb-overview]來使用 Azure 檔案服務進行連線。 本文會示範如何在 Azure Kubernetes Service (AKS) 叢集中以動態方式建立 Azure 檔案共用，以供多個 Pod 使用。

有關 Kubernets 卷的詳細資訊,請參閱[AKS 中應用程式的儲存選項][concepts-storage]。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您還需要 Azure CLI 版本 2.0.59 或更高版本安裝和配置。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="create-a-storage-class"></a>建立儲存體類別

儲存體類別可用來定義 Azure 檔案共用的建立方式。 在[節點資源組中][node-resource-group]自動建立儲存帳戶,以便與存儲類一起保存 Azure 檔共用。 為 *skuName* 選擇下列 [Azure 儲存體備援][storage-skus]：

* *Standard_LRS* - 標準本地備援儲存體 (LRS)
* *Standard_GRS* - 標準異地備援儲存體 (GRS)
* *Standard_ZRS* - 標準區域冗餘儲存 (ZRS)
* *Standard_RAGRS* - 標準讀取權限異地備援儲存體 (RA-GRS)
* *Premium_LRS* - 優質本地冗餘儲存 (LRS)
* *Premium_ZRS* - 進階區域冗餘儲存 (GRS)

> [!NOTE]
> Azure 檔案支援執行 Kubernets 1.13 或更高、最小進階檔案分享為 100GB 的 AKS 群組的進階儲存

有關 Azure 檔的庫伯奈斯儲存類別的詳細資訊,請參閱[庫伯奈斯儲存類][kubernetes-storage-classes]。

建立名為 `azure-file-sc.yaml` 的檔案，然後將下列資訊清單範例複製進來。 如需 mountOptions** 的詳細資訊，請參閱[掛接選項][mount-options]一節。

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

使用[kubectl 應用程式][kubectl-apply]使用指令建立儲存類別:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>建立永續性磁碟區宣告

永續性磁碟區宣告 (PVC) 會使用儲存體類別物件，以動態方式佈建 Azure 檔案共用。 以下 YAML 可用於使用*ReadWriteMany*訪問創建 5 *GB*大小的持久卷聲明。 如需有關存取模式的詳細資訊，請參閱 [Kubernetes 永續性磁碟區][access-modes]文件。

現在，建立名為 `azure-file-pvc.yaml` 的檔案，然後將下列 YAML 複製進來。 請確定 storageClassName** 與最後一個步驟中建立的儲存體類別相符。

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
> 如果儲存*類別的*Premium_LRS sku,*則儲存*的最小值必須為*100Gi*。

使用[kubectl 應用程式][kubectl-apply]使用指令建立持久卷聲明:

```console
kubectl apply -f azure-file-pvc.yaml
```

完成之後，便會建立檔案共用。 此外，也會建立 Kubernetes 祕密，其中包含連線資訊和認證。 您可以使用 [kubectl get][kubectl-get] 命令來檢視 PVC 狀態：

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>使用永續性磁碟區

下列 YAML 所建立的 Pod，會使用永續性磁碟區宣告 azurefile**，將 Azure 檔案共用裝載在 /mnt/azure** 路徑。 對於 Windows 伺服器容器(目前在 AKS 中預覽),使用 Windows 路徑約定(如 *"D:")* 指定*載入路徑*。

建立名為 `azure-pvc-files.yaml` 的檔案，然後將下列 YAML 複製進來。 請確定claimName** 與最後一個步驟中建立的 PVC 相符。

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

使用 [kubectl apply][kubectl-apply] 命令來建立 Pod。

```console
kubectl apply -f azure-pvc-files.yaml
```

您現在已有一個 Azure 檔案共用掛接在 /mnt/azure** 目錄中的執行中 Pod。 當您透過 `kubectl describe pod mypod` 檢查 Pod 時，可以看到這項設定。 下列扼要範例輸出顯示容器中掛接的磁碟區：

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

對於 Kubernetes 版本 1.13.0 及以上,*檔案模式*和*dirMode*的預設值為*0777。* 如果使用存儲類動態創建持久卷,則可以在存儲類物件上指定裝載選項。 下列範例會設定 0777**：

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

有關相關的最佳實務,請參閱[AKS 中儲存和備份的最佳做法][operator-best-practices-storage]。

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
