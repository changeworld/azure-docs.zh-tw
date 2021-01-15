---
title: 升級公用 IP 位址
titleSuffix: Azure Virtual Network
description: 將公用 IP 位址從基本升級至標準。
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions
ms.openlocfilehash: d6e8c4f4b6646254aeea12cf587f47047e661e3f
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222865"
---
# <a name="upgrade-public-ip-addresses"></a>升級公用 IP 位址

Azure 公用 IP 位址會使用 SKU （基本或標準）來建立，以決定其功能的各個層面 (包括配置方法、跨可用性區域的使用量，以及可與) 相關聯的資源。 

本文將討論下列案例：
* 如何使用入口網站、PowerShell 或 CLI) 將基本 SKU 公用 IP 升級為標準 SKU 公用 IP (
* 如何將傳統 Azure 保留的 IP 遷移至 Azure Resource Manager 基本 SKU 公用 IP

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>將公用 IP 位址從基本升級為標準 SKU

若要升級公用 IP，其不得與任何資源相關聯 (請參閱 [此頁面](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) ，以取得如何將公用 ip 解除關聯) 的詳細資訊。

>[!IMPORTANT]
>從基本升級至標準 SKU 的公用 Ip 會繼續沒有 [可用性區域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)。  這表示它們不能與區域冗余的 Azure 資源相關聯，或系結到提供此功能的區域中的預先指定區域。

---
# <a name="basic-to-standard---powershell"></a>[**基本至標準-PowerShell**](#tab/option-upgrade-powershell)

下列範例假設先前建立基本 SKU 公用 IP，使用 [此頁面](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic)上提供的範例搭配 **MyResourceGroup** 中的基本公用 IP **myBasicPublicIP** 。

若要升級 IP，只要使用 PowerShell 執行下列命令即可。  請注意，如果已靜態配置 IP 位址，則可以略過該區段。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**基本到標準-CLI**](#tab/option-upgrade-cli)

下列範例假設先前建立基本 SKU 公用 IP，使用 [此頁面](./create-public-ip-cli.md?tabs=option-create-public-ip-basic)上提供的範例搭配 **MyResourceGroup** 中的基本公用 IP **myBasicPublicIP** 。

若要升級 IP，只要使用 Azure CLI 執行下列命令。  請注意，如果已靜態配置 IP 位址，則可以略過該區段。

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>升級 (將傳統保留的 IP 遷移) 至靜態公用 IP

為了受益于 Azure Resource Manager 中的新功能，您可以將現有的公用靜態 IP 位址（稱為保留 Ip）從傳統模型遷移到新式的 Azure Resource Manager 模型。  遷移的公用 IP 將會是基本的 SKU 類型。


---

# <a name="reserved-to-basic---powershell"></a>[**保留至基本-PowerShell**](#tab/option-migrate-powershell)

下列範例假設先前在 **myResourceGroup** 中建立傳統 Azure 保留的 IP **>myreservedip** 。 另一項遷移的必要條件是確保 Azure Resource Manager 的訂用帳戶已註冊進行遷移。 本 [頁面](../virtual-machines/migration-classic-resource-manager-ps.md)的步驟3和4將詳細說明這一點。

若要遷移保留的 IP，請使用 PowerShell 執行下列命令。  請注意，如果 IP 位址未與任何服務相關聯 (下有一個名為 **myService** 的服務) ，則可以略過該步驟。

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
上述命令會顯示任何阻礙遷移的警告和錯誤。 如果驗證成功，您可以繼續進行下列步驟來準備和認可遷移：
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Azure Resource Manager 中的新資源群組是使用上述範例中的已遷移保留的 IP (名稱所建立，它會是資源群組 **>myreservedip 遷移的**) 。

# <a name="reserved-to-basic---cli"></a>[**保留至基本-CLI**](#tab/option-migrate-cli)

下列範例假設先前在 **myResourceGroup** 中建立傳統 Azure 保留的 IP **>myreservedip** 。 另一項遷移的必要條件是確保 Azure Resource Manager 的訂用帳戶已註冊進行遷移。 本 [頁面](../virtual-machines/migration-classic-resource-manager-cli.md)的步驟3和4將詳細說明這一點。

為了遷移保留的 IP，請使用 Azure CLI 執行下列命令。  請注意，如果 IP 位址未與任何服務相關聯 (下面有一個名為 **myService** And deployment **myDeployment**) 的服務，則可以略過該步驟。

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
上述命令會顯示任何阻礙遷移的警告和錯誤。 如果驗證成功，您可以繼續進行下列步驟來準備和認可遷移：
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Azure Resource Manager 中的新資源群組是使用上述範例中的已遷移保留的 IP (名稱所建立，它會是資源群組 **>myreservedip 遷移的**) 。

---

## <a name="limitations"></a>限制

* 為了升級基本公用 IP，它無法與任何 Azure 資源相關聯。  請參閱 [此頁面](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) ，以取得如何將公用 ip 解除關聯的詳細資訊。  同樣地，若要遷移保留的 IP，則無法與任何雲端服務相關聯。  請參閱 [此頁面](./remove-public-ip-address-vm.md) ，以取得如何將保留的 ip 解除關聯的詳細資訊。  
* 從基本升級至標準 SKU 的公用 Ip 會繼續沒有 [可用性區域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) ，因此無法與區域冗余或區域性的 Azure 資源相關聯。  請注意，這只適用于提供可用性區域的區域。
* 您無法從標準降級為基本。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 Azure 中的 [公用 ip 位址](./public-ip-addresses.md#public-ip-addresses) ，包括 SKU 類型和 [公用 ip 位址設定](virtual-network-public-ip-address.md#create-a-public-ip-address)之間的差異。
- 瞭解如何將 [Azure 公用負載平衡器從基本升級至標準](../load-balancer/upgrade-basic-standard.md)。
- 瞭解 [傳統的 Azure 保留 ip](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) ，以及 [將傳統資源遷移至 Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)。