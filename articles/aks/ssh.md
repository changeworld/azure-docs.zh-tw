---
title: 透過 SSH 連線至 Azure Kubernetes Service (AKS) 叢集
description: 了解如何使用 Azure Kubernetes Service (AKS) 叢集節點建立 SSH 連線，以進行疑難排解和維護工作。
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 50a52584618e505aa2ae7bd9ed7e0a9f6bc330a9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87015607"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>使用 SSH 連線到 Azure Kubernetes Service (AKS) 叢集節點以進行維護或疑難排解

在 Azure Kubernetes Service (AKS) 叢集的生命週期中，您可能需要存取 AKS 節點。 此存取可能用於維護、記錄收集，或其他疑難排解作業。 您可以使用 SSH 來存取 AKS 節點，包括 Windows Server 節點。 您也可以[使用遠端桌面通訊協定（RDP）連線來連接到 Windows Server 節點][aks-windows-rdp]。 基於安全考慮，AKS 節點不會公開至網際網路。 對於 SSH 至 AKS 節點，您可以使用私人 IP 位址。

本文會示範如何使用私人 IP 位址以 AKS 節點建立 SSH 連線。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

根據預設，當您建立 AKS 叢集時，會取得或產生 SSH 金鑰，然後將其新增至節點。 本文說明如何指定不同的 SSH 金鑰，而不是您建立 AKS 叢集時所使用的 SSH 金鑰。 本文也會示範如何判斷您節點的私人 IP 位址，並使用 SSH 連線到它。 如果您不需要指定不同的 SSH 金鑰，則可以略過將 SSH 公開金鑰新增至節點的步驟。

本文也假設您有 SSH 金鑰。 您可以使用[macOS 或 Linux][ssh-nix]或[Windows][ssh-windows]建立 SSH 金鑰。 如果您使用 PuTTY Gen 來建立金鑰組，請將金鑰組儲存為 OpenSSH 格式，而不是預設的 PuTTy 私密金鑰格式（. .ppk 檔案）。

您也需要安裝並設定 Azure CLI 版本2.0.64 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>設定以虛擬機器擴展集為基礎的 AKS 叢集以進行 SSH 存取

若要設定以 SSH 存取為基礎的虛擬機器擴展集，請尋找叢集虛擬機器擴展集的名稱，並將您的 SSH 公開金鑰新增至該擴展集。

使用[az aks show][az-aks-show]命令來取得 aks 叢集的資源組名，然後按[az vmss list][az-vmss-list]命令來取得您擴展集的名稱。

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

上述範例會將*myResourceGroup*中*myAKSCluster*的叢集資源組名指派給*CLUSTER_RESOURCE_GROUP*。 然後，此範例會使用*CLUSTER_RESOURCE_GROUP*來列出擴展集名稱，並將它指派給*SCALE_SET_NAME*。

> [!IMPORTANT]
> 此時，您應該只使用 Azure CLI 來更新虛擬機器擴展集型 AKS 叢集的 SSH 金鑰。
> 
> 針對 Linux 節點，目前只能使用 Azure CLI 來新增 SSH 金鑰。 如果您想要使用 SSH 連線到 Windows Server 節點，請使用您在建立 AKS 叢集時所提供的 SSH 金鑰，並略過下一組用來新增 SSH 公開金鑰的命令。 您仍然需要您想要疑難排解之節點的 IP 位址，這會顯示在本節的最後一個命令中。 或者，您可以[使用遠端桌面通訊協定（RDP）連線來連線到 Windows Server 節點][aks-windows-rdp]，而不是使用 SSH 連接。

若要將您的 SSH 金鑰新增至虛擬機器擴展集中的節點，請使用[az vmss extension set][az-vmss-extension-set]和[az vmss update-][az-vmss-update-instances] instance 命令。

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

上述範例會使用上述命令中的*CLUSTER_RESOURCE_GROUP*和*SCALE_SET_NAME*變數。 上述範例也使用 *~/.ssh/id_rsa pub*作為 ssh 公開金鑰的位置。

> [!NOTE]
> 根據預設，AKS 節點的使用者名稱是 *azureuser*。

將 SSH 公開金鑰新增至擴展集之後，您可以使用其 IP 位址透過 SSH 連線到該擴展集內的節點虛擬機器。 使用[kubectl get 命令][kubectl-get]來查看 AKS 叢集節點的私人 IP 位址。

