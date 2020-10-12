---
title: Azure 防火牆中的 IP 群組
description: IP 群組可讓您為 Azure 防火牆規則分組及管理 IP 位址。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 97d8d10e30d0d0c1654c82651220489785a37059
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87460213"
---
# <a name="ip-groups-in-azure-firewall"></a>Azure 防火牆中的 IP 群組

IP 群組可讓您透過下列方式，為 Azure 防火牆規則分組及管理 IP 位址：

- 作為 DNAT 規則中的來源位址
- 作為網路規則中的來源或目的地位址
- 作為應用程式規則中的來源位址


IP 群組可以有單一 IP 位址、多個 IP 位址，或一或多個 IP 位址範圍。

您可以在 azure 中跨區域和訂用帳戶的 Azure 防火牆 DNAT、網路和應用程式規則中重複使用 IP 群組。 組名必須是唯一的。 您可以在 Azure 入口網站、Azure CLI 或 REST API 中設定 IP 群組。 提供範例範本以協助您開始使用。

## <a name="sample-format"></a>樣本格式

下列 IPv4 位址格式範例適用于 IP 群組：

- 單一位址：10.0.0。0
- CIDR 標記法： 10.1.0.0/32
- 位址範圍： 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>建立 IP 群組

您可以使用 Azure 入口網站、Azure CLI 或 REST API 來建立 IP 群組。 如需詳細資訊，請參閱 [建立 IP 群組](create-ip-group.md)。

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

您現在可以在建立 Azure 防火牆 DNAT、應用程式或網路規則時，選取 ip **群組** 作為 ip 位址 (es) 的 **來源類型** 或 **目的地類型** 。

![防火牆中的 IP 群組](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>區域可用性

IP 群組適用于所有公用雲端區域。

## <a name="ip-address-limits"></a>IP 位址限制

每個防火牆最多可有100個 IP 群組，每個 IP 群組最多有5000個個別 IP 位址或 IP 首碼。

## <a name="related-azure-powershell-cmdlets"></a>相關的 Azure PowerShell Cmdlet

下列 Azure PowerShell Cmdlet 可以用來建立和管理 IP 群組：

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [移除-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [新 AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [新 AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>接下來的步驟

- 瞭解如何 [部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。