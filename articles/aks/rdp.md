---
title: RDP 進入 Azure 庫伯奈斯服務 （AKS） 群集 Windows 伺服器節點
description: 瞭解如何使用 Azure Kubernetes 服務 （AKS） 群集 Windows 伺服器節點創建 RDP 連接，以便執行故障排除和維護任務。
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 897504aa9902d0feaf4245c719d3a4a3c6fd2241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594476"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>使用 RDP 連接到 Azure 庫伯奈斯服務 （AKS） 群集 Windows 伺服器節點，以便進行維護或故障排除

在 Azure 庫伯內斯服務 （AKS） 群集的整個生命週期中，您可能需要訪問 AKS Windows 伺服器節點。 此存取可能用於維護、記錄收集，或其他疑難排解作業。 您可以使用 RDP 訪問 AKS Windows 伺服器節點。 或者，如果要使用 SSH 訪問 AKS Windows 伺服器節點，並且可以訪問群集創建期間使用的相同金鑰組，則可以按照 SSH 中的步驟[進入 Azure Kubernetes 服務 （AKS） 叢集節點][ssh-steps]。 基於安全考量，AKS 節點不會公開至網際網路。

Windows 伺服器節點支援當前處於 AKS 中的預覽版。

本文介紹如何使用 AKS 節點的私人 IP 位址創建 RDP 連接。

## <a name="before-you-begin"></a>開始之前

本文假定您具有具有 Windows 伺服器節點的現有 AKS 群集。 如果需要 AKS 群集，請參閱有關[使用 Azure CLI 使用 Windows 容器創建 AKS 群集][aks-windows-cli]的文章。 您需要要疑難排解的 Windows 伺服器節點的 Windows 管理員使用者名和密碼。 您還需要一個RDP用戶端，如[微軟遠端桌面][rdp-mac]。

您還需要 Azure CLI 版本 2.0.61 或更高版本安裝和配置。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>將虛擬機器部署到與群集相同的子網

AKS 群集的 Windows 伺服器節點沒有外部可訪問的 IP 位址。 要建立 RDP 連接，可以將具有可公開訪問的 IP 位址的虛擬機器部署到與 Windows Server 節點相同的子網。

下面的示例在*myResourceGroup 資源*組中創建名為*myVM*的虛擬機器。

首先，獲取 Windows Server 節點池使用的子網。 要獲取子網 ID，您需要子網的名稱。 要獲取子網的名稱，您需要 vnet 的名稱。 通過查詢群集的網路清單來獲取 vnet 名稱。 要查詢群集，您需要其名稱。 通過在 Azure 雲外殼中運行以下內容，可以獲取所有這些功能：

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

現在，您已經SUBNET_ID了，請在同一 Azure 雲外殼視窗中運行以下命令以創建 VM：

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

以下示例輸出顯示 VM 已成功創建並顯示虛擬機器的公共 IP 位址。

```console
13.62.204.18
```

記錄虛擬機器的公共 IP 位址。 您將在後面的步驟中使用此位址。

## <a name="allow-access-to-the-virtual-machine"></a>允許訪問虛擬機器

預設情況下，AKS 節點池子網受 NSG（網路安全性群組）保護。 要訪問虛擬機器，您必須在 NSG 中啟用存取權限。

> [!NOTE]
> NSG 由 AKS 服務控制。 您對 NSG 的任何更改將隨時被控制平面覆蓋。
>

首先，獲取 nsg 的資源組和 nsg 名稱，以將規則添加到：

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

然後，創建 NSG 規則：

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>獲取節點位址

若要管理 Kubernetes 叢集，請使用 Kubernetes 命令列用戶端：[kubectl][kubectl]。 如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 若要在本機安裝 `kubectl`，請使用 [az aks install-cli][az-aks-install-cli] 命令：
    
```azurecli-interactive
az aks install-cli
```

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

使用[kubectl get][kubectl-get]命令列出 Windows 伺服器節點的內部 IP 位址：

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

記錄要疑難排解的 Windows 伺服器節點的內部 IP 位址。 您將在後面的步驟中使用此位址。

## <a name="connect-to-the-virtual-machine-and-node"></a>連接到虛擬機器和節點

連接到您之前使用 RDP 用戶端（如[Microsoft 遠端桌面][rdp-mac]）創建的虛擬機器的公共 IP 位址。

![使用 RDP 用戶端連接到虛擬機器的圖像](media/rdp/vm-rdp.png)

連接到虛擬機器後，請連接到要使用虛擬機器內 RDP 用戶端進行故障排除的 Windows Server 節點*的內部 IP 位址*。

![使用 RDP 用戶端連接到 Windows 伺服器節點的圖像](media/rdp/node-rdp.png)

您現在已連接到 Windows 伺服器節點。

![Windows 伺服器節點中的 cmd 視窗圖像](media/rdp/node-session.png)

您現在可以在*cmd*視窗中運行任何故障排除命令。 由於 Windows 伺服器節點使用 Windows 伺服器核心，因此當您通過 RDP 連接到 Windows Server 節點時，沒有完整的 GUI 或其他 GUI 工具。

## <a name="remove-rdp-access"></a>刪除 RDP 存取權限

完成後，退出 RDP 連接到 Windows 伺服器節點，然後將 RDP 會話退出虛擬機器。 退出兩個 RDP 會話後，使用 az [vm 刪除][az-vm-delete]命令刪除虛擬機器：

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

如果需要其他故障排除資料，可以[查看庫伯奈斯主節點日誌][view-master-logs]或[Azure 監視器][azure-monitor-containers]。

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
