---
title: 設定公用 IP 位址的路由喜好設定 - Azure 入口網站
description: 了解如何使用網際網路流量路由喜好設定來建立公用 IP
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: mnayak
ms.openlocfilehash: ef1e33a2e43f26dcaf794b2ed81f27d39639b52d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533973"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>使用 Azure 入口網站設定公用 IP 位址的路由喜好設定

本文說明如何透過 ISP network (**Internet** 選項) 針對公用 IP 位址設定 [路由喜好](https://docs.microsoft.com/azure/virtual-network/routing-preference-overview)設定。 建立公用 IP 位址之後，您可以將其與下列 Azure 資源建立關聯，以取得連至網際網路的輸入和輸出流量：

* 虛擬機器
* 虛擬機器擴展集
* Azure Kubernetes Service (AKS)
* 網際網路對應負載平衡器
* 應用程式閘道
* Azure 防火牆

根據預設，公用 IP 位址的路由喜好設定會設為所有 Azure 服務的 Microsoft 全球網路，而且可與任何 Azure 服務相關聯。

> [!IMPORTANT]
> 路由喜好設定目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果您沒有 Azure 訂用帳戶，請立即建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>使用路由喜好設定建立公用 IP 位址
1. 登入 [Azure 入口網站](https://preview.portal.azure.com/)。
2. 選取 [建立資源]。 
3. 在搜尋方塊中，輸入「公用 IP 位址」。
3. 在搜尋結果中，選取「公用 IP 位址」。 接下來，在 [公用 IP 位址] 頁面中，選取 [建立]。
3. 在 [路由喜好設定] 選項中，選取 [網際網路]。

      ![建立公用 IP 位址](./media/routing-preference-portal/pip-new.png)

    > [!NOTE]
    > 公用 IP 位址是使用 IPv4 或 IPv6 位址所建立的。 不過，路由喜好設定目前僅支援 IPV4。

您可以將上述建立的公用 IP 位址與 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器建立關聯。 使用教學課程頁面上的 CLI 章節：[將公用 IP 位址與虛擬機器建立關聯](associate-public-ip-address-vm.md#azure-cli)，以將公用 IP 與您的 VM 產生關聯。 您也可以將公用 IP 位址指派給負載平衡 **前端** 組態，將上述建立的公用 IP 位址與 [Azure Load Balancer](../load-balancer/load-balancer-overview.md) 建立關聯。 此公用 IP 位址作為負載平衡的虛擬 IP 位址 (VIP)。

## <a name="next-steps"></a>後續步驟
- 深入了解[使用路由喜好設定的公用 IP](routing-preference-overview.md)。
- [設定 VM 的路由喜好設定](tutorial-routing-preference-virtual-machine-portal.md)。
- [使用 PowerShell 設定公用 IP 位址的路由喜好設定](routing-preference-powershell.md)。
- 深入了解 Azure 中的[公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 深入了解所有[公用 IP 位址設定](virtual-network-public-ip-address.md#create-a-public-ip-address)。
