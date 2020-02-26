---
title: Azure 防火牆強制通道
description: 您可以設定強制通道，將網際網路系結流量路由傳送至額外的防火牆或網路虛擬裝置，以供進一步處理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597266"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure 防火牆強制通道（預覽）

您可以設定 Azure 防火牆，將所有網際網路系結流量路由傳送至指定的下一個躍點，而不是直接前往網際網路。 例如，您可能有內部部署邊緣防火牆或其他網路虛擬裝置（NVA），可以在將網路流量傳遞至網際網路之前，先處理它。

> [!IMPORTANT]
> Azure 防火牆強制通道目前為公開預覽版。
>
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 特定功能可能不受支援、功能可能受限，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

根據預設，Azure 防火牆不允許強制通道，以確保符合其所有的輸出 Azure 相依性。 *AzureFirewallSubnet*上的使用者定義路由（UDR）設定若未直接傳送至網際網路，則會停用其預設路由。

## <a name="forced-tunneling-configuration"></a>強制通道設定

為了支援強制通道，服務管理流量會與客戶流量分開。 需要另一個名為*AzureFirewallManagementSubnet* （最小子網大小/26）的專用子網，而且其本身有相關聯的公用 IP 位址。 這個子網上唯一允許的路由是網際網路的預設路由，且必須停用 BGP 路由傳播。

如果您有透過 BGP 通告的預設路由，以強制流量至內部部署，您必須先建立*AzureFirewallSubnet*和*AzureFirewallManagementSubnet* ，然後再部署防火牆，並將 UDR 設為網際網路的預設路由，且**虛擬網路閘道路由傳播**已停用。

在此設定中， *AzureFirewallSubnet*現在可以包含任何內部部署防火牆或 NVA 的路由，以在傳遞至網際網路之前處理流量。 如果已在此子網上啟用**虛擬網路閘道路由傳播**，您也可以透過 BGP 將這些路由發佈至*AzureFirewallSubnet* 。

例如，您可以在*AzureFirewallSubnet*上建立預設路由，並將您的 VPN 閘道做為下一個躍點，以前往您的內部部署裝置。 或者，您可以啟用**虛擬網路閘道路由傳播**，以取得適用于內部部署網路的適當路由。

![虛擬網路閘道路由傳播](media/forced-tunneling/route-propagation.png)

一旦您設定 Azure 防火牆以支援強制通道，就無法復原設定。 如果您移除防火牆上的所有其他 IP 設定，系統也會移除管理 IP 設定，並解除配置防火牆。 指派給管理 IP 設定的公用 IP 位址無法移除，但您可以指派不同的公用 IP 位址。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure 入口網站在混合式網路中部署及設定 Azure 防火牆](tutorial-hybrid-portal.md)