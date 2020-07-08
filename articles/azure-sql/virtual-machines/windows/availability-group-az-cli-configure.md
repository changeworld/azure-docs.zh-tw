---
title: 使用 Azure CLI 設定可用性群組
description: 使用 Azure CLI 在 Azure 中的 SQL Server VM 上建立 Windows 容錯移轉叢集、可用性群組接聽程式和內部負載平衡器。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3366438012ecc3395e7f4ae3774316ef1ddcd3b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669336"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-azure-vm"></a>使用 Azure CLI 在 Azure VM 上設定 SQL Server 的 Always On 可用性群組
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文說明如何使用 [Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) 來部署 Windows 容錯移轉叢集、將 SQL Server VM 新增至叢集，以及建立 Always On 可用性群組的內部負載平衡器和接聽程式。 Always On 可用性群組的部署目前仍要手動透過 SQL Server Management Studio (SSMS) 來完成。 

## <a name="prerequisites"></a>必要條件
若要使用 Azure CLI 自動設定 Always On 可用性群組，則必須具備下列先決條件： 
- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- 具有網域控制站的資源群組。 
- [Azure 中一或多個執行 SQL Server 2016 (或更新版本) Enterprise Edition 的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)，且這些 VM 必須已加入網域，並且位於已[向 SQL VM 資源提供者註冊](sql-vm-resource-provider-register.md)的*相同可用性設定組或不同可用性區域*中。  
- [Azure CLI](/cli/azure/install-azure-cli)。 
- 兩個可用 (未由任何實體使用) 的 IP 位址。 一個用於內部負載平衡器。 另一個用於與可用性群組位於相同子網路內的可用性群組接聽程式。 如果您使用的是現有的負載平衡器，則可用性群組接聽程式只需要一個可用的 IP 位址。 

## <a name="permissions"></a>權限
您需要有下列帳戶權限，才能使用 Azure CLI 來設定 Always On 可用性群組： 

- 在網域中擁有**建立電腦物件**權限的現有網域使用者帳戶。 例如，網域系統管理員帳戶通常會有足夠的權限 (例如：account@domain.com)。 _此帳戶也應該屬於建立叢集的每個 VM 上的本機系統管理員群組一部分。_
- 控制 SQL Server 的網域使用者帳戶。 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>步驟 1:將儲存體帳戶建立為雲端見證
叢集需要有儲存體帳戶來作為雲端見證。 您可以使用任何現有的儲存體帳戶，也可以建立新的儲存體帳戶。 如果您想要使用現有的儲存體帳戶，請跳至下一節。 

下列程式碼片段會建立儲存體帳戶： 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> 如果您使用過期的 Azure CLI 版本，則可能會看到錯誤 `az sql: 'vm' is not in the 'az sql' command group`。 請下載 [Azure CLI 的最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)以解決這個錯誤。

## <a name="step-2-define-windows-failover-cluster-metadata"></a>步驟 2:定義 Windows 容錯移轉叢集中繼資料
Azure CLI 的 [az sql vm group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) 命令群組可管理可用性群組裝載所在 Windows Server 容錯移轉叢集 (WSFC) 服務的中繼資料。 叢集中繼資料包括 Active Directory 網域、叢集帳戶、用來作為雲端見證的儲存體帳戶，以及 SQL Server 版本。 請使用 [az sql vm group create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) 來定義 WSFC 的中繼資料，如此一來，在新增第一個 SQL Server VM 時就會依照定義來建立叢集。 

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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>步驟 3：將 SQL Server VM 新增至叢集
將第一個 SQL Server VM 新增至叢集便會建立叢集。 [az sql vm add-to-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) 命令會以先前指定的名稱建立叢集、將叢集角色安裝到 SQL Server VM 上，再將這些 VM 新增至叢集。 後續使用 `az sql vm add-to-group` 命令時，則會將更多 SQL Server VM 新增至新建立的叢集。 

下列程式碼片段會建立叢集，並於其中新增第一個 SQL Server VM： 

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
使用此命令可將任何其他 SQL Server VM 新增至叢集。 只需修改 SQL Server VM 名稱的 `-n` 參數即可。 

## <a name="step-4-create-the-availability-group"></a>步驟 4：建立可用性群組
像平常一樣，使用 [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)、[PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) 或 [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) 手動建立可用性群組。 

