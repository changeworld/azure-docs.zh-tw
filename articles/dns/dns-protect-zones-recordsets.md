---
title: 保護 DNS 區域和記錄-Azure DNS
description: 在此學習路徑中，開始保護 Microsoft Azure DNS 中的 DNS 區域和記錄集。
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 52cb1f144608202739dc46f2053950b38d810631
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330150"
---
# <a name="how-to-protect-dns-zones-and-records"></a>如何保護 DNS 區域和記錄

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS 區域和記錄是重要的資源。 刪除 DNS 區域或單一 DNS 記錄可能會導致服務中斷。 請務必保護 DNS 區域和記錄，以防止未經授權或意外的變更。

本文說明 Azure DNS 如何讓您針對這類變更保護私人 DNS 區域和記錄。  我們會套用 Azure Resource Manager 所提供的兩項強大的安全性功能： [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/overview.md) 和 [資源鎖定](../azure-resource-manager/management/lock-resources.md)。

## <a name="azure-role-based-access-control"></a>Azure 角色型存取控制

Azure 角色型存取控制 (Azure RBAC) 可針對 Azure 使用者、群組和資源提供更細緻的存取權管理。 使用 Azure RBAC，您可以授與使用者所需的存取層級。 如需 Azure RBAC 如何協助您管理存取權的詳細資訊，請參閱 [什麼是 azure (AZURE rbac) 的 azure 角色型存取控制 ](../role-based-access-control/overview.md)。

### <a name="the-dns-zone-contributor-role"></a>DNS 區域參與者角色

DNS 區域參與者角色是用來管理私人 DNS 資源的內建角色。 此角色會套用至使用者或群組，讓他們能夠管理 DNS 資源。

資源群組 *myResourceGroup* 包含 Contoso Corporation 的五個區域。 將該資源群組的 DNS 區域參與者權限授與 DNS 系統管理員，可讓他們具有這些 DNS 區域的完整控制權。 它可避免授與不必要的許可權。 DNS 系統管理員無法建立或停止虛擬機器。

指派 Azure RBAC 許可權最簡單的方式是透過 [Azure 入口網站](../role-based-access-control/role-assignments-portal.md)。  

開啟資源群組的 [ **存取控制] (IAM) ** ，然後選取 [ **新增**]，再選取 [ **DNS 區域參與者** ] 角色。 選取所需的使用者或群組以授與許可權。

![透過 Azure 入口網站的資源群組層級 Azure RBAC](./media/dns-protect-zones-recordsets/rbac1.png)

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

### <a name="zone-level-azure-rbac"></a>區域層級 Azure RBAC

Azure RBAC 規則可以套用至訂用帳戶、資源群組或個別資源。 該資源可以是個別的 DNS 區域，也可以是個別的記錄集。

例如，資源群組 *myResourceGroup* 包含區域 *contoso.com* 和子領域 *customers.contoso.com*。 系統會為每個客戶帳戶建立 CNAME 記錄。 用來管理 CNAME 記錄的系統管理員帳戶，會獲指派在 *customers.contoso.com* 區域中建立記錄的許可權。 帳戶只能管理 *customers.contoso.com* 。

您可以透過 Azure 入口網站授與區域層級的 Azure RBAC 許可權。  開啟區域的 [ **存取控制] (IAM) ** ，選取 [ **新增**]，然後選取 [ **DNS 區域參與者** ] 角色，然後選取必要的使用者或群組以授與許可權。

![經由 Azure 入口網站的 DNS 區域層級 Azure RBAC](./media/dns-protect-zones-recordsets/rbac2.png)

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

### <a name="record-set-level-azure-rbac"></a>記錄集層級 Azure RBAC

許可權會套用於記錄集層級。  使用者會獲得所需專案的控制權，而且無法進行任何其他變更。

您可以使用 [記錄集] 頁面中的 [ **存取控制] (IAM) ** 按鈕，透過 Azure 入口網站設定記錄集層級的 Azure RBAC 許可權：

![透過 Azure 入口網站記錄集層級的 Azure RBAC](./media/dns-protect-zones-recordsets/rbac3.png)

您也可以 [使用 Azure PowerShell 來授](../role-based-access-control/role-assignments-powershell.md)與記錄集層級的 Azure RBAC 許可權：

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

