---
title: Azure ExpressRoute：將閘道新增至 VNet： PowerShell
description: 本文會協助您將 VNet 閘道新增到已經建立的 ExpressRoute 的 Resource Manager VNet。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74037420"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>使用 PowerShell 為 ExpressRoute 設定虛擬網路閘道
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [傳統 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [影片-Azure 入口網站](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

本文會協助您為既存的 VNet 新增虛擬網路 (VNet) 閘道、調整該閘道大小及移除該閘道。 此設定的步驟適用于使用 Resource Manager 部署模型為 ExpressRoute 設定所建立的 Vnet。 如需詳細資訊，請參閱[關於 ExpressRoute 的虛擬網路閘道](expressroute-about-virtual-network-gateways.md)。

## <a name="before-beginning"></a>開始之前

### <a name="working-with-powershell"></a>使用 PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>設定參考清單

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>後續步驟
建立 VNet 閘道之後，您可以將 VNet 連結至 ExpressRoute 循環。 請參閱 [將虛擬網路連結到 ExpressRoute 循環](expressroute-howto-linkvnet-arm.md)。
