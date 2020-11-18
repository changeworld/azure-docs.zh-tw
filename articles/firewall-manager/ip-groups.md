---
title: Azure 防火牆原則中的 IP 群組
description: IP 群組可讓您針對 Azure 防火牆原則規則來分組及管理 IP 位址。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 921a8b6c808d4eef2b02ef0580d5b2ea9045366d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659500"
---
# <a name="ip-groups-in-azure-firewall-policy"></a>Azure 防火牆原則中的 IP 群組

IP 群組可讓您透過下列方式來分組及管理 Azure 防火牆原則的 IP 位址：

- 作為 DNAT 規則中的來源類型
- 作為網路規則中的來源或目的地類型
- 作為應用程式規則中的來源類型


IP 群組可以有單一 IP 位址、多個 IP 位址，或一或多個 IP 位址範圍。

您可以在 azure 中跨區域和訂用帳戶的 Azure 防火牆 DNAT、網路和應用程式規則中重複使用 IP 群組。 組名必須是唯一的。 您可以在 Azure 入口網站、Azure CLI 或 REST API 中設定 IP 群組。 提供範例範本以協助您開始使用。

## <a name="sample-format"></a>樣本格式

下列 IPv4 位址格式範例適用于 IP 群組：

- 單一位址：10.0.0。0
- CIDR 標記法： 10.1.0.0/32
- 位址範圍： 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>建立 IP 群組

您可以使用 Azure 入口網站、Azure CLI 或 REST API 來建立 IP 群組。 如需詳細資訊，請參閱 [建立 IP 群組](../firewall/create-ip-group.md)。

## <a name="browse-ip-groups"></a>流覽 IP 群組
1. 在 Azure 入口網站搜尋列中，輸入 **IP 群組** 並加以選取。 您可以看到 IP 群組清單，也可以選取 [ **新增** ] 來建立新的 ip 群組。
2. 選取 IP 群組以開啟 [總覽] 頁面。 您可以編輯、新增或刪除 IP 位址或 IP 群組。

   ![IP 群組總覽](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>管理 IP 群組

您可以看到 IP 群組中的所有 IP 位址，以及與其相關聯的規則或資源。 若要刪除 IP 群組，您必須先將 IP 群組與使用它的資源中斷關聯。

1. 若要查看或編輯 IP 位址，請在左窗格的 [**設定**] 底下選取 [ **ip 位址**]。
2. 若要將單一或多個 IP 位址新增 (es) ，請選取 [ **新增 IP 位址**]。 這會開啟 [ **拖曳] 或 [流覽]** 頁面進行上傳，您也可以手動輸入位址。
3.    選取省略號 (**...**) 至右邊以編輯或刪除 IP 位址。 若要編輯或刪除多個 IP 位址，請選取方塊，然後選取頂端的 [ **編輯** ] 或 [ **刪除** ]。
4. 最後，可以匯出 CSV 檔案格式的檔案。

> [!NOTE]
> 如果您刪除 IP 群組中的所有 IP 位址，但在規則中仍在使用中，則會略過該規則。


## <a name="use-an-ip-group"></a>使用 IP 群組

您現在可以在建立具有 DNAT、應用程式或網路規則的原則時，選取 **Ip 群組** 作為 ip 位址 (es) 的 **來源類型** 或 **目的地類型** 。

:::image type="content" source="media/ip-groups/firewall-ip-group.png" alt-text="防火牆中的 IP 群組":::

## <a name="ip-address-limits"></a>IP 位址限制

每個防火牆最多可有100個 IP 群組，每個 IP 群組最多有5000個個別 IP 位址或 IP 首碼。

## <a name="related-azure-powershell-cmdlets"></a>相關的 Azure PowerShell Cmdlet

下列 Azure PowerShell Cmdlet 可以用來建立和管理 IP 群組：

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [移除-AzIPGroup](/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [新 AzFirewallPolicyNetworkRule](/powershell/module/az.network/new-azfirewallpolicynetworkrule?view=azps-3.4.0)
- [新 AzFirewallPolicyApplicationRule](/powershell/module/az.network/new-azfirewallpolicyapplicationrule?view=azps-3.4.0)
- [新 AzFirewallPolicyNatRule](/powershell/module/az.network/new-azfirewallpolicynatrule?view=azps-3.4.0)

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure 防火牆管理員來保護您的虛擬 WAN](secure-cloud-network.md)