內建 DNS 區域參與者角色可以有 DNS 資源的完整控制權。 您可以建立自己的自訂 Azure 角色，以提供更細微的控制。

用來管理 Cname 的帳戶只被授與管理 CNAME 記錄的許可權。 帳戶無法修改其他類型的記錄。 帳戶無法進列區域層級的作業，例如區域刪除。

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
> 使用 Azure 自訂角色來防止刪除記錄集，同時仍然允許更新這些記錄集並不是有效的控制。 它會防止記錄集被刪除，但它不會防止它們被修改。  允許的修改包括從記錄集新增和移除記錄，包括移除所有記錄以保持空白記錄集。 這與從 DNS 解析觀點來刪除記錄集有相同的效果。

自訂角色定義目前無法透過 Azure 入口網站定義。 可以使用 Azure PowerShell 建立根據此角色定義的自訂角色︰

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

如需有關如何建立、管理及指派自訂角色的詳細資訊，請參閱 [Azure 自訂角色](../role-based-access-control/custom-roles.md)。

## <a name="resource-locks"></a>資源鎖定

Azure Resource Manager 支援另一種類型的安全性控制，也就是鎖定資源的能力。 資源鎖定會套用至資源，而且在所有使用者和角色中都是有效的。 如需詳細資訊，請參閱 [使用 Azure Resource Manager 鎖定資源](../azure-resource-manager/management/lock-resources.md)。

資源鎖定有兩種類型： **CanNotDelete** 和 **ReadOnly**。 這些鎖定類型可以套用至私人 DNS 區域或個別記錄集。 下列章節說明幾個常見的案例，以及如何使用資源鎖定進行支援。

### <a name="protecting-against-all-changes"></a>保護免於所有變更

若要避免進行變更，請將 ReadOnly 鎖定套用至區域。 此鎖定可防止建立新的記錄集，以及修改或刪除現有的記錄集。

可以透過 Azure 入口網站來建立區域層級資源鎖定。  從 [DNS 區域] 頁面選取 [鎖定]****，然後選取 [+新增]****：

![透過 Azure 入口網站的區域層級資源鎖定](./media/dns-protect-zones-recordsets/locks1.png)

您也可以透過 [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)建立區域層級資源鎖定：

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

記錄集層級資源鎖定目前只能使用 Azure PowerShell 進行設定。  Azure 入口網站或 Azure CLI 不支援這些功能。

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

在 Azure DNS 中刪除區域時，會刪除區域中的所有記錄集。  此操作無法復原。 不小心刪除重要區域，可能會對業務造成嚴重影響。  避免意外刪除區域是很重要的。

將 CanNotDelete 鎖定套用至區域可防止區域刪除。 子資源會繼承鎖定。 鎖定可防止刪除區域中的任何記錄集。 如上面的附注所述，因為仍然可以從現有的記錄集移除記錄，所以會失效。

或者，將 CanNotDelete 鎖定套用至區域中的記錄集，例如 SOA 記錄集。 未刪除記錄集也不會刪除該區域。 此鎖定可防止區域刪除，同時仍然允許區域內的記錄集自由修改。 如果嘗試刪除區域，Azure Resource Manager 會偵測到此移除。 移除也會刪除 SOA 記錄集，Azure Resource Manager 封鎖此呼叫，因為 SOA 已被鎖定。  不會刪除任何記錄集。

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

防止意外刪除區域的另一個選項是使用自訂角色。 此角色可確保用來管理區域的帳戶沒有區域刪除許可權。 

當您需要刪除區域時，您可以強制執行兩步驟刪除：

 - 首先，授與區域刪除許可權
 - 其次，授與刪除區域的許可權。

自訂角色適用于這些帳戶所存取的所有區域。 具有區域刪除許可權的帳戶（例如訂用帳戶擁有者）仍可能不小心刪除區域。

您可以同時使用這兩種方法（資源鎖定和自訂角色），作為 DNS 區域保護的深度防禦方法。

## <a name="next-steps"></a>後續步驟

* 如需有關使用 Azure RBAC 的詳細資訊，請參閱 [什麼是 azure (AZURE rbac) 的 azure 角色型存取控制 ](../role-based-access-control/overview.md)。
* 如需使用資源鎖定的詳細資訊，請參閱[使用 Azure Resource Manager 鎖定資源](../azure-resource-manager/management/lock-resources.md)。
