---
title: Azure 防火牆強制隧道
description: 您可以配置強制隧道，將 Internet 綁定流量路由到其他防火牆或網路虛擬裝置，以便進一步處理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597266"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure 防火牆強制隧道（預覽）

您可以配置 Azure 防火牆，將所有 Internet 綁定流量路由到指定的下一個躍點，而不是直接轉到 Internet。 例如，您可能有一個本地邊緣防火牆或其他網路虛擬裝置 （NVA） 來處理網路流量，然後再將其傳遞到 Internet。

> [!IMPORTANT]
> Azure 防火牆強制隧道當前處於公共預覽版中。
>
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 特定功能可能不受支援、功能可能受限，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

預設情況下，Azure 防火牆上不允許強制隧道化，以確保滿足其所有出站 Azure 依賴項。 禁用*Azure 防火牆子網*上預設路由不直接轉到 Internet 的使用者定義路由 （UDR） 配置。

## <a name="forced-tunneling-configuration"></a>強制通道設定

為了支援強制隧道，服務管理流量與客戶流量分離。 使用其關聯的公共 IP 位址，需要一個名為*Azure 防火牆管理子網*（最小子網大小 /26）的其他專用子網。 此子網上允許的唯一路由是到 Internet 的預設路由，並且必須禁用 BGP 路由傳播。

如果通過 BGP 通告的預設路由以強制流量到本地，則必須在部署防火牆之前創建*Azure 防火牆子網*和*Azure 防火牆管理子網*，並且具有預設路由到 Internet 的 UDR 和禁用**虛擬網路閘道路由傳播**。

在此配置中 *，Azure 防火牆 Subnet*現在可以包括路由到任何本地防火牆或 NVA，用於在流量傳遞到 Internet 之前處理流量。 如果在此子網上啟用**了虛擬網路閘道路由傳播**，還可以通過 BGP 將這些路由發佈到*Azure 防火牆子網*。

例如，您可以在*Azure 防火牆 Subnet*上創建預設路由，將 VPN 閘道作為下一個要跳以到達本地設備。 或者，您可以啟用**虛擬網路閘道路由傳播**，以獲取到本地網路的適當路由。

![虛擬網路閘道路由傳播](media/forced-tunneling/route-propagation.png)

將 Azure 防火牆配置為支援強制隧道化後，無法撤銷配置。 如果刪除防火牆上的所有其他 IP 配置，管理 IP 配置也被刪除，防火牆也已處理。 無法刪除分配給管理 IP 配置的公共 IP 位址，但您可以分配其他公共 IP 位址。

## <a name="next-steps"></a>後續步驟

- [教程：使用 Azure 門戶在混合網路中部署和配置 Azure 防火牆](tutorial-hybrid-portal.md)