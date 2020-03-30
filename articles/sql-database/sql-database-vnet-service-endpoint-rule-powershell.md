---
title: 用於 VNet 終結點的 PowerShell 和單個和池資料庫的規則
description: 提供 PowerShell 腳本，用於為 Azure SQL 資料庫和 Azure 突觸創建和管理虛擬服務終結點。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, vanto
ms.date: 03/12/2019
tags: azure-synapse
ms.openlocfilehash: 1e8ec394eab1df0aebe394b8acebc74c7ed49e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124694"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell：為 SQL 建立虛擬服務端點與 VNet 規則

*虛擬網路規則*是一個防火牆安全功能，它控制 Azure [SQL 資料庫中](sql-database-technical-overview.md)的單個資料庫和彈性池的資料庫伺服器或[Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)中的資料庫是否接受從虛擬網路中的特定子網發送的通信。

> [!IMPORTANT]
> 本文適用于 Azure SQL 伺服器，以及 Azure 同步器中在 Azure SQL 伺服器上創建的 SQL 資料庫和資料倉儲。 為簡單起見，SQL 資料庫在引用 SQL 資料庫和 Azure 突觸時使用。 本文「不」** 適用 Azure SQL Database 中的**受控執行個體**部署，因為它沒有相關聯的服務端點。

本文提供並說明可執行下列動作的 PowerShell 指令碼：

1. 在您的子網路上建立 Microsoft Azure「虛擬服務端點」**。
2. 將端點加入 Azure SQL Database 伺服器的防火牆，以建立「虛擬網路規則」**。

有關為何要建立規則的說明，請參閱：[Azure SQL Database 的虛擬服務端點][sql-db-vnet-service-endpoint-rule-overview-735r]。

> [!TIP]
> 如果您需要的是評估 SQL Database 的虛擬服務端點「類型名稱」** 或將它加入子網路，則可以直接跳到更為[直接的 PowerShell 指令碼](#a-verify-subnet-is-endpoint-ps-100)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Azure SQL 資料庫仍然支援 PowerShell Azure 資源管理器模組，但所有後續開發都針對 Az.Sql 模組。 有關這些 Cmdlet，請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的參數基本相同。

## <a name="major-cmdlets"></a>主要 Cmdlet

本文強調將子網終結點添加到 Azure SQL 資料庫伺服器的存取控制清單 （ACL） 的新 **-AzSqlServerVirtualNetworkRule** Cmdlet，從而創建規則。

下面的清單顯示了其他*主要*Cmdlet 的順序，您必須運行，以準備調用**New-AzSqlServer虛擬網路規則**。 在本文中，這些呼叫發生於[指令碼 3「虛擬網路規則」](#a-script-30)中：

1. [新建-Az虛擬網路子網配置](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig)：創建子網物件。
2. [新-Az虛擬網路](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork)：創建虛擬網路，為它提供子網。
3. [設置-Az虛擬網路子網配置](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig)：將虛擬服務終結點分配給子網。
4. [設置-Az虛擬網路](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork)：保留對虛擬網路所做的更新。
5. [New-AzSqlServer虛擬網路規則](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule)：在子網成為終結點後，將子網作為虛擬網路規則添加到 Azure SQL 資料庫伺服器的 ACL 中。
   - 從 Azure RM PowerShell 模組 5.1.1 版開始，此 Cmdlet 就提供參數 **-IgnoreMissingVnetServiceEndpoint**。

## <a name="prerequisites-for-running-powershell"></a>執行 PowerShell 的必要條件

- 您已經可以登入 Azure，例如透過 [Azure 入口網站][http-azure-portal-link-ref-477t]。
- 您已經可以執行 PowerShell 指令碼。

> [!NOTE]
> 針對要新增至伺服器的 VNet/子網路，請確認已開啟服務端點，否則建立 VNet 防火牆規則將會失敗。

## <a name="one-script-divided-into-four-chunks"></a>分成四個區塊的單一指令碼

示範的 PowerShell 指令碼會分割成一系列較小的指令碼。 分割指令碼有助於學習，並提供彈性。 指令碼需按照指定的順序執行。 如果您目前沒有時間執行指令碼，我們的實際測試輸出顯示在指令碼 4 的後面。

<a name="a-script-10" />

### <a name="script-1-variables"></a>指令碼 1：變數

第一個 PowerShell 指令碼會將值指派給變數。 後續的指令碼將會仰賴這些變數。

> [!IMPORTANT]
> 執行此指令碼之前，您可以依本身需求來編輯這些值。 比方說，如果您已經有一個資源群組，則可能會將資源群組名稱編輯為指派的值。
>
> 您的訂用帳戶名稱應編輯到指令碼中。

### <a name="powershell-script-1-source-code"></a>PowerShell 指令碼 1 原始程式碼

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>指令碼 2：必要條件

此指令碼會針對端點動作所在的下一個指令碼作準備。 此指令碼會為您建立以下列出的項目，但僅限於它們尚未存在時。 如果您確定這些項目已存在，則可跳過指令碼 2：

- Azure 資源群組
- Azure SQL Database 伺服器

### <a name="powershell-script-2-source-code"></a>PowerShell 指令碼 2 原始程式碼

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";
    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else {
    Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; 
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>指令碼 3：建立端點及規則

此指令碼會建立具有子網路的虛擬網路。 然後，指令碼會將 **Microsoft.Sql** 端點類型指派給您的子網路。 最後，指令碼會將您的子網路加入 SQL Database 伺服器的存取控制清單 (ACL)，以藉此建立規則。

### <a name="powershell-script-3-source-code"></a>PowerShell 指令碼 3 原始程式碼

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>指令碼 4：清除

此最終指令碼會刪除先前指令碼所建立的示範用資源。 不過，指令碼在刪除下列項目之前會要求確認：

- Azure SQL Database 伺服器
- Azure 資源群組

您可以在指令碼 1 完成後的任何時間執行指令碼 4。

### <a name="powershell-script-4-source-code"></a>PowerShell 指令碼 4 原始程式碼

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno) {
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>確認您的子網路是端點

子網路可能已被指派 **Microsoft.Sql** 類型名稱，這表示它已是虛擬服務端點。 您可以使用 [Azure 入口網站][http-azure-portal-link-ref-477t]從端點建立虛擬網路規則。

或者，您可能不確定您的子網路是否具有 **Microsoft.Sql** 類型名稱。 您可以執行下列 PowerShell 指令碼以進行下列動作：

1. 確定您的子網路是否具有 **Microsoft.Sql** 類型名稱。
2. 選擇性地指派類型名稱 (若沒有的話)。
    - 指令碼會先要求您「確認」**，然後再套用缺少的類型名稱。

### <a name="phases-of-the-script"></a>指令碼的階段

以下是 PowerShell 指令碼的各個階段：

1. 登入您的 Azure 帳戶，每個 PS 工作階段只需登入一次。  指派變數。
2. 搜尋您的虛擬網路，然後搜尋您的子網路。
3. 您的子網路是否已標記為 **Microsoft.Sql** 端點伺服器類型？
4. 在您的子網路上加入類型名稱是 **Microsoft.Sql** 的虛擬服務端點。

> [!IMPORTANT]
> 執行此指令碼之前，您必須先編輯指派給靠近指令碼最上方 $ 變數的值。

### <a name="direct-powershell-source-code"></a>直接的 PowerShell 原始程式碼

除非要求確認時您回應「是」，否則此 PowerShell 指令碼不會更新任何項目。 此指令碼可以將類型名稱 **Microsoft.Sql** 加入您的子網路。 不過，只有當您的子網路缺少類型名稱時，此指令碼才會嘗試加入。

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
