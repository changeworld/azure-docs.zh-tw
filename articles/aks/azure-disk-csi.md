---
title: '在 Azure Kubernetes Service (AKS 上使用適用于 Azure 磁片的 (CSI) 驅動程式容器儲存體介面) '
description: 瞭解如何在 Azure Kubernetes Service (AKS) 叢集中使用 Azure 磁片的容器儲存體介面 (CSI) 驅動程式。
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 68a892768e5cfa5be7fe6f9ad99fc4cded68b02d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071807"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>在 Azure Kubernetes Service (AKS)  (preview 中使用 Azure 磁片容器存放裝置介面 (CSI) 驅動程式) 
Azure 磁片容器存放裝置介面 (CSI) 驅動程式是符合 [CSI 規格規範](https://github.com/container-storage-interface/spec/blob/master/spec.md)的驅動程式，AZURE KUBERNETES SERVICE (AKS) 用來管理 Azure 磁片的生命週期。

CSI 是將任意區塊和檔案儲存系統公開給 Kubernetes 上容器化工作負載的標準。 藉由採用和使用 CSI，AKS 可以撰寫、部署及反覆運算外掛程式，以在 Kubernetes 中公開新的或改善現有的儲存系統，而不需要觸控核心 Kubernetes 程式碼並等候其發行週期。

若要建立具有 CSI 驅動程式支援的 AKS 叢集，請參閱 [啟用 Azure 磁片的 csi 驅動程式和 AKS 上的 Azure 檔案儲存體](csi-storage-drivers.md)。

>[!NOTE]
> *樹狀結構中的驅動程式* 是指核心 Kubernetes 程式碼中的目前儲存驅動程式，與新的 CSI 驅動程式（即外掛程式）的一部分。

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>搭配 Azure 磁片使用 CSI 永久性磁片區

[持續性磁片](concepts-storage.md#persistent-volumes)區 (PV) 代表布建來與 Kubernetes pod 搭配使用的一段儲存體。 PV 可供一或多個 pod 使用，且可以動態或靜態方式布建。 本文說明如何使用 Azure 磁片，以動態方式建立 PVs，以供 AKS 叢集中的單一 pod 使用。 如需靜態布建，請參閱 [手動建立和使用 Azure 磁片的磁片](azure-disk-volume.md)區。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

如需 Kubernetes 磁碟區的詳細資訊，請參閱 [AKS 中的應用程式適用的儲存體選項][concepts-storage]。

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>使用內建的儲存類別，以動態方式建立 Azure 磁片 PVs

儲存體類別可用來定義如何搭配永續性磁碟區動態建立儲存體單位。 如需 Kubernetes 儲存體類別的詳細資訊，請參閱 [Kubernetes 儲存類別][kubernetes-storage-classes]。 當您在 AKS 上使用儲存體 CSI 驅動程式時，有兩個額外 `StorageClasses` 的內建使用 Azure 磁片 CSI 儲存體驅動程式。 額外的 CSI 儲存類別是以叢集和樹狀目錄預設儲存類別一起建立。

- `managed-csi`：使用 Azure 標準 SSD 本地儲存體 (LRS) 來建立受控磁片。
- `managed-csi-premium`：使用 Azure Premium LRS 來建立受控磁片。

這兩種儲存類別中的回收原則，可確保在刪除個別的 PV 時，會刪除基礎的 Azure 磁片。 儲存類別也會將 PVs 設定為可展開。 您只需要以新的大小編輯持續性磁片區宣告 (PVC) 。

若要利用這些儲存類別，請建立一個會參考和使用的 [PVC](concepts-storage.md#persistent-volume-claims) 和各自的 pod。 PVC 可用來根據儲存類別自動布建儲存體。 PVC 可以使用其中一個預先建立的儲存體類別或使用者定義的儲存體類別，針對所需的 SKU 和大小建立 Azure 受控磁片。 當您建立 pod 定義時，會指定 PVC 來要求所需的儲存體。

使用 [kubectl apply][kubectl-apply] 命令建立範例 pod 和個別的 PVC：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

當 pod 處於執行中狀態之後，請建立名為的新檔案 `test.txt` 。

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

您現在可以藉由執行下列命令來驗證磁片是否已正確掛接，並確認您 `test.txt` 在輸出中看到該檔案：

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>建立自訂儲存類別

預設的儲存類別符合最常見的案例，但並非全部。 在某些情況下，您可能會想要使用您自己的參數自訂自己的儲存類別。 例如，我們有一個案例，您可能會想要變更 `volumeBindingMode` 類別。

預設的儲存類別會使用 `volumeBindingMode: Immediate` 可保證在建立 PVC 之後立即發生的類別。 如果您的節點集區是拓撲限制的（例如，使用可用性區域），則 PVs 會系結或布建，而不需要知道 pod 的排程需求 (在此案例中是在特定區域) 。

若要解決此案例，您可以使用 `volumeBindingMode: WaitForFirstConsumer` ，這會延遲系結和布建的 PV，直到建立使用 PVC 的 pod 為止。 如此一來，PV 將符合並布建在可用性區域中， (或 pod 的排程條件約束所指定的其他拓撲) 。

建立名為的檔案 `sc-azuredisk-csi-waitforfirstconsumer.yaml` ，並貼上下列資訊清單。
儲存類別與我們的 `managed-csi` 儲存類別相同，但具有不同的 `volumeBindingMode` 類別。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

使用 [kubectl apply][kubectl-apply] 命令建立儲存類別，並指定您的檔案 `sc-azuredisk-csi-waitforfirstconsumer.yaml` ：

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>磁碟區快照

Azure 磁片 CSI 驅動程式支援建立 [永久性磁片區的快照](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html)集。 作為這項功能的一部分，驅動程式可以根據預設值 (中設定的值來執行 *完整* 或 [*增量* 快照](../virtual-machines/windows/disks-incremental-snapshots.md) 集 `incremental` ，這是) 。

如需所有參數的詳細資訊，請參閱 [磁片區快照集類別參數](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass)。

### <a name="create-a-volume-snapshot"></a>建立磁片區快照集

如需這項功能的範例，請使用[kubectl apply][kubectl-apply]命令建立[磁片區快照集類別](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml)：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

現在，讓我們從[本教學課程開頭動態建立](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes)的 PVC 建立[磁片區快照](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml)集 `pvc-azuredisk` 。


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

檢查是否已正確建立快照集：

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>根據磁片區快照集建立新的 PVC

您可以根據磁片區快照集建立新的 PVC。 使用上一個步驟中所建立的快照集，並建立 [新的 PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) 和新的 [pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) 來使用它。

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

最後，請確定它是在檢查內容之前所建立的相同 PVC。

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

如預期般，我們仍然可以看到先前建立的檔案 `test.txt` 。

## <a name="clone-volumes"></a>複製磁片區

複製的磁片區會定義為現有 Kubernetes 磁片區的複本。 如需有關在 Kubernetes 中複製磁片區的詳細資訊，請參閱 [磁片區複製](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)的概念檔。

適用于 Azure 磁片的 CSI 驅動程式支援磁片區複製。 若要示範，請建立[先前建立](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes)的[複製磁片](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)區 `azuredisk-pvc` ，然後建立[新的 pod 來取用它](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)。


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

我們現在可以藉由執行下列命令來檢查複製的磁片區內容，並確認仍看到我們 `test.txt` 建立的檔案。

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>調整永久性磁片區的大小

您可以改為針對 PVC 要求較大的磁片區。 編輯 PVC 物件，並指定較大的大小。 這項變更會觸發支援 PV 的基礎磁片區擴充。

> [!NOTE]
> 永遠不會建立新的 PV 來滿足宣告。 相反地，會調整現有磁片區的大小。

在 AKS 中，內建的 `managed-csi` 儲存類別已可進行擴充，因此請使用稍 [早建立的 PVC 搭配此儲存類別](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes)。 PVC 要求 10-Gi 的永久性磁片區。 我們可以藉由執行下列動作來確認：

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> 目前，Azure 磁片 CSI 驅動程式只支援調整 Pvc (沒有相關聯的 pod，而且未將磁片區掛接到特定節點) 。

因此，讓我們刪除稍早建立的 pod：

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

讓我們藉由增加欄位來展開 PVC `spec.resources.requests.storage` ：

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

讓我們確認磁片區現在更大：

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> PVC 不會反映新的大小，除非它再次有相關聯的 pod。

讓我們建立新的 pod：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

最後，確認 PVC 和 pod 內的大小：
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>共用磁片

[Azure 共用磁片](../virtual-machines/windows/disks-shared.md) 是 azure 受控磁片的功能，可同時將 azure 磁片附加至代理程式節點。 例如，將受控磁片連結至多個代理程式節點可讓您將新的叢集應用程式部署至 Azure，或將其遷移至 Azure。

> [!IMPORTANT] 
> 目前， `volumeMode: Block` Azure 磁片 CSI 驅動程式只支援原始區塊裝置 () 。 應用程式應該在共用磁片上管理寫入、讀取、鎖定、快取、裝載和隔離的協調和控制，這會以原始區塊裝置的形式公開。

讓我們藉 `shared-disk.yaml` 由複製下列包含共用磁片儲存類別和 PVC 的命令來建立呼叫的檔案：

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

使用 [kubectl apply][kubectl-apply] 命令建立儲存類別，並指定您的檔案 `shared-disk.yaml` ：

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

現在，讓我們藉 `deployment-shared.yml` 由複製下列命令來建立呼叫的檔案：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: nginx
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

使用 [kubectl apply][kubectl-apply] 命令建立部署，並指定您的檔案 `deployment-shared.yml` ：

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

最後，讓我們檢查 pod 內的區塊裝置：

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp bash
root@deployment-sharedisk-7454978bc6-xh7jp:/# dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.0502999 s, 2.1 GB/s
```

## <a name="windows-containers"></a>Windows 容器

Azure 磁片 CSI 驅動程式也支援 Windows 節點和容器。 如果您想要使用 Windows 容器，請遵循 [windows 容器教學](windows-container-cli.md) 課程來新增 windows 節點集區。

當您擁有 Windows 節點集區之後，您現在可以使用內建的儲存類別，例如 `managed-csi` 。 您可以[Windows-based stateful set](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) `data.txt` 使用[kubectl apply][kubectl-apply]命令部署下列命令，以部署將時間戳記儲存至檔案的範例 Windows 型具狀態設定：

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

您現在可以執行下列動作來驗證磁片區的內容：

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何使用 CSI 驅動程式進行 Azure 檔案儲存體，請參閱搭配 [使用 Azure 檔案儲存體與 csi 驅動程式](azure-files-csi.md)。
- 如需儲存體最佳作法的詳細資訊，請參閱 [Azure Kubernetes Service 中儲存和備份的最佳作法][operator-best-practices-storage]。


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register