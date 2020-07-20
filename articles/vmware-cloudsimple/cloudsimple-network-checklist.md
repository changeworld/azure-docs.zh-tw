---
title: 依 CloudSimple 的 Azure VMware 解決方案-網路檢查清單
description: 透過 CloudSimple 在 Azure VMware 解決方案上配置網路 CIDR 的檢查清單
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77025005"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple 的網路必要條件

Azure VMware Solution by CloudSimple 提供 VMware 私用雲端環境，可供來自內部部署環境、企業管理的裝置和 Azure 資源的使用者和應用程式存取。 透過網路服務（例如 Vpn 和 Azure ExpressRoute 連線）來傳遞連線能力。 其中一些網路服務會要求您指定用來啟用服務的網路位址範圍。 

本文中的表格說明使用指定位址的位址範圍和對應服務的集合。 有些位址是必要的，有些則取決於您想要部署的服務。 這些位址空間不應與您的任何內部部署子網、Azure 虛擬網路子網或規劃的 CloudSimple 工作負載子網重迭。

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>建立私人雲端所需的網路位址範圍

在建立 CloudSimple 服務和私人雲端時，您必須遵守指定的網路無類別網域間路由（CIDR）範圍，如下所示。

| 名稱/用於     | Description                                                                                                                            | 位址範圍            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| 閘道 CIDR      | Edge 服務（VPN 閘道）的必要。  在 CloudSimple 服務建立期間需要此 CIDR，而且必須來自 RFC 1918 空間。 | /28                      |
| vSphere/vSAN CIDR | VMware 管理網路的必要。 在建立私人雲端期間，必須指定此 CIDR。                                    | /24 或/23 或/22 或/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>內部部署網路的 Azure 網路連線所需的網路位址範圍

[透過 ExpressRoute 從內部部署網路連線到私人雲端網路](on-premises-connection.md)，會建立全球觸達連線。  此連線會使用邊界閘道協定（BGP），在您的內部部署網路、私人雲端網路和 Azure 網路之間交換路由。

| 名稱/用於             | Description                                                                                                                                                                             | 位址範圍 |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute 對等互連 CIDR | 當您使用 ExpressRoute Global 觸達內部部署連線能力時所需。 當透過支援票證提出的全球觸達連線要求時，必須提供此 CIDR。 | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>使用內部部署網路的站對站 VPN 連線所需的網路位址範圍

[使用站對站 VPN 從內部部署網路連線到私人雲端網路](vpn-gateway.md)時，需要下列 IP 位址、內部部署網路和識別碼。 

| 位址/位址範圍 | Description                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 對等 IP               | 內部部署 VPN 閘道的公用 IP 位址。 需要在內部部署資料中心與 CloudSimple 服務區域之間建立站對站 VPN 連線。 在建立站對站 VPN 閘道期間，需要此 IP 位址。                                         |
| 對等識別碼       | 內部部署 VPN 閘道的對等識別碼。 這通常與**對等 IP**相同。  如果您在內部部署 VPN 閘道上指定了唯一識別碼，則必須指定識別碼。  在建立站對站 VPN 閘道期間，必須要有對等識別碼。   |
| 內部部署網路   | 在區域中需要存取 CloudSimple 網路的內部部署首碼。  包含將存取 CloudSimple 網路之內部部署網路中的所有首碼，包括使用者存取網路的來源用戶端網路。                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>使用點對站 VPN 連線所需的網路位址範圍

點對站 VPN 連線可讓您從用戶端電腦存取 CloudSimple 網路。  [若要設定點對站 VPN](vpn-gateway.md)，您必須指定下列網路位址範圍。

| 位址/位址範圍 | Description                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 用戶端子網         | 當您使用點對站 VPN 進行連接時，用戶端子網會提供 DHCP 位址。 當您要在 CloudSimple 入口網站上建立點對站 VPN 閘道時，需要此子網。  網路分為兩個子網;一個用於 UDP 連接，另一個用於 TCP 連接。 |

## <a name="next-steps"></a>後續步驟

* [用於存取私人雲端的內部部署防火牆設定](on-premises-firewall-configuration.md)
* [快速入門-建立 CloudSimple 服務](quickstart-create-cloudsimple-service.md)
* [快速入門-設定私人雲端](quickstart-create-private-cloud.md)
* 深入瞭解[Azure 網路](cloudsimple-azure-network-connection.md)連線
* 深入瞭解[VPN 閘道](cloudsimple-vpn-gateways.md)
