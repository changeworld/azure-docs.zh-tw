---
title: 保護 DNS 區域和記錄 - Azure DNS
description: 在此學習路徑中，開始保護 Microsoft Azure DNS 中的 DNS 區域和記錄集。
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 89a945f146601084795b2e12a721a03a1b96aaea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371472"
---
# <a name="how-to-protect-dns-zones-and-records"></a>如何保護 DNS 區域和記錄

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS 區域和記錄是重要的資源。 刪除 DNS 區域或單個 DNS 記錄可能會導致服務中斷。 保護 DNS 區域和記錄免受未經授權的或意外更改非常重要。

本文介紹 Azure DNS 如何保護專用 DNS 區域和記錄免受此類更改。  我們應用 Azure 資源管理器提供的兩個強大證券功能：[基於角色的存取控制](../role-based-access-control/overview.md)[和資源鎖](../azure-resource-manager/management/lock-resources.md)。

## <a name="role-based-access-control"></a>角色型存取控制

Azure 角色型存取控制 (RBAC) 可以對 Azure 使用者、群組和資源進行更細緻的存取權管理。 使用 RBAC，您可以授予使用者所需的存取層級。 如需有關 RBAC 如何協助您管理存取權的詳細資訊，請參閱[什麼是角色型存取控制](../role-based-access-control/overview.md)。

### <a name="the-dns-zone-contributor-role"></a>DNS 區域參與者角色

DNS 區域參與者角色是管理專用 DNS 資源的內置角色。 此角色應用於使用者或組，使他們能夠管理 DNS 資源。

*資源組 myResourceGroup*包含康托索公司的五個區域。 將該資源群組的 DNS 區域參與者權限授與 DNS 系統管理員，可讓他們具有這些 DNS 區域的完整控制權。 它避免授予不必要的許可權。 DNS 管理員無法創建或停止虛擬機器。

指派 RBAC 權限的最簡單方式是[透過 Azure 入口網站](../role-based-access-control/role-assignments-portal.md)。  

打開資源組**的存取控制 （IAM），** 然後選擇 **"添加**"，然後選擇**DNS 區域參與者**角色。 選擇要授予許可權所需的使用者或組。

![透過 Azure 入口網站的資源群組層級 RBAC](./media/dns-protect-zones-recordsets/rbac1.png)

權限也可以[使用 Azure PowerShell 授與](../role-based-access-control/role-assignments-powershell.md)：

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

對等的命令也可以[透過 Azure CLI 使用](../role-based-access-control/role-assignments-cli.md)：

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-rbac"></a>區域層級 RBAC

Azure RBAC 規則可以套用至訂用帳戶、資源群組或個別資源。 該資源可以是單個 DNS 區域或單個記錄集。

例如，資源組*myResourceGroup*包含區域*contoso.com*和子領域*customers.contoso.com*。 為每個客戶帳戶創建 CNAME 記錄。 用於管理 CNAME 記錄的管理員帳戶被分配在*customers.contoso.com*區域中創建記錄的許可權。 該帳戶只能管理*customers.contoso.com。*

區域層級 RBAC 權限可以透過 Azure 入口網站授與。  打開區域**的存取控制 （IAM），** 選擇 **"添加**"，然後選擇**DNS 區域參與者**角色，然後選擇授予許可權所需的使用者或組。

![透過 Azure 入口網站的 DNS 區域層級 RBAC](./media/dns-protect-zones-recordsets/rbac2.png)

權限也可以[使用 Azure PowerShell 授與](../role-based-access-control/role-assignments-powershell.md)：

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

對等的命令也可以[透過 Azure CLI 使用](../role-based-access-control/role-assignments-cli.md)：

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>記錄集層級 RBAC

許可權在記錄集級別應用。  使用者被授予對他們需要的條目的控制項，並且無法進行任何其他更改。

可以使用記錄集頁中的**存取控制 （IAM）** 按鈕通過 Azure 門戶配置記錄集級別 RBAC 許可權：

![透過 Azure 入口網站的記錄集層級 RBAC](./media/dns-protect-zones-recordsets/rbac3.png)

記錄集層級 RBAC 權限也可以[使用 Azure PowerShell 授與](../role-based-access-control/role-assignments-powershell.md)：

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

對等的命令也可以[透過 Azure CLI 使用](../role-based-access-control/role-assignments-cli.md)：

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>自訂角色

內建 DNS 區域參與者角色可以有 DNS 資源的完整控制權。 可以構建自己的自訂 Azure 角色，以提供更細細微性的控制項。

用於管理 CNAMA 的帳戶被授予僅管理 CNAME 記錄的許可權。 帳戶無法修改其他類型的記錄。 該帳戶無法執列區域級操作，如區域刪除。

下列範例示範只管理 CNAME 記錄的自訂角色定義︰

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Actions 屬性會定義下列 DNS 特定權限︰

