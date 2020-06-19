---
title: 使用 Azure 入口網站開放對 VM 的連接埠
description: 了解如何使用 Azure 入口網站對 Windows VM 開放連接埠 / 建立端點
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/27/2020
ms.author: cynthn
ms.openlocfilehash: 12db42b0edb3d3e27756593f3dfb079804a4145f
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170019"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>如何使用 Azure 入口網站開啟虛擬機器的連接埠
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>登入 Azure
在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-network-security-group"></a>建立網路安全性群組

1. 搜尋並選取 VM 的資源群組，選擇 [新增]，然後搜尋並選取 [網路安全性群組]。

1. 選取 [建立]。

    [建立網路安全性群組] 視窗隨即開啟。

    ![建立網路安全性群組](./media/nsg-quickstart-portal/create-nsg.png)

1. 輸入網路安全性群組的名稱。 

1. 選取或建立資源群組，然後選取位置。

1. 選取 [建立] 以建立網路安全性群組。

## <a name="create-an-inbound-security-rule"></a>建立輸入安全性規則

1. 選取您的新網路安全性群組。 

1. 從左側功能表中選取 [輸入安全性規則]，然後選取 [新增]。

    ![切換至進階頁面](./media/nsg-quickstart-portal/advanced.png)

1. 在 [新增輸入安全性規則] 頁面上，從頁面頂端的 [基本] 切換至 [進階]。 

1. 從下拉式功能表中選擇常見的**服務**，例如 **HTTP**。 如果要以提供特定連接埠供使用者使用，您也可以選取 [自訂]。 

1. 選擇性地變更 [優先順序] 或 [名稱]。 優先順序會影響規則的套用順序，數值越低的規則越早套用。

1. 選取 [新增] 以建立規則。

## <a name="associate-your-network-security-group-with-a-subnet"></a>將「網路安全性群組」與子網路建立關聯

最後一個步驟是將您的網路安全性群組與子網路或特定網路介面建立關聯。 對於此範例，我們會將網路安全性群組與子網路建立關聯。 

1. 從左側功能表中選取 [子網路]，然後選取 [關聯]。

1. 選取您的虛擬網路，然後選取適當的子網路。

    ![將網路安全性群組與虛擬網路功能建立關聯](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. 完成後，選取 [確定]。

## <a name="additional-information"></a>其他資訊

您也可以[使用 Azure PowerShell 執行本文中的步驟](nsg-quickstart-powershell.md)。

本文中所述的命令可讓您快速取得傳輸到 VM 的流量。 「網路安全性群組」針對控制對您資源的存取，提供許多絕佳的功能和細微性。 如需詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](../../virtual-network/tutorial-filter-network-traffic.md)。

如需高可用性 Web 應用程式，請考慮將您的 VM 置於 Azure Load Balancer 後方。 負載平衡器會將流量分散到所有 VM，且有提供流量篩選的網路安全性群組。 如需詳細資訊，請參閱[如何平衡 Azure 中 Windows 虛擬機器的負載以建立高可用性應用程式](tutorial-load-balancer.md)。

## <a name="next-steps"></a>後續步驟
在本文中，您會建立網路安全性群組、建立允許連接埠 80 上 HTTP 流量的輸入規則，然後將該規則與子網路建立關聯。 

您可以從下列文章中，找到有關建立更詳細環境的資訊︰
- [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)
- [安全性群組](../../virtual-network/security-overview.md)
