---
title: 透過 SSH 連線至 Azure Kubernetes Service (AKS) 叢集
description: 了解如何使用 Azure Kubernetes Service (AKS) 叢集節點建立 SSH 連線，以進行疑難排解和維護工作。
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: dfdcda40a24142f85bbeb360aacf0971d72d181f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593626"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>使用 SSH 連線到 Azure Kubernetes Service (AKS) 叢集節點以進行維護或疑難排解

在 Azure Kubernetes Service (AKS) 叢集的生命週期中，您可能需要存取 AKS 節點。 此存取可能用於維護、記錄收集，或其他疑難排解作業。 您可以使用 SSH 訪問 AKS 節點，包括 Windows 伺服器節點（當前在 AKS 中預覽）。 您還可以使用[遠端桌面協定 （RDP） 連接連接到 Windows 伺服器節點][aks-windows-rdp]。 出於安全考慮，AKS 節點不會暴露給互聯網。 對於 SSH 至 AKS 節點，您可以使用私人 IP 位址。

本文會示範如何使用私人 IP 位址以 AKS 節點建立 SSH 連線。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

預設情況下，在創建 AKS 群集時獲取或生成 SSH 金鑰，然後添加到節點。 本文介紹如何指定與創建 AKS 群集時使用的 SSH 金鑰不同的 SSH 金鑰。 本文還介紹了如何確定節點的私人 IP 位址並使用 SSH 連接到節點。 如果不需要指定其他 SSH 金鑰，則可以跳過將 SSH 公開金鑰添加到節點的步驟。

本文還假定您有一個 SSH 金鑰。 您可以使用[macOS 或 Linux 或][ssh-nix] [Windows][ssh-windows]創建 SSH 金鑰。 如果使用 PuTTY Gen 創建金鑰組，請以 OpenSSH 格式保存金鑰組，而不是預設的 PuTTy 私密金鑰格式 （.ppk 檔）。

您還需要 Azure CLI 版本 2.0.64 或更高版本安裝和配置。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>配置虛擬機器縮放基於集的 AKS 群集，以便進行 SSH 訪問

要配置基於 SSH 訪問的虛擬機器縮放集，請查找群集虛擬機器規模集的名稱，並將 SSH 公開金鑰添加到該規模集。

使用[az aks show][az-aks-show]命令獲取 AKS 群集的資源組名稱，然後使用[az vms 清單][az-vmss-list]命令獲取比例集的名稱。

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

上面的示例將*myAKSGroup* *的*群集資源組的名稱分配給*CLUSTER_RESOURCE_GROUP*。 然後，該示例使用*CLUSTER_RESOURCE_GROUP*列出比例集名稱並將其分配給*SCALE_SET_NAME*。

> [!IMPORTANT]
> 此時，您僅應使用 Azure CLI 更新虛擬機器基於集 AKS 群集的 SSH 金鑰。
> 
> 對於 Linux 節點，SSH 金鑰目前只能使用 Azure CLI 添加。 如果要使用 SSH 連接到 Windows Server 節點，請使用創建 AKS 群集時提供的 SSH 金鑰，並跳過添加 SSH 公開金鑰的下一組命令。 您仍然需要要疑難排解的節點的 IP 位址，這一部分的最終命令中顯示了該位址。 或者，您可以使用[遠端桌面協定 （RDP） 連接連接到 Windows Server 節點][aks-windows-rdp]，而不是使用 SSH。

要將 SSH 鍵添加到虛擬機器縮放集中的節點，請使用 az [vms 擴展集][az-vmss-extension-set]和[az vmss 更新實例][az-vmss-update-instances]命令。

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

上面的示例使用上一個命令中*CLUSTER_RESOURCE_GROUP*和*SCALE_SET_NAME*變數。 上面的示例還使用 **/..ssh/id_rsa.pub*作為 SSH 公開金鑰的位置。

