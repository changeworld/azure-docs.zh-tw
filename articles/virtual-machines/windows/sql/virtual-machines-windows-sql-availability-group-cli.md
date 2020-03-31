---
title: 配置可用性組（Azure CLI）
description: 使用 Azure CLI 在 Azure 中的 SQL Server VM 上創建 Windows 容錯移轉叢集、可用性組攔截器和內部負載等化器。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022357"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>使用 Azure CLI 為 Azure VM 上的 SQL Server 配置始終打開的可用性組
本文介紹如何使用[Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/)部署 Windows 容錯移轉叢集、向群集添加 SQL Server VM 以及為"始終打開"可用性組創建內部負載等化器和攔截器。 始終處於打開的可用性組的部署仍通過 SQL 伺服器管理工作室 （SSMS） 手動完成。 

## <a name="prerequisites"></a>Prerequisites
要使用 Azure CLI 自動設置"始終打開"可用性組，必須具備以下先決條件： 
- [Azure 訂閱](https://azure.microsoft.com/free/)。
- 具有網域控制站的資源群組。 
- Azure 中的一個或多個域加入[的 VM 運行 SQL Server 2016（或更高版本）中的](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)*同一可用性集或*[已註冊到 SQL VM 資來源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)的不同可用性區域。  
- [Azure CLI](/cli/azure/install-azure-cli)。 
- 兩個可用的（任何實體不使用）IP 位址。 一個用於內部負載平衡器。 另一個用於可用性組與可用性組位於同一子網中的可用性組攔截器。 如果使用現有負載等化器，則可用性組攔截器只需要一個可用的 IP 位址。 

## <a name="permissions"></a>權限
使用 Azure CLI 配置"始終打開"可用性組需要以下帳戶許可權： 

- 在域中具有 **"創建電腦物件"** 許可權的現有域使用者帳戶。 例如，域管理員帳戶通常具有足夠的許可權（例如： account@domain.com _此帳戶也應該屬於建立叢集的每個 VM 上的本機系統管理員群組一部分。_
- 控制 SQL Server 服務的域使用者帳戶。 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>第 1 步：創建存儲帳戶作為雲見證
群集需要一個存儲帳戶來充當雲見證人。 您可以使用任何現有的存儲帳戶，也可以創建新的存儲帳戶。 如果要使用現有存儲帳戶，請跳到下一節。 

以下程式碼片段創建存儲帳戶： 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> 如果使用過時的 Azure `az sql: 'vm' is not in the 'az sql' command group` CLI 版本，可能會看到錯誤。 下載[最新版本的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)以克服此錯誤。

## <a name="step-2-define-windows-failover-cluster-metadata"></a>第 2 步：定義 Windows 容錯移轉叢集中繼資料
Azure CLI [az sql vm 組](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest)命令組管理承載可用性組的 Windows 伺服器容錯移轉叢集 （WSFC） 服務的中繼資料。 群集中繼資料包括活動目錄域、群集帳戶、用作雲見證的存儲帳戶和 SQL Server 版本。 使用[az sql vm 組創建](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create)來定義 WSFC 的中繼資料，以便在添加第一個 SQL Server VM 時，群集創建為定義。 

以下程式碼片段定義群集的中繼資料：
```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>步驟 3：將 SQL Server VM 添加到群集
將第一個 SQL Server VM 添加到群集將創建群集。 [az sql vm 添加到組](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group)命令創建具有以前給定名稱的群集，在 SQL Server VM 上安裝群集角色，並將它們添加到群集。 命令的`az sql vm add-to-group`後續使用將更多的 SQL Server VM 添加到新創建的群集中。 

以下程式碼片段創建群集並將第一個 SQL Server VM 添加到群集： 

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
使用此命令向群集添加任何其他 SQL Server VM。 僅修改`-n`SQL Server VM 名稱的參數。 

## <a name="step-4-create-the-availability-group"></a>第 4 步：創建可用性組
通過使用[SQL 伺服器管理工作室](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)[、PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)或[Transact-SQL，](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)按照通常方式手動創建可用性組。 

>[!IMPORTANT]
> 此時*不要*創建攔截器，因為此操作通過以下部分中的 Azure CLI 完成。  

## <a name="step-5-create-the-internal-load-balancer"></a>第 5 步：創建內部負載等化器

始終打開可用性組攔截器需要 Azure 負載等化器的內部實例。 內部負載等化器為可用性組攔截器提供"浮動"IP 位址，以便更快地進行容錯移轉和重新連接。 如果可用性組中的 SQL Server VM 屬於同一可用性集的一部分，則可以使用基本負載等化器。 否則，您需要使用標準負載等化器。  

> [!NOTE]
> 內部負載等化器應與 SQL Server VM 實例位於同一虛擬網路中。 

以下程式碼片段創建內部負載等化器：

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> 每個 SQL Server VM 的公共 IP 資源應具有與標準負載等化器相容的標準 SKU。 要確定 VM 公共 IP 資源的 SKU，請轉到**資源組**，為所需的 SQL Server VM 選擇**公共 IP 位址**資源，並在 **"概述"** 窗格中查找**SKU**下的值。  

## <a name="step-6-create-the-availability-group-listener"></a>步驟 6：創建可用性組攔截器
手動創建可用性組後，可以使用[az sql vm ag-攔截器](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create)創建攔截器。 

*子網資源識別碼*是`/subnets/<subnetname>`追加到虛擬網路資源的資源識別碼 的值。 要標識子網資源識別碼，
   1. 轉到[Azure 門戶](https://portal.azure.com)中的資源組。 
   1. 選擇虛擬網路資源。 
   1. 在 **"設置"** 窗格中選擇 **"屬性**"。 
   1. 標識虛擬網路的資源識別碼，並將其追加`/subnets/<subnetname>`到虛擬網路的末尾以創建子網資源識別碼。 例如：
      - 您的虛擬網路資源識別碼 是：`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - 您的子網名稱為：`default`
      - 因此，您的子網資源識別碼 是：`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


以下程式碼片段創建可用性組攔截器：

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>修改可用性組中的副本數
將可用性組部署到 Azure 中託管的 SQL Server VM 時，會增加複雜性層。 資來源提供者和虛擬機器組現在管理資源。 因此，當您在可用性組中添加或刪除副本時，還有一個附加步驟，即使用有關 SQL Server VM 的資訊更新攔截器中繼資料。 修改可用性組中的副本數時，還必須使用[az sql vm 組 ag-攔截器更新](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update)命令使用 SQL Server VM 的中繼資料更新攔截器。 


### <a name="add-a-replica"></a>新增複本

要向可用性組添加新副本，可以：

1. 將 SQL Server VM 添加到群集：
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. 使用 SQL 伺服器管理工作室將 SQL Server 實例添加為可用性組中的副本。
1. 將 SQL Server VM 中繼資料添加到攔截器：
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>移除複本

要從可用性組中刪除副本，請：

1. 使用 SQL 伺服器管理工作室從可用性組中刪除副本。 
1. 從攔截器中刪除 SQL Server VM 中繼資料：
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. 從群集中刪除 SQL Server VM：
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>刪除可用性組攔截器
如果以後需要刪除使用 Azure CLI 配置的可用性組攔截器，則必須通過 SQL VM 資來源提供者。 由於攔截器是通過 SQL VM 資來源提供者註冊的，因此只需通過 SQL 伺服器管理工作室刪除攔截器是不夠的。 

最好的方法是通過使用 Azure CLI 中的以下程式碼片段通過 SQL VM 資來源提供者將其刪除。 這樣做會從 SQL VM 資來源提供者中刪除可用性組攔截器中繼資料。 它還從可用性組物理上刪除攔截器。 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [SQL 伺服器 VM 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL 伺服器 VM 的常見問題解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL 伺服器 VM 的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [切換 SQL Server VM 的授權模式](virtual-machines-windows-sql-ahb.md)
* [始終打開可用性組&#40;SQL Server&#41;概述](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [&#40;SQL 伺服器&#41;，為始終處於可用性組佈建服務器實例](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [管理可用性組&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [監視 &#40;SQL Server&#41;的可用性組](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [始終打開可用性組&#40;SQL 伺服器&#41;的 Transact-SQL 語句概述](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [始終打開可用性組&#40;SQL Server&#41;的 PowerShell Cmdlet 概述](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