>[!IMPORTANT]
> 目前還*不要*建立接聽程式，會在後面幾節透過 Azure CLI 來完成。  

## <a name="step-5-create-the-internal-load-balancer"></a>步驟 5：建立內部負載平衡器

Always On 可用性群組接聽程式需要 Azure Load Balancer 的內部執行個體。 內部負載平衡器會為可用性群組接聽程式提供「浮動」IP 位址，以加快容錯移轉和重新連線的速度。 如果可用性群組中的 SQL Server VM 屬於相同可用性設定組，則可使用基本的負載平衡器。 否則，即須使用標準負載平衡器。  

> [!NOTE]
> 內部負載平衡器應該與 SQL Server VM 執行個體位於相同的虛擬網路中。 

下列程式碼片段會建立內部負載平衡器：

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> 每個 SQL Server VM 的公用 IP 資源都應有標準 SKU，以便與標準負載平衡器相容。 若要確認 VM 公用 IP 資源的 SKU，請移至 [資源群組]，並為所需的 SQL Server VM 選取 [公用 IP 位址] 資源，然後在 [概觀] 窗格的 [SKU] 下方找出其值。  

## <a name="step-6-create-the-availability-group-listener"></a>步驟 6：建立可用性群組接聽程式
在您手動建立可用性群組之後，您可以使用 [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create) 建立接聽程式。 

*子網路資源識別碼*是附加至虛擬網路資源識別碼的 `/subnets/<subnetname>` 值。 若要識別子網路資源識別碼：
   1. 在 [Azure 入口網站](https://portal.azure.com)中移至您的資源群組。 
   1. 選取虛擬網路資源。 
   1. 在 [設定] 窗格中選取 [屬性]。 
   1. 找到虛擬網路的資源識別碼，並將 `/subnets/<subnetname>` 附加至其結尾，以建立子網路資源識別碼。 例如：
      - 您的虛擬網路資源識別碼是：`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - 您的子網路名稱是：`default`
      - 因此，您的子網路資源識別碼是：`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


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
當您要將可用性群組部署至 Azure 中裝載的 SQL Server VM 時，複雜程度會更高。 資源提供者和虛擬機器群組現在會管理資源。 因此，當您在可用性群組中新增或移除複本時，需要再進行一個步驟來使用 SQL Server VM 的相關資訊更新接聽程式的中繼資料。 當您要修改可用性群組中的複本數目時，您還必須使用 [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) 命令，以使用 SQL Server VM 的中繼資料更新接聽程式。 


### <a name="add-a-replica"></a>新增複本

若要將新的複本新增至可用性群組：

1. 將 SQL Server VM 新增至叢集：
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. 使用 SQL Server Management Studio 將 SQL Server 執行個體新增為可用性群組中的複本。
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

1. 使用 SQL Server Management Studio 從可用性群組中移除複本。 
1. 從接聽程式中移除 SQL Server VM 中繼資料：
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. 從叢集中移除 SQL Server VM：
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>移除可用性群組接聽程式
如果您稍後需要移除使用 Azure CLI 所設定的可用性群組接聽程式，您必須透過 SQL VM 資源提供者來執行。 由於接聽程式是透過 SQL VM 資源提供者註冊的，因此僅透過 SQL Server Management Studio 加以刪除並不足夠。 

最好的方法是在 Azure CLI 中使用下列程式碼片段，透過 SQL VM 資源提供者加以刪除。 這麼做可從 SQL VM 資源提供者中移除可用性群組接聽程式的中繼資料。 同時實際從可用性群組中刪除該接聽程式。 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [SQL Server VM 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server VM 常見問題集](frequently-asked-questions-faq.md)
* [SQL Server VM 版本資訊](../../database/doc-changes-updates-release-notes.md)
* [切換 SQL Server VM 的授權模式](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [AlwaysOn 可用性群組概觀 (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [設定 AlwaysOn 可用性群組的伺服器執行個體 (SQL Server)](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [可用性群組的管理 (SQL Server)](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [監視可用性群組 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [AlwaysOn 可用性群組的 Transact-SQL 陳述式概觀 (SQL Server)](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [AlwaysOn 可用性群組的 PowerShell Cmdlet 概觀 (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
