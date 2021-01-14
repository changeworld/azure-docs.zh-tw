---
title: 整合 Azure NetApp Files 與 Azure Kubernetes Service
description: 瞭解如何整合 Azure NetApp Files 與 Azure Kubernetes Service
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 19727d3c3322b05f340463d94a2bc3884e5d9d93
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196005"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>整合 Azure NetApp Files 與 Azure Kubernetes Service

[Azure NetApp Files][anf] 是在 azure 上執行的企業級、高效能、計量付費檔案儲存體服務。 本文說明如何整合 Azure NetApp Files 與 Azure Kubernetes Service (AKS) 。

## <a name="before-you-begin"></a>開始之前
此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

> [!IMPORTANT]
> 您的 AKS 叢集也必須 [位於支援 Azure NetApp Files 的區域中][anf-regions]。

您也必須安裝並設定 Azure CLI 2.0.59 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

### <a name="limitations"></a>限制

當您使用 Azure NetApp Files 時，適用下列限制：

* Azure NetApp Files 僅適用 [于所選的 azure 區域][anf-regions]。
* 在您可以使用 Azure NetApp Files 之前，您必須先獲得 Azure NetApp Files 服務的存取權。 若要申請存取權，您可以使用 [Azure NetApp Files 等候清單提交表單][anf-waitlist] 或移至 https://azure.microsoft.com/services/netapp/#getting-started 。 在您收到來自 Azure NetApp Files 團隊的官方確認電子郵件之前，您無法存取 Azure NetApp Files 服務。
* 初始部署 AKS 叢集之後，只支援 Azure NetApp Files 的靜態布建。
* 若要搭配 Azure NetApp Files 使用動態布建，請安裝和設定 [NetApp Trident](https://netapp-trident.readthedocs.io/) 19.07 版或更新版本。

## <a name="configure-azure-netapp-files"></a>設定 Azure NetApp Files

> [!IMPORTANT]
> 註冊  *Microsoft* netapp 資源提供者之前，您必須先完成訂用帳戶的 [Azure NetApp Files 等候清單提交表單][anf-waitlist] 或移至 https://azure.microsoft.com/services/netapp/#getting-started 。 在您收到來自 Azure NetApp Files 團隊的官方確認電子郵件之前，您無法註冊資源提供。

註冊 *Microsoft NetApp* 資源提供者：

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> 這需要一些時間才會完成。

當您建立要與 AKS 搭配使用的 Azure NetApp 帳戶時，您需要在 **node** 資源群組中建立帳戶。 首先，使用 [az aks show][az-aks-show] 命令來取得資源群組名稱，並新增 `--query nodeResourceGroup` 查詢參數。 下列範例會取得資源組名 *myResourceGroup* 中名為 *myAKSCluster* 之 AKS 叢集的節點資源群組：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

使用 [az netappfiles account create][az-netappfiles-account-create]，在 **node** 資源群組和與 AKS 叢集相同的區域中建立 Azure NetApp Files 帳戶。 下列範例會在 *MC_myResourceGroup_myAKSCluster_eastus* 資源群組和 *eastus* 區域中建立名為 *>myaccount1* 的帳戶：

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

使用 [az netappfiles pool create][az-netappfiles-pool-create]建立新的容量集區。 下列範例會建立名為 *>mypool1* 的新容量集區，其大小與 *Premium* 服務層級為 4 TB：

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

使用[az network vnet subnet create][az-network-vnet-subnet-create]來建立子網，以[委派給 Azure NetApp Files][anf-delegate-subnet] 。 *此子網必須位於與您的 AKS 叢集相同的虛擬網路中。*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

使用 [az netappfiles volume create][az-netappfiles-volume-create]來建立磁片區。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>建立 PersistentVolume

使用[az netappfiles volume show][az-netappfiles-volume-show]列出磁片區的詳細資料

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

建立 `pv-nfs.yaml` PersistentVolume 的定義。 請 `path` 以 *creationToken* 取代，並使用 `server` 上一個命令中的 *ipAddress* 來取代。 例如：

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

將 *伺服器* 和 *路徑* 更新為您在上一個步驟中建立的 NFS (網路檔案系統) 磁片區的值。 使用 [kubectl apply][kubectl-apply] 命令建立 PersistentVolume：

```console
kubectl apply -f pv-nfs.yaml
```

使用 [kubectl 描述][kubectl-describe]命令確認 PersistentVolume 的 *狀態* 是否 *可用*：

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>建立 PersistentVolumeClaim

建立 `pvc-nfs.yaml` PersistentVolume 的定義。 例如：

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

使用 [kubectl apply][kubectl-apply] 命令建立 PersistentVolumeClaim：

```console
kubectl apply -f pvc-nfs.yaml
```

使用 [kubectl 描述][kubectl-describe]命令確認 PersistentVolumeClaim 的 *狀態*：

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>使用 pod 掛接

建立 `nginx-nfs.yaml` 定義使用 PersistentVolumeClaim 之 pod 的。 例如：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

使用 [kubectl apply][kubectl-apply] 命令建立 pod：

```console
kubectl apply -f nginx-nfs.yaml
```

使用 [kubectl 描述][kubectl-describe]命令確認 *pod 正在執行*：

```console
kubectl describe pod nginx-nfs
```

使用 [kubectl exec][kubectl-exec] 來連線至 pod，然後檢查是否已掛接磁片區，以確認已將磁片區掛接到 pod 中 `df -h` 。

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>後續步驟

如需 Azure NetApp Files 的詳細資訊，請參閱 [什麼是 Azure Netapp files][anf]。 如需搭配使用 NFS 與 AKS 的詳細資訊，請參閱使用 [Azure Kubernetes Service (AKS) ，手動建立和使用 nfs (網路檔案系統) Linux Server 磁片 ][aks-nfs]區。


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
