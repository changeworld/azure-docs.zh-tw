---
title: Azure VMware Solution by CloudSimple-網路檢查清單
description: 在 Azure VMware Solution by CloudSimple 配置網路 CIDR 的檢查清單
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77025005"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple 的網路必要條件

Azure VMware Solution by CloudSimple 提供 VMware 私用雲端環境，可供來自內部部署環境、企業管理裝置和 Azure 資源的使用者和應用程式存取。 連線是透過網路服務（例如 Vpn 和 Azure ExpressRoute 連線）來傳遞。 其中一些網路服務會要求您指定網路位址範圍來啟用服務。 

本文中的表格描述使用指定位址的一組位址範圍和對應的服務。 某些位址是必要的，有些則取決於您想要部署的服務。 這些位址空間不應與任何內部部署子網、Azure 虛擬網路子網或計畫的 CloudSimple 工作負載子網重迭。

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>建立私人雲端所需的網路位址範圍

在建立 CloudSimple 服務和私人雲端期間，您必須符合指定的網路無類別網域間路由 (CIDR) 範圍，如下所示。

| 名稱/用於     | 描述                                                                                                                            | 位址範圍            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| 閘道 CIDR      |  (VPN 閘道) 的 edge 服務所需。  在 CloudSimple 服務建立期間必須要有此 CIDR，而且必須來自 RFC 1918 空間。 | /28                      |
| vSphere/vSAN CIDR | VMware 管理網路的必要元件。 在私人雲端建立期間，必須指定此 CIDR。                                    | /24 或/23 或/22 或/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>連線至內部部署網路的 Azure 網路所需的網路位址範圍

[透過 ExpressRoute 從內部部署網路連線到私人雲端網路，可](on-premises-connection.md)建立全球連線。  連線會使用邊界閘道協定 (BGP) ，在您的內部部署網路、私人雲端網路和 Azure 網路之間交換路由。

| 名稱/用於             | 描述                                                                                                                                                                             | 位址範圍 |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute 對等互連 CIDR | 當您針對內部部署連線使用 ExpressRoute 全球存取範圍時需要此功能。 當透過支援票證提出全球觸達連線要求時，必須提供此 CIDR。 | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>使用站對站 VPN 連線至內部部署網路所需的網路位址範圍

[使用站對站 VPN 從內部部署網路連線到私人雲端網路](vpn-gateway.md)時，需要下列 IP 位址、內部部署網路和識別碼。 

| 位址/位址範圍 | 描述                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 對等 IP               | 內部部署 VPN 閘道的公用 IP 位址。 建立內部部署資料中心與 CloudSimple 服務區域之間的站對站 VPN 連線所需。 建立站對站 VPN 閘道時，需要此 IP 位址。                                         |
| 對等識別碼       | 內部部署 VPN 閘道的對等識別碼。 這通常與 **對等 IP**相同。  如果您的內部部署 VPN 閘道上已指定唯一識別碼，則必須指定識別碼。  建立站對站 VPN 閘道時，必須要有對等識別碼。   |
| 內部部署網路   | 區域中需要存取 CloudSimple 網路的內部部署首碼。  包含將存取 CloudSimple 網路之內部部署網路中的所有前置詞，包括使用者將在其中存取網路的用戶端網路。                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>使用點對站 VPN 連接所需的網路位址範圍

點對站 VPN 連線可讓您從用戶端電腦存取 CloudSimple 網路。  [若要設定點對站 VPN](vpn-gateway.md)，您必須指定下列網路位址範圍。

| 位址/位址範圍 | 描述                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 用戶端子網         | 當您使用點對站 VPN 進行連線時，用戶端子網會提供 DHCP 位址。 當您在 CloudSimple 入口網站上建立點對站 VPN 閘道時，需要這個子網。  網路分成兩個子網;一個用於 UDP 連接，另一個用於 TCP 連接。 |

## <a name="next-steps"></a>後續步驟

* [用於存取私用雲端的內部部署防火牆設定](on-premises-firewall-configuration.md)
* [快速入門-建立 CloudSimple 服務](quickstart-create-cloudsimple-service.md)
* [快速入門-設定私人雲端](quickstart-create-private-cloud.md)
* 深入瞭解 [Azure 網路連接](cloudsimple-azure-network-connection.md)
* 深入瞭解 [VPN 閘道](cloudsimple-vpn-gateways.md)
