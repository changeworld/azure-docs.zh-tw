---
title: 保護私人 DNS 區域和記錄-Azure DNS
description: 在此學習路徑中，開始在 Microsoft Azure DNS 中保護私人 DNS 區域和記錄集。
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: a817cb0722bff721891cd290ea4a6552c778dddf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84701842"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>如何保護私人 DNS 區域和記錄

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

私人 DNS 區域和記錄是重要的資源。 刪除 DNS 區域或單一 DNS 記錄可能會導致服務中斷。 請務必保護 DNS 區域和記錄，避免未經授權或意外的變更。

本文說明 Azure DNS 如何讓您根據這類變更來保護私人 DNS 區域和記錄。  我們會套用 Azure Resource Manager 所提供的兩個強大的安全性功能：[角色型存取控制](../role-based-access-control/overview.md)和[資源鎖定](../azure-resource-manager/management/lock-resources.md)。

## <a name="role-based-access-control"></a>角色型存取控制

Azure 角色型存取控制 (RBAC) 可以對 Azure 使用者、群組和資源進行更細緻的存取權管理。 使用 RBAC，您可以授與使用者所需的存取層級。 如需有關 RBAC 如何協助您管理存取權的詳細資訊，請參閱[什麼是角色型存取控制](../role-based-access-control/overview.md)。

### <a name="the-private-dns-zone-contributor-role"></a>私人 DNS 區域參與者角色

私人 DNS 區域參與者角色是用來管理私人 DNS 資源的內建角色。 此角色會套用到使用者或群組，讓他們能夠管理私人 DNS 資源。

[資源群組*myPrivateDNS* ] 包含 Contoso Corporation 的五個區域。 授與 DNS 系統管理員私人 DNS 區域參與者對該資源群組的許可權，可讓您完整控制這些 DNS 區域。 它可避免授與不必要的許可權。 DNS 系統管理員無法建立或停止虛擬機器。

指派 RBAC 權限的最簡單方式是[透過 Azure 入口網站](../role-based-access-control/role-assignments-portal.md)。  

開啟資源群組的 **[存取控制（IAM）** ]，選取 [**新增**]，然後選取 [**私人 DNS 區域參與者**] 角色。 選取必要的使用者或群組以授與許可權。

![透過 Azure 入口網站的資源群組層級 RBAC](./media/dns-protect-private-zones-recordsets/rbac1.png)

權限也可以[使用 Azure PowerShell 授與](../role-based-access-control/role-assignments-powershell.md)：

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

對等的命令也可以[透過 Azure CLI 使用](../role-based-access-control/role-assignments-cli.md)：

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-rbac"></a>私人區域層級 RBAC

Azure RBAC 規則可以套用至訂用帳戶、資源群組或個別資源。 該資源可以是個別的 DNS 區域或個別的記錄集。

例如，資源群組*myPrivateDNS*包含區域*private.contoso.com*和子領域*customers.private.contoso.com*。 系統會為每個客戶帳戶建立 CNAME 記錄。 用來管理 CNAME 記錄的系統管理員帳戶會被指派許可權，以在*customers.private.contoso.com*區域中建立記錄。 帳戶只能管理*customers.private.contoso.com* 。

區域層級 RBAC 權限可以透過 Azure 入口網站授與。  開啟區域的 **[存取控制（IAM）** ]，選取 [**新增**]，然後選取 [**私人 DNS 區域參與者**] 角色。 選取必要的使用者或群組以授與許可權。

![透過 Azure 入口網站的 DNS 區域層級 RBAC](./media/dns-protect-private-zones-recordsets/rbac2.png)

權限也可以[使用 Azure PowerShell 授與](../role-based-access-control/role-assignments-powershell.md)：

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

對等的命令也可以[透過 Azure CLI 使用](../role-based-access-control/role-assignments-cli.md)：

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>記錄集層級 RBAC

許可權會套用於記錄集層級。  使用者會被授與所需專案的控制權，而且無法進行任何其他變更。

記錄集層級 RBAC 許可權可透過 Azure 入口網站來設定，使用 [記錄集] 頁面中的 [**存取控制（IAM）** ] 按鈕：

![透過 Azure 入口網站的記錄集層級 RBAC](./media/dns-protect-private-zones-recordsets/rbac3.png)

![透過 Azure 入口網站的記錄集層級 RBAC](./media/dns-protect-private-zones-recordsets/rbac4.png)

記錄集層級 RBAC 權限也可以[使用 Azure PowerShell 授與](../role-based-access-control/role-assignments-powershell.md)：

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

對等的命令也可以[透過 Azure CLI 使用](../role-based-access-control/role-assignments-cli.md)：

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>自訂角色

內建的私人 DNS 區域參與者角色可讓您完整控制 DNS 資源。 您可以建立自己的自訂 Azure 角色，以提供更細微的控制。

用來管理 Cname 的帳戶只會被授與管理 CNAME 記錄的許可權。 帳戶無法修改其他類型的記錄。 帳戶無法執列區域層級的作業，例如區域刪除。

下列範例示範只管理 CNAME 記錄的自訂角色定義︰

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
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

