---
title: 適用于單一和集區資料庫之 VNet 端點和規則的 PowerShell
description: 提供 PowerShell 腳本，以建立和管理 Azure SQL Database 和 Azure Synapse 的虛擬服務端點。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 04/17/2019
ms.custom: sqldbrb=1
tags: azure-synapse
ms.openlocfilehash: ae92d2000bb2c0dfd7e7a42c6070c143e5b787e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84170863"
---
# <a name="powershell-create-a-virtual-service-endpoint-and-vnet-rule-for-azure-sql-database"></a>PowerShell：建立 Azure SQL Database 的虛擬服務端點和 VNet 規則
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

*虛擬網路規則*是一項防火牆安全性功能，可控制[Azure Synapse](../../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)中[Azure SQL Database](../sql-database-paas-overview.md)資料庫、彈性集區或資料庫的[邏輯 SQL server](../logical-servers.md)是否接受從虛擬網路中的特定子網傳送的通訊。

> [!IMPORTANT]
> 本文適用于 Azure SQL Database，包括 Azure Synapse （先前稱為 SQL DW）。 為了簡單起見，本文中 Azure SQL Database 一詞適用于屬於 Azure SQL Database 或 Azure Synapse 的資料庫。 本文*不適用 AZURE* SQL 受控執行個體，因為它沒有相關聯的服務端點。

本文示範的 PowerShell 腳本會採取下列動作：

1. 在您的子網路上建立 Microsoft Azure「虛擬服務端點」**。
2. 將端點新增至您伺服器的防火牆，以建立*虛擬網路規則*。

如需更多背景，請參閱[Azure SQL Database 的虛擬服務端點][sql-db-vnet-service-endpoint-rule-overview-735r]。

> [!TIP]
> 如果您只需要針對 Azure SQL Database 的虛擬服務端點*類型名稱*進行評估或新增至您的子網，您可以跳到我們更[直接的 PowerShell 腳本](#a-verify-subnet-is-endpoint-ps-100)。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對[ `Az.Sql` Cmdlet](/powershell/module/az.sql)。 如需較舊的模組，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

## <a name="major-cmdlets"></a>主要 Cmdlet

本文強調[ **AzSqlServerVirtualNetworkRule** ](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule)指令程式，此 Cmdlet 會將子網端點新增至您伺服器的存取控制清單（ACL），藉此建立規則。

下列清單顯示您必須執行以準備呼叫**新 AzSqlServerVirtualNetworkRule**的其他*主要*Cmdlet 順序。 在本文中，這些呼叫發生於[指令碼 3「虛擬網路規則」](#a-script-30)中：

1. [New-azvirtualnetworksubnetconfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig)：建立子網物件。
2. [New-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork)：建立您的虛擬網路，並為其提供子網。
3. [New-azvirtualnetworksubnetconfig：將](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig)虛擬服務端點指派給您的子網。
4. [New-azvirtualnetwork：將](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork)對您的虛擬網路進行的更新保存。
5. [AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule)：當您的子網是端點之後，會將您的子網作為虛擬網路規則新增至伺服器的 ACL。
   - 從 Azure RM PowerShell 模組 5.1.1 版開始，此 Cmdlet 就提供參數 **-IgnoreMissingVnetServiceEndpoint**。

## <a name="prerequisites-for-running-powershell"></a>執行 PowerShell 的必要條件

- 您已經可以登入 Azure，例如透過 [Azure 入口網站][http-azure-portal-link-ref-477t]。
- 您已經可以執行 PowerShell 指令碼。

> [!NOTE]
> 針對要新增至伺服器的 VNet/子網路，請確認已開啟服務端點，否則建立 VNet 防火牆規則將會失敗。

## <a name="one-script-divided-into-four-chunks"></a>分成四個區塊的單一指令碼

示範的 PowerShell 指令碼會分割成一系列較小的指令碼。 分割指令碼有助於學習，並提供彈性。 指令碼需按照指定的順序執行。 如果您目前沒有時間執行指令碼，我們的實際測試輸出顯示在指令碼 4 的後面。

<a name="a-script-10"></a>

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

<a name="a-script-20"></a>

### <a name="script-2-prerequisites"></a>指令碼 2：必要條件

此指令碼會針對端點動作所在的下一個指令碼作準備。 此指令碼會為您建立以下列出的項目，但僅限於它們尚未存在時。 如果您確定這些項目已存在，則可跳過指令碼 2：

- Azure 資源群組
- 邏輯 SQL 伺服器

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
## Ensure your server already exists. ##
###########################################################

Write-Host "Check whether your server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing server - $SqlDbServerName.";
    Write-Host "Gather the credentials necessary to next create a server.";

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

    Write-Host "Create your server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else {
    Write-Host "Good, your server already exists - $SqlDbServerName.";
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30"></a>

## <a name="script-3-create-an-endpoint-and-a-rule"></a>指令碼 3：建立端點及規則

此指令碼會建立具有子網路的虛擬網路。 然後，指令碼會將 **Microsoft.Sql** 端點類型指派給您的子網路。 最後，腳本會將您的子網新增至存取控制清單（ACL），進而建立規則。

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

Write-Host "Add the subnet .Id as a rule, into the ACLs for your server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL Database ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40"></a>

## <a name="script-4-clean-up"></a>指令碼 4：清除

此最終指令碼會刪除先前指令碼所建立的示範用資源。 不過，指令碼在刪除下列項目之前會要求確認：

- 邏輯 SQL 伺服器
- Azure 資源群組

您可以在指令碼 1 完成後的任何時間執行指令碼 4。

### <a name="powershell-script-4-source-code"></a>PowerShell 指令碼 4 原始程式碼

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL Database ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL Database ACL.";

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
##   1. Logical SQL server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your server AND your resource group?  [yes/no]';
if ('yes' -eq $yesno) {
    Write-Host "Remove the server.";

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

<a name="a-actual-output"></a>

<a name="a-verify-subnet-is-endpoint-ps-100"></a>

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
[sql-db-vnet-service-endpoint-rule-overview-735r]:../vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
