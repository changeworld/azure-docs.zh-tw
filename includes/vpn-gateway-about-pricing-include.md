---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/08/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8f8d366961049deb3eda193718ccb553aac930e3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "67666359"
---
您需要支付兩件事︰虛擬網路閘道的每小時計算成本，以及虛擬網路閘道的輸出資料傳輸。 在 [價格](https://azure.microsoft.com/pricing/details/vpn-gateway) 頁面上可以找到價格資訊。 如需舊版閘道 SKU 定價，請參閱 [ExpressRoute 定價頁面](https://azure.microsoft.com/pricing/details/expressroute)，然後捲動至 [虛擬網路閘道]  區段。

**虛擬網路閘道計算成本**<br>每個虛擬網路閘道都有每小時計算成本。 價格是以您建立虛擬網路閘道時所指定的閘道 SKU 為基礎。 除了透過閘道流動的資料傳輸以外，此成本屬於閘道本身。 主動-主動設定的成本與主動-被動相同。

**資料傳輸成本**<br>資料傳輸成本是根據來源虛擬網路閘道的輸出流量來計算。

* 如果您將流量傳送到內部部署 VPN 裝置，則會以網際網路輸出資料傳輸費率收費。
* 如果您是傳送不同區域中的虛擬網路之間的流量，則會依據區域定價。
* 如果您只是傳送相同區域中的虛擬網路之間的流量，則沒有資料成本。 相同區域中 VNet 之間的流量是免費的。
