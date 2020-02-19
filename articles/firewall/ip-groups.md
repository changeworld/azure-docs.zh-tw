---
title: Azure 防火牆中的 IP 群組
description: IP 群組可讓您分組和管理 Azure 防火牆規則的 IP 位址。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444481"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Azure 防火牆中的 IP 群組（預覽）

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

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

您可以使用 Azure 入口網站、Azure CLI 或 REST API 來建立 IP 群組。 如需詳細資訊，請參閱[建立 IP 群組（預覽）](create-ip-group.md)。

## <a name="browse-ip-groups"></a>流覽 IP 群組
1. 在 [Azure 入口網站搜尋] 列中，輸入**IP 群組**並加以選取。 您可以看到 IP 群組的清單，或選取 [**新增**] 來建立新的 ip 群組。
2. 選取 IP 群組以開啟 [總覽] 頁面。 您可以編輯、新增或刪除 IP 位址或 IP 群組。

   ![IP 群組總覽](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>管理 IP 群組

您可以看到 IP 群組中的所有 IP 位址，以及與其相關聯的規則或資源。 若要刪除 IP 群組，您必須先將 IP 群組與使用它的資源中斷關聯。

1. 若要查看或編輯 IP 位址，請在左窗格中選取 [**設定**] 下的 [ **ip 位址**]。
2. 若要新增單一或多個 IP 位址，請選取 [**新增 Ip 位址**]。 這會開啟 [上傳] 的 [**拖曳] 或 [流覽]** 頁面，您也可以手動輸入位址。
3.  選取右邊的省略號（ **...** ），以編輯或刪除 IP 位址。 若要編輯或刪除多個 IP 位址，請選取方塊，然後選取頂端的 [**編輯**] 或 [**刪除**]。
4. 最後，可以將檔案匯出成 CSV 檔案格式。

> [!NOTE]
> 如果您刪除 IP 群組中的所有 IP 位址，但規則仍在使用中，則會略過該規則。


## <a name="use-an-ip-group"></a>使用 IP 群組

當您建立 Azure 防火牆 DNAT、應用程式或網路規則時，您現在可以選取**Ip 群組**做為 ip 位址的**來源類型**或**目的地類型**。

> [!NOTE]
> 防火牆原則中不支援 IP 群組。 目前僅支援傳統的防火牆規則。

![防火牆中的 IP 群組](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>區域可用性

IP 群組目前可在下欄區域使用：

- 美國西部
- 美國西部 2
- 美國東部
- 美國東部 2
- 美國中部
- 美國中北部
- 美國中西部
- 美國中南部
- 加拿大中部
- 北歐
- 西歐
- 法國中部
- 英國南部
- 澳大利亞東部
- 澳大利亞中部
- 澳大利亞東南部

## <a name="related-azure-powershell-cmdlets"></a>相關 Azure PowerShell Cmdlet

下列 Azure PowerShell Cmdlet 可以用來建立和管理 IP 群組：

- [新增-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [移除-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [設定-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [新增-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [新增-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>後續步驟

- 深入了解如何[部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。