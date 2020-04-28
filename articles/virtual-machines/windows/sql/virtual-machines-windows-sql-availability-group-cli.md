---
title: 設定可用性群組（Azure CLI）
description: 使用 Azure CLI 在 Azure 中的 SQL Server VM 上建立 Windows 容錯移轉叢集、可用性群組接聽程式和內部負載平衡器。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74022357"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>使用 Azure CLI 在 Azure VM 上設定 SQL Server 的 Always On 可用性群組
本文說明如何使用[Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/)來部署 Windows 容錯移轉叢集、將 SQL Server vm 新增至叢集，以及建立 Always On 可用性群組的內部負載平衡器和接聽程式。 Always On 可用性群組的部署仍然是透過 SQL Server Management Studio （SSMS）手動完成。 

## <a name="prerequisites"></a>Prerequisites
若要使用 Azure CLI 自動設定 Always On 可用性群組，您必須具備下列必要條件： 
- [Azure 訂](https://azure.microsoft.com/free/)用帳戶。
- 具有網域控制站的資源群組。 
- 在[Azure 中執行 SQL Server 2016 （或更新版本） Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)的一或多個已加入網域的 vm，位於*相同的可用性設定組或*已[向 SQL VM 資源提供者註冊](virtual-machines-windows-sql-register-with-resource-provider.md)的不同可用性區域中。  
- [Azure CLI](/cli/azure/install-azure-cli)。 
- 有兩個可用（未由任何實體使用） IP 位址。 一個適用于內部負載平衡器。 另一個則用於與可用性群組位於相同子網內的可用性群組接聽程式。 如果您要使用現有的負載平衡器，可用性群組接聽程式只需要一個可用的 IP 位址。 

## <a name="permissions"></a>權限
您需要下列帳戶許可權，才能使用 Azure CLI 設定 Always On 可用性群組： 

- 在網域中具有「**建立電腦物件**」許可權的現有網域使用者帳戶。 例如，網域系統管理員帳戶通常具有足夠的許可權（例如： account@domain.com）。 _此帳戶也應該屬於建立叢集的每個 VM 上的本機系統管理員群組一部分。_
- 控制 SQL Server 服務的網域使用者帳戶。 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>步驟1：建立儲存體帳戶作為雲端見證
叢集需要儲存體帳戶作為雲端見證。 您可以使用任何現有的儲存體帳戶，也可以建立新的儲存體帳戶。 如果您想要使用現有的儲存體帳戶，請跳至下一節。 

下列程式碼片段會建立儲存體帳戶： 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> 如果您使用的是`az sql: 'vm' is not in the 'az sql' command group`過期的 Azure CLI 版本，您可能會看到錯誤。 下載[最新版本的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) ，以取得過去這個錯誤。

## <a name="step-2-define-windows-failover-cluster-metadata"></a>步驟2：定義 Windows 容錯移轉叢集中繼資料
Azure CLI [az sql vm group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest)命令群組會管理裝載可用性群組之 Windows Server 容錯移轉叢集（WSFC）服務的中繼資料。 叢集中繼資料包括 Active Directory 網域、叢集帳戶、用來作為雲端見證的儲存體帳戶，以及 SQL Server 版本。 使用[az sql vm group create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create)來定義 WSFC 的中繼資料，如此一來，當第一個 SQL Server vm 新增時，就會依照定義來建立叢集。 

下列程式碼片段會定義叢集的中繼資料：
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>步驟3：將 SQL Server Vm 新增至叢集
將第一個 SQL Server VM 新增至叢集會建立叢集。 [Az sql vm add-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group)命令會使用先前指定的名稱建立叢集、在 SQL Server 的 vm 上安裝叢集角色，並將其新增至叢集。 後續使用`az sql vm add-to-group`命令會將更多 SQL Server vm 新增至新建立的叢集。 

下列程式碼片段會建立叢集，並在其中新增第一個 SQL Server VM： 

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
使用此命令，將任何其他 SQL Server Vm 新增至叢集。 只修改 SQL Server `-n` VM 名稱的參數。 

## <a name="step-4-create-the-availability-group"></a>步驟4：建立可用性群組
如往常般手動建立可用性群組，方法是使用[SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)、 [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)或[transact-sql](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)。 

>[!IMPORTANT]
> 此時*請勿建立接聽*程式，因為這是透過下列各節中的 Azure CLI 完成。  

## <a name="step-5-create-the-internal-load-balancer"></a>步驟5：建立內部負載平衡器

Always On 可用性群組接聽程式需要 Azure Load Balancer 的內部實例。 「內部負載平衡器」提供可用性群組接聽程式的「浮動」 IP 位址，可讓您更快速地進行容錯移轉和重新連接。 如果可用性群組中的 SQL Server Vm 屬於相同的可用性設定組，您可以使用基本負載平衡器。 否則，您必須使用標準負載平衡器。  

> [!NOTE]
> 內部負載平衡器應該與 SQL Server VM 實例位於相同的虛擬網路中。 

下列程式碼片段會建立內部負載平衡器：

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> 每個 SQL Server VM 的公用 IP 資源都應該要有標準 SKU，才能與標準負載平衡器相容。 若要判斷 VM 公用 IP 資源的 SKU，請移至 [**資源群組**]，選取所需 SQL Server VM 的**公用 IP 位址**資源，並在 [**總覽**] 窗格中的 [ **SKU** ] 下找出值。  

## <a name="step-6-create-the-availability-group-listener"></a>步驟6：建立可用性群組接聽程式
在您手動建立可用性群組之後，您可以使用[az sql vm ag-接聽程式](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create)來建立接聽程式。 

*子網資源識別碼*是`/subnets/<subnetname>`附加至虛擬網路資源的資源識別碼的值。 若要識別子網資源識別碼：
   1. 移至[Azure 入口網站](https://portal.azure.com)中的資源群組。 
   1. 選取虛擬網路資源。 
   1. 在 [**設定**] 窗格中選取 [**屬性**]。 
   1. 識別虛擬網路的資源識別碼，並在其`/subnets/<subnetname>`結尾附加以建立子網資源識別碼。 例如：
      - 您的虛擬網路資源識別碼是：`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - 您的子網名稱為：`default`
      - 因此，您的子網資源識別碼是：`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


下列程式碼片段會建立可用性群組接聽程式：

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

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>修改可用性群組中的複本數目
當您要將可用性群組部署至 Azure 中裝載的 SQL Server Vm 時，會有一層額外的複雜性。 資源提供者和虛擬機器群組現在會管理資源。 因此，當您在可用性群組中新增或移除複本時，會有一個額外的步驟來更新接聽程式中繼資料，其中包含 SQL Server Vm 的相關資訊。 當您要修改可用性群組中的複本數目時，也必須使用[az sql vm group ag-接聽程式更新](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update)命令，以 SQL Server vm 的中繼資料來更新接聽程式。 


### <a name="add-a-replica"></a>新增複本

若要將新的複本加入至可用性群組：

1. 將 SQL Server VM 新增至叢集：
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. 使用 SQL Server Management Studio，將 SQL Server 實例新增為可用性群組中的複本。
1. 將 SQL Server VM 中繼資料新增至接聽程式：
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>移除複本

若要從可用性群組中移除複本：

1. 使用 SQL Server Management Studio，從可用性群組中移除複本。 
1. 從接聽程式移除 SQL Server 的 VM 中繼資料：
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. 從叢集移除 SQL Server VM：
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>移除可用性群組接聽程式
如果您稍後需要移除以 Azure CLI 設定的可用性群組接聽程式，您必須經過 SQL VM 資源提供者。 因為接聽程式是透過 SQL VM 資源提供者來註冊，所以只要透過 SQL Server Management Studio 刪除它就不足。 

最佳方法是使用 Azure CLI 中的下列程式碼片段，透過 SQL VM 資源提供者刪除它。 這麼做會從 SQL VM 資源提供者中移除可用性群組接聽程式中繼資料。 它也會實際刪除可用性群組中的接聽程式。 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [SQL Server Vm 的總覽](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Vm 的常見問題](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server Vm 的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [切換 SQL Server VM 的授權模式](virtual-machines-windows-sql-ahb.md)
* [Always On 可用性群組 &#40;SQL Server 的總覽&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [設定 Always On 可用性群組 &#40;SQL Server 的伺服器實例&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [管理可用性群組 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [監視可用性群組 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Always On 可用性群組 &#40;SQL Server&#41;的 Transact-sql 語句總覽](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Always On 可用性群組 &#40;SQL Server&#41;的 PowerShell Cmdlet 總覽](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
