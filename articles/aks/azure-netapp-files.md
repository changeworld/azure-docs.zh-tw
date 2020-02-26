---
title: 整合 Azure NetApp Files 與 Azure Kubernetes Service
description: 瞭解如何整合 Azure NetApp Files 與 Azure Kubernetes Service
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 42985e57d63c01553532928b2ba04ed5ee3dd8fb
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596635"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>整合 Azure NetApp Files 與 Azure Kubernetes Service

[Azure NetApp Files][anf]是在 azure 上執行的企業級、高效能、計量付費檔案儲存體服務。 本文說明如何整合 Azure NetApp Files 與 Azure Kubernetes Service （AKS）。

## <a name="before-you-begin"></a>開始之前
此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

> [!IMPORTANT]
> 您的 AKS 叢集也必須[位於支援 Azure NetApp Files 的區域中][anf-regions]。

您也需要安裝並設定 Azure CLI 版本2.0.59 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

### <a name="limitations"></a>限制

當您使用 Azure NetApp Files 時，適用下列限制：

* Azure NetApp Files 僅適用[于選取的 azure 區域][anf-regions]。
* 您必須先獲得 Azure NetApp Files 服務的存取權，才可以使用 Azure NetApp Files。 若要申請存取，您可以使用[Azure NetApp Files 等候清單提交表單][anf-waitlist]。 在您收到來自 Azure NetApp Files 小組的官方確認電子郵件之前，您無法存取 Azure NetApp Files 服務。
* 您的 Azure NetApp Files 服務必須建立在與您的 AKS 叢集相同的虛擬網路中。
* 初始部署 AKS 叢集之後，只支援 Azure NetApp Files 的靜態布建。
* 若要搭配使用動態布建與 Azure NetApp Files，請安裝並設定[NetApp Trident](https://netapp-trident.readthedocs.io/) 19.07 版或更新版本。

## <a name="configure-azure-netapp-files"></a>設定 Azure NetApp Files

> [!IMPORTANT]
> 您必須先完成訂用帳戶的[Azure NetApp Files 等候清單提交表單][anf-waitlist]，才能註冊*Microsoft netapp*資源提供者。 您必須從 Azure NetApp Files 小組收到官方確認電子郵件，才能註冊資源提供。

註冊*Microsoft NetApp*資源提供者：

```azure-cli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> 這需要一些時間才會完成。

當您建立 Azure NetApp 帳戶以與 AKS 搭配使用時，您必須在**節點**資源群組中建立帳戶。 首先，使用[az aks show][az-aks-show]命令取得資源組名，並加入 `--query nodeResourceGroup` 查詢參數。 下列範例會在資源組名*myResourceGroup*中，取得名為*myAKSCluster*之 AKS 叢集的節點資源群組：

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

使用[az netappfiles account create][az-netappfiles-account-create]，在**node**資源群組和與您的 AKS 叢集相同的區域中建立 Azure NetApp Files 帳戶。 下列範例會在*MC_myResourceGroup_myAKSCluster_eastus*資源群組和*eastus*區域中建立名為*myaccount1*的帳戶：

```azure-cli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

使用[az netappfiles pool create][az-netappfiles-pool-create]建立新的容量集區。 下列範例會建立名為*mypool1*的新容量集區，大小和*Premium*服務層級為 4 TB：

```azure-cli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

使用[az network vnet subnet create][az-network-vnet-subnet-create]建立要[委派給 Azure NetApp Files][anf-delegate-subnet]的子網。 *此子網必須位於與您的 AKS 叢集相同的虛擬網路中。*

```azure-cli
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

使用[az netappfiles volume create][az-netappfiles-volume-create]來建立磁片區。

```azure-cli
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

## <a name="create-the-persistentvolume"></a>建立 PersistentVolume

使用[az netappfiles volume show][az-netappfiles-volume-show]列出磁片區的詳細資料
```azure-cli
$ az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"

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

建立 `pv-nfs.yaml` 定義 PersistentVolume。 將 `path` 取代為上一個命令中具有*ipAddress*的*creationToken*和 `server`。 例如：

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

將*伺服器*和*路徑*更新為您在上一個步驟中建立的 NFS （網路檔案系統）磁片區的值。 使用[kubectl apply][kubectl-apply]命令來建立 PersistentVolume：

```console
kubectl apply -f pv-nfs.yaml
```

使用 [ [kubectl 描述][kubectl-describe]] 命令，確認 PersistentVolume 的*狀態*是否*可用*：

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>建立 PersistentVolumeClaim

建立 `pvc-nfs.yaml` 定義 PersistentVolume。 例如：

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

使用[kubectl apply][kubectl-apply]命令來建立 PersistentVolumeClaim：

```console
kubectl apply -f pvc-nfs.yaml
```

使用[kubectl 描述][kubectl-describe]*命令，確認*PersistentVolumeClaim 的*狀態*是否已系結：

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>使用 pod 掛接

建立 `nginx-nfs.yaml`，定義使用 PersistentVolumeClaim 的 pod。 例如：

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

使用[kubectl apply][kubectl-apply]命令建立 pod：

```console
kubectl apply -f nginx-nfs.yaml
```

使用[kubectl 描述][kubectl-describe]命令確認*pod 正在執行*：

```console
kubectl describe pod nginx-nfs
```

使用[kubectl exec][kubectl-exec]來連線至 pod，以確認您的磁片區已掛接在 pod 中，然後 `df -h` 檢查是否已掛接該磁片區。

```console
$ kubectl exec -it nginx-nfs -- bash

root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>後續步驟

如需 Azure NetApp Files 的詳細資訊，請參閱[什麼是 Azure Netapp files][anf]。 如需搭配使用 NFS 與 AKS 的詳細資訊，請參閱[手動建立和使用具有 Azure Kubernetes Service （AKS）的 nfs （網路檔案系統） Linux 伺服器磁片][aks-nfs]區。


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
