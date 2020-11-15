---
title: '在 Azure Kubernetes Service (AKS 上，使用適用于 Azure 檔案儲存體的 (CSI) 驅動程式的容器儲存體介面) '
description: 瞭解如何在 Azure Kubernetes Service (AKS) 叢集中，針對 Azure 檔案儲存體使用 (CSI) 驅動程式的容器存放裝置介面。
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: b29f4034b12ce43e6c051e454601f196365469f3
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636975"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>在 Azure Kubernetes Service (AKS)  (preview 中使用 Azure 檔案儲存體容器儲存體介面 (CSI) 驅動程式) 

Azure 檔案儲存體容器存放裝置介面 (CSI) 驅動程式是一種符合 [CSI 規格規範](https://github.com/container-storage-interface/spec/blob/master/spec.md)的驅動程式，AZURE KUBERNETES SERVICE (AKS) 用來管理 Azure 檔案儲存體共用的生命週期。

CSI 是將任意區塊和檔案儲存系統公開給 Kubernetes 上容器化工作負載的標準。 藉由採用和使用 CSI，AKS 現在可以撰寫、部署及反覆運算外掛程式，以在 Kubernetes 中公開新的或改善現有的儲存系統，而不需要觸控核心 Kubernetes 程式碼並等候其發行週期。

若要建立具有 CSI 驅動程式支援的 AKS 叢集，請參閱 [啟用 Azure 磁片的 csi 驅動程式和 AKS 上的 Azure 檔案儲存體](csi-storage-drivers.md)。

>[!NOTE]
> *樹狀結構中的驅動程式* 是指核心 Kubernetes 程式碼中的目前儲存驅動程式，與新的 CSI 驅動程式（即外掛程式）的一部分。

## <a name="use-a-persistent-volume-with-azure-files"></a>使用具有 Azure 檔案儲存體的持續性磁片區

[持續性磁片區 (PV) ](concepts-storage.md#persistent-volumes)代表布建來與 Kubernetes pod 搭配使用的一段儲存體。 PV 可供一或多個 pod 使用，且可以動態或靜態方式布建。 如果有多個 pod 需要平行存取相同的存放磁片區，您可以使用 Azure 檔案儲存體 [ (SMB) 通訊協定][smb-overview]來連接。 本文說明如何以動態方式建立 Azure 檔案儲存體共用，以供 AKS 叢集中的多個 pod 使用。 如需靜態布建，請參閱 [手動建立和使用具有 Azure 檔案儲存體共用的磁片區](azure-files-volume.md)。

如需 Kubernetes 磁碟區的詳細資訊，請參閱 [AKS 中的應用程式適用的儲存體選項][concepts-storage]。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>使用內建的儲存類別，以動態方式建立 Azure 檔案儲存體 PVs

儲存體類別可用來定義建立 Azure 檔案儲存體共用的方式。 系統會自動在 [節點資源群組][node-resource-group] 中建立儲存體帳戶，以搭配儲存類別來保存 Azure 檔案儲存體共用。 選擇下列其中一個適用于 *skuName* 的 [Azure 儲存體冗余 sku][storage-skus] ：

* **Standard_LRS** ：標準本機冗余儲存體
* **Standard_GRS** ：標準異地冗余儲存體
* **Standard_ZRS** ：標準區域-多餘的儲存體
* **Standard_RAGRS** ：標準讀取權限異地冗余儲存體
* **Premium_LRS** ： Premium 本機冗余儲存體

> [!NOTE]
> Azure 檔案儲存體支援 Azure 進階儲存體。 最小 premium 檔案共用為 100 GB。

當您在 AKS 上使用 storage CSI 驅動程式時，有兩個額外 `StorageClasses` 的內建使用 AZURE 檔案儲存體 CSI 儲存驅動程式。 額外的 CSI 儲存類別是以叢集和樹狀目錄預設儲存類別一起建立。

- `azurefile-csi`：使用 Azure 標準儲存體建立 Azure 檔案儲存體共用。
- `azurefile-csi-premium`：使用 Azure 進階儲存體建立 Azure 檔案儲存體共用。

這兩種儲存類別的回收原則可確保在刪除個別的 PV 時，會刪除基礎 Azure 檔案儲存體共用。 儲存類別也會將檔案共用設定為可擴充，您只需要以新的大小編輯持續性磁片區宣告 (PVC) 。

若要使用這些儲存類別，請建立可參考並使用的 [PVC](concepts-storage.md#persistent-volume-claims) 和個別 pod。 PVC 可用來根據儲存類別自動布建儲存體。 PVC 可以使用其中一個預先建立的儲存體類別或使用者定義的儲存體類別，來建立所需 SKU 和大小的 Azure 檔案儲存體共用。 當您建立 pod 定義時，會指定 PVC 來要求所需的儲存體。

建立[範例 PVC 和 pod，此範例會將目前的日期 `outfile` 列印至](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml)with [kubectl apply][kubectl-apply]命令：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

當 pod 處於執行中狀態之後，您可以執行下列命令來驗證檔案共用是否已正確掛接，並確認輸出包含 `outfile` ：

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>建立自訂儲存類別

預設的儲存類別符合最常見的案例，但並非全部。 在某些情況下，您可能會想要使用您自己的參數自訂自己的儲存類別。 例如，使用下列資訊清單來設定檔案 `mountOptions` 共用的。

Kubernetes 掛接的檔案共用的預設 *值為0777，而* *dirMode* 為 *0777* 。 您可以在儲存類別物件上指定不同的掛接選項。

建立名為的檔案 `azure-file-sc.yaml` ，並貼上下列範例資訊清單：

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

使用 [kubectl apply][kubectl-apply] 命令建立儲存體類別：

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Azure 檔案儲存體 CSI 驅動程式支援建立持續性磁片區和基礎檔案共用 [的快照](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) 集。

使用[kubectl apply][kubectl-apply]命令建立[磁片區快照集類別](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml)：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

從[本教學課程開頭動態建立](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes)的 PVC 建立[磁片區快照](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml)集 `pvc-azurefile` 。


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

確認已正確建立快照集：

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>調整永久性磁片區的大小

您可以為 PVC 要求較大的磁片區。 編輯 PVC 物件，並指定較大的大小。 這項變更會觸發支援 PV 的基礎磁片區擴充。

> [!NOTE]
> 永遠不會建立新的 PV 來滿足宣告。 相反地，會調整現有磁片區的大小。

在 AKS 中，內建的 `azurefile-csi` 儲存類別已支援擴充，因此請使用稍 [早建立的 PVC 搭配此儲存類別](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes)。 PVC 要求了100Gi 檔案共用。 我們可以藉由執行下列動作來確認：

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

藉由增加欄位來展開 PVC `spec.resources.requests.storage` ：

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

確認 pod 內的 PVC 和檔案系統都顯示新的大小：

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>NFS 檔案共用
[Azure 檔案儲存體現在支援 NFS 4.1 通訊協定](../storage/files/storage-files-how-to-create-nfs-shares.md)。 Azure 檔案儲存體的 NFS 4.1 支援會提供完全受控的 NFS 檔案系統，作為以高可用性和高耐用性分散式彈性儲存平臺為基礎的服務。

 此選項已針對具有就地資料更新的隨機存取工作負載優化，並提供完整的 POSIX 檔案系統支援。 本節說明如何在 AKS 叢集上搭配使用 NFS 共用與 Azure 檔案 CSI 驅動程式。

在預覽階段，請務必檢查 [限制](../storage/files/storage-files-compare-protocols.md#limitations) 和 [區域的可用性](../storage/files/storage-files-compare-protocols.md#regional-availability) 。

### <a name="register-the-allownfsfileshares-preview-feature"></a>註冊 `AllowNfsFileShares` 預覽功能

若要建立利用 NFS 4.1 的檔案共用，您必須 `AllowNfsFileShares` 在您的訂用帳戶上啟用功能旗標。

`AllowNfsFileShares`使用[az feature register][az-feature-register]命令註冊功能旗標，如下列範例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

狀態需要幾分鐘的時間才會顯示「已註冊」。 使用 [az feature list][az-feature-list] 命令來確認註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

當您準備好時，請使用 [az provider register][az-provider-register]命令重新整理 *Microsoft 的儲存體* 資源提供者註冊：

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>建立 NFS 檔案共用的儲存體帳戶

[建立 `Premium_LRS`](../storage/files/storage-how-to-create-premium-fileshare.md)具有下列設定以支援 NFS 共用的 Azure 儲存體帳戶：
- 帳戶種類： FileStorage
- 需要安全傳輸 (僅啟用 HTTPS 流量) ： false
- 在防火牆和虛擬網路中選取代理程式節點的虛擬網路，因此您可能會想要在 MC_ 資源群組中建立儲存體帳戶。

### <a name="create-nfs-file-share-storage-class"></a>建立 NFS 檔案共用儲存類別

`nfs-sc.yaml`使用下面的資訊清單來儲存檔案，以編輯個別的預留位置。

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

編輯並儲存檔案之後，請使用 [kubectl apply][kubectl-apply] 命令來建立儲存類別：

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>使用受 NFS 支援的檔案共用建立部署
您可以[stateful set](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) `data.txt` 使用[kubectl apply][kubectl-apply]命令部署下列命令，以部署將時間戳記儲存至檔案的範例具狀態集：

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

藉由執行下列動作來驗證磁片區的內容：

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> 請注意，由於 NFS 檔案共用是 Premium 帳戶，因此檔案共用大小下限為 100 GB。 如果您建立的 PVC 具有少量的儲存體大小，您可能會遇到「無法建立檔案共用 ...」錯誤大小 (5) ...」。


## <a name="windows-containers"></a>Windows 容器

Azure 檔案儲存體 CSI 驅動程式也支援 Windows 節點和容器。 如果您想要使用 Windows 容器，請遵循 [windows 容器教學](windows-container-cli.md) 課程來新增 windows 節點集區。

當您有 Windows 節點集區之後，請使用內建的儲存類別，例如 `azurefile-csi` 或建立自訂的儲存體類別。 您可以[Windows-based stateful set](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) `data.txt` 使用[kubectl apply][kubectl-apply]命令部署下列命令，以部署將時間戳記儲存至檔案的範例 Windows 型具狀態設定：

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

藉由執行下列動作來驗證磁片區的內容：

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何使用適用于 Azure 磁片的 CSI 驅動程式，請參閱 [使用具有 CSI 驅動程式的 azure 磁片](azure-disk-csi.md)。
- 如需儲存體最佳作法的詳細資訊，請參閱 [Azure Kubernetes Service 中儲存和備份的最佳作法][operator-best-practices-storage]。


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md
