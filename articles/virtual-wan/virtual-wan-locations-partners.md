---
title: Azure 虛擬 WAN 夥伴和位置 |Microsoft Docs
description: 本文包含 Azure 虛擬 WAN 夥伴和中樞位置的清單。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 928a68cff5dc8043e69c25be3dcfa3510a7d3a2a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267290"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>虛擬 WAN 夥伴與虛擬中樞位置

本文提供虛擬 WAN 支援區域和合作夥伴的相關資訊，以連接到虛擬中樞。

Azure 虛擬 WAN 是一種網路服務，透過 Azure 提供最佳且自動化的分支對分支連線。 虛擬 WAN 可讓您連線，並設定與 Azure 通訊的分支裝置。 這可以手動方式完成，或透過虛擬 WAN 夥伴使用提供者裝置來完成。 使用合作夥伴裝置可讓您輕鬆使用、簡化連線能力和設定管理。

從內部部署裝置連線到虛擬中樞是透過自動化方式建立。 虛擬中樞是受 Microsoft 管理的虛擬網路。 中樞包含不同的服務端點，可啟用您內部部署網路 (vpnsite) 中的連線。 每個地區都只能有一個中樞。

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>從合作夥伴分支 IPSec 連線能力自動化

連接到 Azure 虛擬 WAN 的裝置已內建自動化連接能力。 這通常是在裝置管理 UI (或相同功能) 中設定，它可在 VPN 分支裝置與 Azure 虛擬中樞 VPN 端點 (VPN 閘道) 之間設定連線和組態管理。

下列高階自動化是在裝置主控台/管理中心中設定：

* 裝置擁有存取 Azure 虛擬 WAN 資源群組的適當權限
* 將分支裝置上傳到 Azure 虛擬 WAN
* 自動下載 Azure 的連線資訊
* 內部部署分支裝置的設定 

有些連線夥伴可能會擴充自動化功能以包括建立 Azure 虛擬中樞 VNet 與 VPN 閘道的能力。 如果您想要深入瞭解自動化，請參閱 [虛擬 WAN 合作夥伴的自動化指導方針](virtual-wan-configure-automation-providers.md)。

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>分支 IPSec 連線合作夥伴

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

下列合作夥伴會在未來的藍圖中規劃：128技術、Arista、Cisco Systems (Viptela) 、F5 Networks、Oracle SD-WAN 和 SharpLink。

## <a name="partners-with-integrated-virtual-hub-offerings"></a>具有整合式虛擬中樞供應專案的合作夥伴
除了具有自動化的分公司 IPSec 連線能力之外，某些夥伴還提供 **網路虛擬裝置 (nva) ** ，可直接整合到 AZURE 虛擬 WAN 中樞。  這可讓客戶選擇將其分支連線終止至虛擬中樞內的相容協力廠商應用裝置。  

在虛擬 WAN Hub 中提供 NVA 的合作夥伴必須：

* 已從其分支裝置執行 IPSec 連線能力自動化，並已將其 NVA 供應專案上線至 Azure 虛擬 WAN 中樞。
* Azure Marketplace 中有現有的網路虛擬裝置供應專案可供使用。

如果您是合作夥伴，而且有關于虛擬中樞供應專案中受控 NVA 的問題，請傳送電子郵件給我們 vwannvaonboarding@microsoft.com

## <a name="integrated-virtual-hub-nva-partners"></a>整合式虛擬中樞 NVA 合作夥伴
這些合作夥伴具有 **受控應用程式** 供應專案，現在可供部署到虛擬 WAN 中樞。

|合作夥伴|設定/操作說明/部署指南|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Barracuda CloudGen WAN 部署指南](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco Cloud Service 路由器 (CSR) VWAN](https://aka.ms/ciscoMarketPlaceOffer)| [Cisco Cloud Service 路由器 (CSR) VWAN 部署指南]()

下列合作夥伴預定在不久的未來將 NVA 帶入虛擬中樞供應專案： Citrix，反之亦然： Networks 和 VeloCloud。

## <a name="locations"></a><a name="locations"></a>位置

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>後續步驟

* 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 常見問題集](virtual-wan-faq.md)。

* 如需如何將 Azure 虛擬 WAN 的連線自動化的詳細資訊，請參閱 [虛擬 wan 合作夥伴的自動化指導方針](virtual-wan-configure-automation-providers.md)。