```console
kubectl get nodes -o wide
```

下列範例輸出顯示叢集中所有節點的內部 IP 位址，包括 Windows Server 節點。

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

記錄您想要疑難排解之節點的內部 IP 位址。

若要使用 SSH 來存取您的節點，請遵循[建立 ssh](#create-the-ssh-connection)連線中的步驟。

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>設定以虛擬機器可用性集合為基礎的 AKS 叢集以進行 SSH 存取

若要針對 SSH 存取設定以虛擬機器可用性集合為基礎的 AKS 叢集，請尋找叢集 Linux 節點的名稱，並將您的 SSH 公開金鑰新增至該節點。

使用[az aks show][az-aks-show]命令來取得 aks 叢集的資源組名，然後按[az vm list][az-vm-list]命令來列出叢集 Linux 節點的虛擬機器名稱。

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

上述範例會將*myResourceGroup*中*myAKSCluster*的叢集資源組名指派給*CLUSTER_RESOURCE_GROUP*。 然後，此範例會使用*CLUSTER_RESOURCE_GROUP*來列出虛擬機器名稱。 範例輸出會顯示虛擬機器的名稱：

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

若要將 SSH 金鑰新增至節點，請使用 [az vm user update][az-vm-user-update] 命令。

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

上述範例會使用先前命令中的*CLUSTER_RESOURCE_GROUP*變數和 node 虛擬機器名稱。 上述範例也使用 *~/.ssh/id_rsa pub*作為 ssh 公開金鑰的位置。 您也可以使用 SSH 公用金鑰的內容，而不是指定路徑。

> [!NOTE]
> 根據預設，AKS 節點的使用者名稱是 *azureuser*。

將 SSH 公開金鑰新增至 node 虛擬機器之後，您可以使用其 IP 位址透過 SSH 連線到該虛擬機器。 使用 [az vm list-ip-addresses][az-vm-list-ip-addresses] 命令檢視 AKS 叢集節點的私人 IP 位址。

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

上述範例使用先前命令中所設定的*CLUSTER_RESOURCE_GROUP*變數。 下列範例輸出顯示 AKS 節點的私人 IP 位址：

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>建立 SSH 連線

若要建立到 AKS 節點的 SSH 連線，您可以在 AKS 叢集中執行協助程式 Pod。 此協助程式 Pod 為您提供到叢集的 SSH 存取權，以及額外的 SSH 節點存取權。 若要建立和使用此協助程式 Pod，請完成下列步驟：

1. 執行 `debian` 容器映像，並將終端機工作階段與它連結。 您可以使用此容器搭配 AKS 叢集中的任何節點來建立 SSH 工作階段：

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > 如果您使用 Windows Server 節點，請將節點選取器新增至命令，以排程 Linux 節點上的 Debian 容器：
    >
    > ```console
    > kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"v1","spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}'
    > ```

1. 終端機會話連線到容器之後，請使用下列程式來安裝 SSH 用戶端 `apt-get` ：

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. 開啟新的終端機視窗，而不是連線到您的容器，將您的私人 SSH 金鑰複製到 helper pod。 此私密金鑰是用來在 AKS 節點中建立 SSH。 

   如有需要，將 *~/.ssh/id_rsa* 變更到私人 SSH 金鑰的位置：

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. 返回您容器的終端機會話，並更新 `id_rsa` 已複製之私人 SSH 金鑰的許可權，讓它成為使用者唯讀：

    ```console
    chmod 0600 id_rsa
    ```

1. 建立連至您 AKS 節點的 SSH 連線。 同樣地，AKS 節點的預設使用者名稱是 *azureuser*。 在 SSH 金鑰首次受到信任時，接受提示以繼續進行連線。 接著會提供您 AKS 節點的 bash 提示：

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>移除 SSH 存取

完成時，`exit` SSH 工作階段，然後 `exit` 互動式容器工作階段。 這個容器工作階段關閉時，會刪除用來從 AKS 叢集存取 SSH 的 Pod。

## <a name="next-steps"></a>接下來的步驟

如需其他疑難排解資料，您可以[檢視 kubelet 記錄][view-kubelet-logs]或[檢視 Kubernetes 主要節點記錄][view-master-logs]。

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
