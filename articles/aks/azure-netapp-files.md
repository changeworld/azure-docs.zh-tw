---
title: 將 Azure NetApp 檔與 Azure 庫伯奈斯服務集成
description: 瞭解如何將 Azure NetApp 檔與 Azure 庫伯奈斯服務集成
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 1c4996df66d475c63110e3d2797f55598fd85b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273758"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>將 Azure NetApp 檔與 Azure 庫伯奈斯服務集成

[Azure NetApp 檔][anf]是在 Azure 上運行的企業級高性能、按流量計費的檔存儲服務。 本文介紹如何將 Azure NetApp 檔與 Azure 庫伯奈斯服務 （AKS） 集成。

## <a name="before-you-begin"></a>開始之前
此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

> [!IMPORTANT]
> AKS 群集還必須[位於支援 Azure NetApp 檔的區域][anf-regions]。

您還需要 Azure CLI 版本 2.0.59 或更高版本安裝和配置。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

### <a name="limitations"></a>限制

使用 Azure NetApp 檔時，以下限制適用：

* Azure NetApp 檔僅在[選定的 Azure 區域][anf-regions]中可用。
* 在使用 Azure NetApp 檔之前，必須授予對 Azure NetApp 檔服務的存取權限。 要申請訪問，可以使用[Azure NetApp 檔等待清單提交表單][anf-waitlist]。 在收到 Azure NetApp 檔團隊的官方確認電子郵件之前，無法訪問 Azure NetApp 檔服務。
* Azure NetApp 檔服務必須在與 AKS 群集相同的虛擬網路中創建。
* 在初始部署 AKS 群集後，僅支援 Azure NetApp 檔的靜態預配。
* 要將動態預配與 Azure NetApp 檔一起使用，請安裝和配置[NetApp Trident](https://netapp-trident.readthedocs.io/)版本 19.07 或更高版本。

## <a name="configure-azure-netapp-files"></a>配置 Azure NetApp 檔

> [!IMPORTANT]
> 必須先完成訂閱的[Azure NetApp 檔等待清單提交表單][anf-waitlist]，然後才能註冊*Microsoft.NetApp*資來源提供者。 在收到 Azure NetApp 檔團隊的官方確認電子郵件之前，無法註冊資源提供。

註冊*Microsoft.NetApp*資源供應商：

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> 這需要一些時間才會完成。

創建用於 AKS 的 Azure NetApp 帳戶時，需要在**節點**資源組中創建該帳戶。 首先，使用 [az aks show][az-aks-show] 命令來取得資源群組名稱，並新增 `--query nodeResourceGroup` 查詢參數。 下面的示例獲取名為 myAKSCluster 的資源組名為*myAKSCluster*的 AKS 群集的節點資源組，該組名為*myResourceGroup*：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

使用[az netapp 檔帳戶][az-netappfiles-account-create]創建在**節點**資源組中創建 Azure NetApp 檔帳戶，並使用 與 AKS 群集相同的區域。 以下示例在*MC_myResourceGroup_myAKSCluster_eastus*資源組和東部區域中創建名為*myaccount1* *的*帳戶：

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

使用[az netapp檔池創建][az-netappfiles-pool-create]創建新的容量池。 下面的示例創建一個名為*mypool1*的新容量池，其大小和*高級*服務等級為 4 TB：

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

創建子網以使用[az 網路 vnet 子網創建][az-network-vnet-subnet-create][將委託給 Azure NetApp 檔][anf-delegate-subnet]。 *此子網必須與 AKS 群集位於同一虛擬網路中。*

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

使用[az netappfile 卷創建創建卷][az-netappfiles-volume-create]。

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
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

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
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>創建持久卷

使用[az netapp檔卷顯示][az-netappfiles-volume-show]列出卷的詳細資訊

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

創建定義`pv-nfs.yaml`持久卷。 替換為`path`*創建權杖*，然後`server`替換為上一個命令中的*ipAddress。* 例如：

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
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

更新*伺服器*和*路徑*，以訪問在上一步驟中創建的 NFS（網路檔案系統）卷的值。 使用[kubectl 應用][kubectl-apply]命令創建持久卷：

```console
kubectl apply -f pv-nfs.yaml
```

使用[kubectl 描述][kubectl-describe]命令驗證持久卷*的狀態**可用*：

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>創建持久卷索賠

創建定義`pvc-nfs.yaml`持久卷。 例如：

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

使用[kubectl 應用][kubectl-apply]命令創建持久卷聲明：

```console
kubectl apply -f pvc-nfs.yaml
```

*使用*[kubectl 描述][kubectl-describe]命令驗證持久卷聲明*的狀態*：

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>隨吊艙安裝

創建`nginx-nfs.yaml`使用持久卷聲明的窗格定義。 例如：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
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

使用[kubectl 應用][kubectl-apply]命令創建 pod：

```console
kubectl apply -f nginx-nfs.yaml
```

使用[kubectl 描述][kubectl-describe]命令驗證窗格*正在運行*：

```console
kubectl describe pod nginx-nfs
```

使用[kubectl exec][kubectl-exec]連接到 pod，然後`df -h`檢查卷是否已安裝，從而驗證卷已安裝在窗格中。

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>後續步驟

有關 Azure NetApp 檔的詳細資訊，請參閱[什麼是 Azure NetApp 檔][anf]。 有關將 NFS 與 AKS 一起使用的詳細資訊，請參閱[手動創建和使用具有 Azure 庫伯奈斯服務 （AKS） 的 NFS（網路檔案系統）Linux 伺服器卷][aks-nfs]。


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