* `Microsoft.Network/privateDnsZones/CNAME/*` 授與 CNAME 記錄的完整控制權
* `Microsoft.Network/privateDNSZones/read`授與讀取 DNS 私人區域的許可權，但無法修改它們，讓您可以查看正在建立 CNAME 的區域。

> [!NOTE]
> 使用自訂 RBAC 角色，以避免刪除記錄集，同時仍然允許更新它們不是有效的控制。 它會防止記錄集被刪除，但它不會防止它們被修改。  允許的修改包括從記錄集新增和移除記錄，包括移除所有記錄以保持空白記錄集。 這與從 DNS 解析觀點來刪除記錄集有相同的效果。

自訂角色定義目前無法透過 Azure 入口網站定義。 可以使用 Azure PowerShell 建立根據此角色定義的自訂角色︰

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

也可以透過 Azure CLI 建立︰

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

然後可以使用與內建角色相同的方式指派角色，如本文稍早所述。

如需如何建立、管理及指派自訂角色的詳細資訊，請參閱[AZURE RBAC 中的自訂角色](../role-based-access-control/custom-roles.md)。

## <a name="resource-locks"></a>資源鎖定

Azure Resource Manager 支援另一種類型的安全性控制，也就是鎖定資源的能力。 資源鎖定會套用至資源，並且在所有使用者和角色中都有效。 如需詳細資訊，請參閱[使用 Azure Resource Manager 鎖定資源](../azure-resource-manager/management/lock-resources.md)。

有兩種類型的資源鎖定： **CanNotDelete**和**ReadOnly**。 這些鎖定類型可以套用至私人 DNS 區域或個別記錄集。  下列章節說明幾個常見的案例，以及如何使用資源鎖定進行支援。

### <a name="protecting-against-all-changes"></a>保護免於所有變更

若要防止進行變更，請將 ReadOnly 鎖定套用至區域。 此鎖定可防止建立新的記錄集，以及修改或刪除現有的記錄集。

可以透過 Azure 入口網站來建立區域層級資源鎖定。  從 [DNS 區域] 頁面選取 [鎖定]****，然後選取 [+新增]****：

![透過 Azure 入口網站的區域層級資源鎖定](./media/dns-protect-private-zones-recordsets/locks1.png)

您也可以透過[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)來建立區域層級資源鎖定：

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

對等的命令也可以[透過 Azure CLI 使用](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create)：

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>保護個別記錄

若要防止現有 DNS 記錄集遭到修改，請將 ReadOnly 鎖定套用至記錄集。

> [!NOTE]
> 將 CanNotDelete 鎖定套用至記錄集不是有效的控制。 它會防止記錄集被刪除，但它不會防止它被修改。  允許的修改包括從記錄集新增和移除記錄，包括移除所有記錄以保持空白記錄集。 這與從 DNS 解析觀點來刪除記錄集有相同的效果。

記錄集層級資源鎖定目前只能使用 Azure PowerShell 進行設定。  Azure 入口網站或 Azure CLI 不支援它們。

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>防止區域刪除

在 Azure DNS 中刪除區域時，會刪除區域中的所有記錄集。  此作業無法復原。 不小心刪除重要區域，可能會對業務造成嚴重影響。  請務必防止意外刪除區域。

將 CanNotDelete 鎖定套用至區域可防止區域刪除。 子資源會繼承鎖定。 鎖定會防止刪除區域中的任何記錄集。 如上面的附注所述，這是不正確，因為仍然可以從現有的記錄集移除記錄。

或者，將 CanNotDelete 鎖定套用至區域中的記錄集，例如 SOA 記錄集。 不會刪除該區域，也不會刪除記錄集。 此鎖定可防止區域刪除，但仍允許在區域內自由修改記錄集。 如果嘗試刪除區域，Azure Resource Manager 會偵測到此移除。 移除也會刪除 SOA 記錄集，Azure Resource Manager 封鎖呼叫，因為 SOA 已鎖定。  不會刪除任何記錄集。

下列 PowerShell 命令會針對指定區域的 SOA 記錄建立 CanNotDelete 鎖定︰

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
防止意外刪除區域的另一個選項是使用自訂角色。 此角色可確保用來管理您的區域的帳戶不具有區域刪除許可權。 

當您需要刪除區域時，您可以強制執行兩步驟刪除：

 - 首先，授與區域刪除許可權
 - 第二，授與刪除區域的許可權。

自訂角色適用于這些帳戶所存取的所有區域。 具有區域刪除許可權的帳戶（例如訂用帳戶擁有者）仍可能不小心刪除區域。

您可以同時使用這兩種方法-資源鎖定和自訂角色，做為 DNS 區域保護的深層防禦方法。

## <a name="next-steps"></a>後續步驟

* 如需使用 RBAC 的詳細資訊，請參閱[開始使用 Azure 入口網站中的存取管理](../role-based-access-control/overview.md)。
* 如需使用資源鎖定的詳細資訊，請參閱[使用 Azure Resource Manager 鎖定資源](../azure-resource-manager/management/lock-resources.md)。
