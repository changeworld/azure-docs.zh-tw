---
title: Azure ExpressRoute：將閘道新增至 VNet：傳統
description: 針對 ExpressRoute 組態，使用 PowerShell 設定傳統部署模型 VNet 的 VNet 閘道。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 0f42a7d030f45be1b96728fdcac7a13cf797ed34
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396398"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>使用 PowerShell 設定 ExpressRoute 的虛擬網路閘道閘道 (傳統)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [傳統 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [影片-Azure 入口網站](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

本文將逐步引導您完成既存 VNet 的虛擬網路 (VNet) 閘道加入、重新調整和移除步驟。 此組態的步驟是使用 **傳統部署模型** 建立的 VNet 專用，且將用於 ExpressRoute 組態中。 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**關於 Azure 部署模型**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>開始之前
確認您已安裝此設定所需的 Azure PowerShell Cmdlet。

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>接下來的步驟
建立 VNet 閘道之後，您可以將 VNet 連結至 ExpressRoute 循環。 請參閱 [將虛擬網路連結到 ExpressRoute 循環](expressroute-howto-linkvnet-classic.md)。

