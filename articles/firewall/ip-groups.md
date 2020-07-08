---
title: Azure 防火牆中的 IP 群組
description: IP 群組可讓您分組和管理 Azure 防火牆規則的 IP 位址。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: 7bfa1ae5bd0f2ffe92fb37494f9fe589e1b2040e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565553"
---
# <a name="ip-groups-in-azure-firewall"></a>Azure 防火牆中的 IP 群組

IP 群組可讓您以下列方式來分組和管理 Azure 防火牆規則的 IP 位址：

- 作為 DNAT 規則中的來源位址
- 作為網路規則中的來源或目的地位址
- 作為應用程式規則中的來源位址


IP 群組可以有單一 IP 位址、多個 IP 位址，或一或多個 IP 位址範圍。

在 azure 中跨區域和訂用帳戶的多個防火牆，您可以在 Azure 防火牆 DNAT、網路和應用程式規則中重複使用 IP 群組。 組名必須是唯一的。 您可以在 [Azure 入口網站]、[Azure CLI] 或 [REST API] 中設定 IP 群組。 提供範例範本可協助您開始使用。

## <a name="sample-format"></a>樣本格式

以下是有效的 IPv4 位址格式範例，可用於 IP 群組：

- 單一位址：10.0.0。0
- CIDR 標記法： 10.1.0.0/32
- 位址範圍： 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>建立 IP 群組

您可以使用 Azure 入口網站、Azure CLI 或 REST API 來建立 IP 群組。 如需詳細資訊，請參閱[建立 IP 群組](create-ip-group.md)。

## <a name="browse-ip-groups"></a>流覽 IP 群組
1. 在 [Azure 入口網站搜尋] 列中，輸入**IP 群組**並加以選取。 您可以看到 IP 群組的清單，或選取 [**新增**] 來建立新的 ip 群組。
2. 選取 IP 群組以開啟 [總覽] 頁面。 您可以編輯、新增或刪除 IP 位址或 IP 群組。

   ![IP 群組總覽](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>管理 IP 群組

您可以看到 IP 群組中的所有 IP 位址，以及與其相關聯的規則或資源。 若要刪除 IP 群組，您必須先將 IP 群組與使用它的資源中斷關聯。

1. 若要查看或編輯 IP 位址，請在左窗格中選取 [**設定**] 下的 [ **ip 位址**]。
2. 若要新增單一或多個 IP 位址，請選取 [**新增 Ip 位址**]。 這會開啟 [上傳] 的 [**拖曳] 或 [流覽]** 頁面，您也可以手動輸入位址。
3.    選取右邊的省略號（**...**），以編輯或刪除 IP 位址。 若要編輯或刪除多個 IP 位址，請選取方塊，然後選取頂端的 [**編輯**] 或 [**刪除**]。
4. 最後，可以將檔案匯出成 CSV 檔案格式。

> [!NOTE]
> 如果您刪除 IP 群組中的所有 IP 位址，但規則仍在使用中，則會略過該規則。


## <a name="use-an-ip-group"></a>使用 IP 群組

當您建立 Azure 防火牆 DNAT、應用程式或網路規則時，您現在可以選取**Ip 群組**做為 ip 位址的**來源類型**或**目的地類型**。

![防火牆中的 IP 群組](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>區域可用性

IP 群組適用于所有公用雲端區域。

## <a name="ip-address-limits"></a>IP 位址限制

針對 50 IP 群組（或更少），每個防火牆實例最多可以有5000個個別 IP 位址。 針對51到100的 IP 群組，每個防火牆實例可以有500個個別 IP 位址。

### <a name="examples"></a>範例

#### <a name="example-1-supported"></a>範例1：支援

|IP 群組  |IP 位址數目  |表示法  |規則  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Rule1|
|IPGroup2     |3|196.0.0.0 - 196.0.0.2|Rule1|
|IPGroup3     |1|1.2.3.4|Rule1|
|     |**總計4100**|         |         |
|     |         |         |         |

#### <a name="example-2-supported"></a>範例2：支援

|IP 群組  |IP 位址數目  |表示法  |規則  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Rule1|
|IPGroup2     |4096|11.0.0.0 版/20|Rule1|
|     |**總計8192**|         |         |

#### <a name="example-3-not-supported"></a>範例3：不支援

|IP 群組  |IP 位址數目  |表示法  |規則  |
|---------|---------|---------|---------|
|IPGroup1 |8192     |10.0.0.0/20、11.0.0.0 版/20  |Rule1|
|     |**總計8192**|||

#### <a name="example-4-supported"></a>範例4：支援

|IP 群組  |IP 位址數目  |表示法  |規則  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Rule1|
|IPGroup2     |4096|11.0.0.0 版/20|Rule2|
|     |**總計8192**|         |         |


## <a name="related-azure-powershell-cmdlets"></a>相關 Azure PowerShell Cmdlet

下列 Azure PowerShell Cmdlet 可以用來建立和管理 IP 群組：

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [移除-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [新增-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [新增-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>後續步驟

- 瞭解如何[部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。