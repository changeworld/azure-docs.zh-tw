---
title: Azure 虛擬 WAN 和遠端工作
description: 本頁介紹如何利用 Azure 虛擬 WAN 啟用由於 COVID-19 大流行而遠端工作。
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: article
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: ce212b5da90906966025674b58884d0e2f5bb064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337134"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure 虛擬 WAN 和支援遠端工作

>[!NOTE]
>本文介紹如何利用 Azure 虛擬 WAN、Azure、Microsoft 網路和 Azure 合作夥伴生態系統遠端工作並緩解因 COVID-19 危機而面臨的網路問題。
>

您是否爭先恐後地為遠端使用者提供連接？
您是否突然看到需要支援超出計畫的使用者激增？
您是否需要使用者從家裡進行連接，不僅訪問雲，而且能夠到達本地？
您是否需要遠端使用者能夠訪問專用廣域網路背後的資源？
您是否需要使用者訪問雲內部資源，而無需設置區域之間的連接？
由於此全球大流行在我們周圍創造了前所未有的變化，Azure 虛擬 WAN 團隊在這裡為您滿足您的連接需求。

Azure 虛擬 WAN 是一種網路服務，它彙集了許多網路、安全和路由功能，以提供單個操作介面。 這些功能包括分支連接（通過虛擬 WAN 合作夥伴設備（如 SD-WAN 或 VPN CPE）的連接自動化）、網站到網站 VPN 連接、遠端使用者 VPN（點對點）連接、專用（快速路由）連接、雲內連接（虛擬網路的傳輸連接）、VPN 快速路由互連、路由、Azure 防火牆、專用連線加密等。您不必擁有所有這些用例，就無需開始使用虛擬 WAN。 您可以開始使用一個用例，並隨著網路的發展進行調整。

![虛擬 WAN 的圖表](./media/virtual-wan-about/virtualwan1.png)

現在，在談論遠端使用者，讓我們來看看需要什麼來啟動和運行您的網路：

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>設置遠端使用者連接

您可以透過 IPsec/IKE (IKEv2) 或 OpenVPN 連線，來連線到您在 Azure 中的資源。 這種類型的連接需要為遠端使用者配置 VPN 用戶端。 此用戶端可以是[Azure VPN 用戶端](https://go.microsoft.com/fwlink/?linkid=2117554)或 OpenVPN 用戶端或支援 IKEv2 的任何用戶端。 如需詳細資訊，請參閱[建立點對站連線](virtual-wan-point-to-site-portal.md)。

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>從遠端使用者到本地的連接

這裡有兩個選項：

* 與任何現有 VPN 設備佈建網站到網站的連接。 將 IPsec VPN 設備連接到 Azure 虛擬廣域網路中心時，點對點使用者 VPN（遠端使用者）和網站到網站 VPN 之間的互連是自動的。 有關如何從本地 VPN 設備到 Azure 虛擬 WAN 佈建網站到網站 VPN 的詳細資訊，請參閱[使用虛擬 WAN 創建網站到網站的連接](virtual-wan-site-to-site-portal.md)。

* 將快速路由電路連接到虛擬廣域網路集線器。 連接快速路由電路需要在虛擬廣域網路中部署快速路由閘道。 部署一個網站使用者 VPN 和 ExpressRoute 使用者之間的互連是自動的。 要創建快速路由連接，請參閱[使用虛擬 WAN 創建快速路由連接](virtual-wan-expressroute-portal.md)。 您可以使用現有的 ExpressRoute 電路連接到 Azure 虛擬廣域網路。

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>現有基本虛擬廣域網路客戶

基本虛擬 WAN 僅提供網站到網站 VPN。 為了使遠端使用者進行連接，您需要將虛擬 WAN 升級到標準虛擬廣域網路。 有關升級虛擬 WAN 的步驟，請參閱[將虛擬 WAN 從基本 WAN 升級到標準](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>其他資訊

虛擬 WAN 支援每個區域/位置一個集線器。 如需位置資訊，請參閱[虛擬 WAN 夥伴與位置](virtual-wan-locations-partners.md)一文。 每個集線器支援多達 10，000 個遠端使用者連接、1，000 個分支連接、4 個 ExpressRoute 電路和多達 500 個虛擬網路連接。 在擴展遠端使用者時，如果您有任何問題，請毫不猶豫地向 發送電子郵件尋求説明azurevirtualwan@microsoft.com。 如果需要技術支援，請確保從 Azure 門戶打開支援票證，並且説明即將提供。

## <a name="faq"></a><a name="faq"></a>常見問題集

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>後續步驟

有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 概述](virtual-wan-about.md)