> [!NOTE]
> 根據預設，AKS 節點的使用者名稱是 *azureuser*。

將 SSH 公開金鑰添加到比例集後，可以使用其 IP 位址將 SSH 添加到該比例集中的節點虛擬機器中。 使用[kubectl get 命令][kubectl-get]查看 AKS 叢集節點的私人 IP 位址。

```console
kubectl get nodes -o wide
```

以下示例輸出顯示群集中所有節點（包括 Windows Server 節點）的內部 IP 位址。

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

記錄要疑難排解的節點的內部 IP 位址。

要使用 SSH 訪問節點，請按照[創建 SSH 連接](#create-the-ssh-connection)中的步驟操作。

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>配置基於虛擬機器可用性集的 AKS 群集，以便進行 SSH 訪問

要配置基於虛擬機器可用性集的基於 SSH 訪問的 AKS 群集，請查找群集的 Linux 節點的名稱，並將 SSH 公開金鑰添加到該節點。

使用[az aks show][az-aks-show]命令獲取 AKS 群集的資源組名稱，然後使用[az vm 清單][az-vm-list]命令列出群集的 Linux 節點的虛擬機器名稱。

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

上面的示例將*myAKSGroup* *的*群集資源組的名稱分配給*CLUSTER_RESOURCE_GROUP*。 然後，該示例使用*CLUSTER_RESOURCE_GROUP*列出虛擬機器名稱。 示例輸出顯示虛擬機器的名稱：

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

上面的示例使用*CLUSTER_RESOURCE_GROUP*變數和以前命令中的節點虛擬機器名稱。 上面的示例還使用 **/..ssh/id_rsa.pub*作為 SSH 公開金鑰的位置。 您還可以使用 SSH 公開金鑰的內容，而不是指定路徑。

> [!NOTE]
> 根據預設，AKS 節點的使用者名稱是 *azureuser*。

將 SSH 公開金鑰添加到節點虛擬機器後，可以使用其 IP 位址將 SSH 連接到該虛擬機器。 使用 [az vm list-ip-addresses][az-vm-list-ip-addresses] 命令檢視 AKS 叢集節點的私人 IP 位址。

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

上面的示例使用上一*個*命令中CLUSTER_RESOURCE_GROUP變數集。 下列範例輸出顯示 AKS 節點的私人 IP 位址：

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>建立 SSH 連線

若要建立到 AKS 節點的 SSH 連線，您可以在 AKS 叢集中執行協助程式 Pod。 此協助程式 Pod 為您提供到叢集的 SSH 存取權，以及額外的 SSH 節點存取權。 若要建立和使用此協助程式 Pod，請完成下列步驟：

1. 執行 `debian` 容器映像，並將終端機工作階段與它連結。 您可以使用此容器搭配 AKS 叢集中的任何節點來建立 SSH 工作階段：

    ```console
    kubectl run --generator=run-pod/v1 -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > 如果使用 Windows Server 節點（當前在 AKS 中預覽），請向命令添加一個節點選擇器以在 Linux 節點上安排 Debian 容器：
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. 將終端會話連接到容器後，請使用 安裝`apt-get`SSH 用戶端：

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. 打開未連接到容器的新終端視窗，將專用 SSH 金鑰複製到説明器窗格中。 此私密金鑰用於將 SSH 創建到 AKS 節點中。 

   如有需要，將 *~/.ssh/id_rsa* 變更到私人 SSH 金鑰的位置：

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. 返回到終端會話到容器，更新複製`id_rsa`的專用 SSH 金鑰的許可權，使其為使用者唯讀：

    ```console
    chmod 0600 id_rsa
    ```

1. 創建與 AKS 節點的 SSH 連接。 同樣地，AKS 節點的預設使用者名稱是 *azureuser*。 在 SSH 金鑰首次受到信任時，接受提示以繼續進行連線。 接著會提供您 AKS 節點的 bash 提示：

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

## <a name="next-steps"></a>後續步驟

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
