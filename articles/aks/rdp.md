---
title: AKS Windows Server 節點的 RDP
titleSuffix: Azure Kubernetes Service
description: 瞭解如何使用 Azure Kubernetes Service (AKS) 叢集 Windows Server 節點來建立 RDP 連線，以進行疑難排解和維護工作。
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: c774e2287d0540c73cdd2234843d6766e7f2fb91
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95792259"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>使用 RDP 連線至 Azure Kubernetes Service (AKS) 叢集 Windows Server 節點進行維護或疑難排解

在 Azure Kubernetes Service (AKS) 叢集的整個生命週期中，您可能需要存取 AKS Windows Server 節點。 此存取可能用於維護、記錄收集，或其他疑難排解作業。 您可以使用 RDP 存取 AKS Windows 伺服器節點。 或者，如果您想要使用 SSH 來存取 AKS Windows Server 節點，而且您可以存取在叢集建立期間使用的相同金鑰組，您可以依照 SSH 中的步驟 [Azure Kubernetes Service (AKS) 叢集節點][ssh-steps]。 基於安全考量，AKS 節點不會公開至網際網路。

本文說明如何使用其私人 IP 位址，建立與 AKS 節點的 RDP 連線。

## <a name="before-you-begin"></a>開始之前

本文假設您已有包含 Windows Server 節點的現有 AKS 叢集。 如果您需要 AKS 叢集，請參閱 [使用 Azure CLI 建立具有 Windows 容器之 AKS][aks-windows-cli]叢集的文章。 您需要 windows 系統管理員使用者名稱和密碼，才能進行要疑難排解的 Windows Server 節點。 如果您不知道它們，可以依照 [重設遠端桌面服務或其在 WINDOWS VM 中的系統管理員密碼 ](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/reset-rdp)進行重設。 您也需要 RDP 用戶端（例如 [Microsoft 遠端桌面][rdp-mac]）。

您也需要安裝並設定 Azure CLI 2.0.61 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>將虛擬機器部署至與叢集相同的子網

AKS 叢集的 Windows Server 節點沒有可從外部存取的 IP 位址。 若要進行 RDP 連線，您可以將具有可公開存取之 IP 位址的虛擬機器部署至與 Windows Server 節點相同的子網。

下列範例會在 *myResourceGroup* 資源群組中建立名為 *myVM* 的虛擬機器。

首先，取得 Windows Server 節點集區所使用的子網。 若要取得子網識別碼，您需要子網的名稱。 若要取得子網的名稱，您需要 vnet 的名稱。 藉由查詢叢集的網路清單來取得 vnet 名稱。 若要查詢叢集，您需要其名稱。 您可以在 Azure Cloud Shell 中執行下列各項，以取得上述各項：

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

現在您已有 SUBNET_ID，請在相同的 Azure Cloud Shell 視窗中執行下列命令，以建立 VM：

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

下列範例輸出顯示已成功建立 VM，並顯示虛擬機器的公用 IP 位址。

```console
13.62.204.18
```

記錄虛擬機器的公用 IP 位址。 您將在稍後的步驟中使用此位址。

## <a name="allow-access-to-the-virtual-machine"></a>允許存取虛擬機器

AKS 節點集區子網預設會受到 Nsg (網路安全性群組的保護，預設為) 。 若要取得虛擬機器的存取權，您必須在 NSG 中啟用存取權。

> [!NOTE]
> Nsg 由 AKS 服務控制。 控制平面隨時都會覆寫您對 NSG 所做的任何變更。
>

首先，取得 nsg 的資源群組和 nsg 名稱，以將規則新增至：

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

然後，建立 NSG 規則：

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>取得節點位址

若要管理 Kubernetes 叢集，請使用 Kubernetes 命令列用戶端：[kubectl][kubectl]。 如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 若要在本機安裝 `kubectl`，請使用 [az aks install-cli][az-aks-install-cli] 命令：
    
```azurecli-interactive
az aks install-cli
```

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

使用 [kubectl get][kubectl-get] 命令列出 Windows Server 節點的內部 IP 位址：

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

記錄您要進行疑難排解之 Windows Server 節點的內部 IP 位址。 您將在稍後的步驟中使用此位址。

## <a name="connect-to-the-virtual-machine-and-node"></a>連接至虛擬機器和節點

使用 RDP 用戶端（例如 [Microsoft 遠端桌面][rdp-mac]）連接到您稍早建立之虛擬機器的公用 IP 位址。

![使用 RDP 用戶端連接到虛擬機器的影像](media/rdp/vm-rdp.png)

連接到您的虛擬機器之後，請連線到您想要在虛擬機器中使用 RDP 用戶端進行疑難排解之 Windows Server 節點的 *內部 IP 位址* 。

![使用 RDP 用戶端連接到 Windows Server 節點的影像](media/rdp/node-rdp.png)

您現在已連線到您的 Windows Server 節點。

![Windows Server 節點中 cmd 視窗的影像](media/rdp/node-session.png)

您現在可以在 *cmd* 視窗中執行任何疑難排解命令。 由於 Windows Server 節點使用 Windows Server Core，因此當您透過 RDP 連接到 Windows Server 節點時，並沒有完整的 GUI 或其他 GUI 工具。

## <a name="remove-rdp-access"></a>移除 RDP 存取

完成時，請結束 Windows Server 節點的 RDP 連線，然後結束虛擬機器的 RDP 會話。 當您結束這兩個 RDP 會話之後，請使用 [az vm delete][az-vm-delete] 命令刪除虛擬機器：

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

NSG 規則：

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>後續步驟

如果您需要額外的疑難排解資料，可以 [查看 Kubernetes 主要節點記錄][view-master-logs] 或 [Azure 監視器][azure-monitor-containers]。

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