* `Microsoft.Network/dnsZones/CNAME/*` 授與 CNAME 記錄的完整控制權
* `Microsoft.Network/dnsZones/read` 授與權限以讀取 DNS 區域，但無法修改它們，讓您查看在其中建立 CNAME 的區域。

剩餘的動作會從 [DNS 區域參與者內建角色](../role-based-access-control/built-in-roles.md#dns-zone-contributor)複製。

> [!NOTE]
> 使用自訂 RBAC 角色，以避免刪除記錄集，同時仍然允許更新它們不是有效的控制。 它會防止記錄集被刪除，但它不會防止它們被修改。  允許的修改包括從記錄集新增和移除記錄，包括移除所有記錄以保持空白記錄集。 這與從 DNS 解析觀點來刪除記錄集有相同的效果。

當前無法通過 Azure 門戶定義自訂角色定義。 可以使用 Azure PowerShell 建立根據此角色定義的自訂角色︰

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

也可以透過 Azure CLI 建立︰

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

然後可以使用與內建角色相同的方式指派角色，如本文稍早所述。

有關如何創建、管理和分配自訂角色的詳細資訊，請參閱[Azure RBAC 中的自訂角色](../role-based-access-control/custom-roles.md)。

## <a name="resource-locks"></a>資源鎖定

Azure 資源管理器支援另一種類型的安全控制，即鎖定資源的能力。 資源鎖應用於資源，並在所有使用者和角色中有效。 有關詳細資訊，請參閱使用[Azure 資源管理器鎖定資源](../azure-resource-manager/management/lock-resources.md)。

有兩種類型的資源鎖：**無法刪除**和**唯讀**。 這些鎖類型可以應用於專用 DNS 區域或單個記錄集。 下列章節說明幾個常見的案例，以及如何使用資源鎖定進行支援。

### <a name="protecting-against-all-changes"></a>保護免於所有變更

為了防止進行更改，請對區域應用"唯讀"鎖。 此鎖阻止創建新記錄集，以及修改或刪除現有記錄集。

可以透過 Azure 入口網站來建立區域層級資源鎖定。  從 [DNS 區域] 頁面選取 [鎖定]****，然後選取 [+新增]****：

![透過 Azure 入口網站的區域層級資源鎖定](./media/dns-protect-zones-recordsets/locks1.png)

區域級資源鎖也可以通過[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)創建：

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

對等的命令也可以[透過 Azure CLI 使用](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create)：

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>保護個別記錄

若要防止現有 DNS 記錄集遭到修改，請將 ReadOnly 鎖定套用至記錄集。

> [!NOTE]
> 將 CanNotDelete 鎖定套用至記錄集不是有效的控制。 它會防止記錄集被刪除，但它不會防止它被修改。  允許的修改包括從記錄集新增和移除記錄，包括移除所有記錄以保持空白記錄集。 這與從 DNS 解析觀點來刪除記錄集有相同的效果。

記錄集層級資源鎖定目前只能使用 Azure PowerShell 進行設定。  Azure 門戶或 Azure CLI 不支援它們。

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>防止區域刪除

在 Azure DNS 中刪除區域時，將刪除區域中的所有記錄集。  無法撤銷此操作。 不小心刪除重要區域，可能會對業務造成嚴重影響。  防止意外區域刪除非常重要。

將 CanNotDelete 鎖定套用至區域可防止區域刪除。 鎖由子資源繼承。 鎖可防止刪除區域中的任何記錄集。 如上文注釋所述，它無效，因為記錄仍然可以從現有記錄集中刪除。

作為替代方法，將 CanNotDelete 鎖應用於區域中的記錄集，如 SOA 記錄集。 如果不刪除記錄集，也不會刪除該區域。 此鎖可防止區域刪除，同時仍允許自由修改區域中的記錄集。 如果嘗試刪除區域，Azure 資源管理器將檢測到此刪除。 刪除還將刪除 SOA 記錄集，Azure 資源管理器會阻止調用，因為 SOA 已鎖定。  不會刪除任何記錄集。

下列 PowerShell 命令會針對指定區域的 SOA 記錄建立 CanNotDelete 鎖定︰

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

防止意外刪除區域的另一個選項是使用自訂角色。 此角色可確保用於管理區域的帳戶沒有區域刪除許可權。 

當您確實需要刪除區域時，可以強制實施兩步刪除：

 - 首先，授予區域刪除許可權
 - 其次，授予刪除區域的許可權。

自訂角色適用于這些帳戶訪問的所有區域。 具有區域刪除許可權的帳戶（如訂閱擁有者）仍可能意外刪除區域。

可以同時使用資源鎖定和自訂角色這兩種方法，作為 DNS 區域保護的縱深防禦方法。

## <a name="next-steps"></a>後續步驟

* 如需使用 RBAC 的詳細資訊，請參閱[開始使用 Azure 入口網站中的存取管理](../role-based-access-control/overview.md)。
* 如需使用資源鎖定的詳細資訊，請參閱[使用 Azure Resource Manager 鎖定資源](../azure-resource-manager/management/lock-resources.md)。
