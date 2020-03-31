---
title: Azure VMware 解決方案（按雲簡單 - 網路檢查表）
description: 用於在 Azure VMware 解決方案上按雲簡單分配網路 CIDR 的檢查表
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025005"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>按雲簡單實現 Azure VMware 解決方案的網路先決條件

雲簡單版 Azure VMware 解決方案提供了 VMware 私有雲環境，可供本地環境、企業託管設備和 Azure 資源的使用者和應用程式訪問。 連接通過 VPN 和 Azure 快速路由連接等網路服務進行。 其中一些網路服務要求您指定網路位址範圍以啟用服務。 

本文中的表描述了使用指定位址的位址範圍和相應服務集。 某些位址是強制性的，有些位址取決於要部署的服務。 這些位址空間不應與任何本地子網、Azure 虛擬網路子網或計畫的 CloudSimple 工作負荷子網重疊。

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>創建私有雲所需的網路位址範圍

在創建 CloudSimple 服務和私有雲期間，必須遵守指定的無類域間路由 （CIDR） 範圍，如下所示。

| 名稱/用於     | 描述                                                                                                                            | 位址範圍            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| 閘道 CIDR      | 邊緣服務（VPN 閘道）需要。  此 CIDR 在雲簡單服務創建期間是必需的，並且必須來自 RFC 1918 空間。 | /28                      |
| vSphere/vSAN CIDR | VMware 管理網路需要。 必須在私有雲創建期間指定此 CIDR。                                    | /24 或 /23 或 /22 或 /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Azure 連接到本地網路所需的網路位址範圍

[通過 ExpressRoute 從本地網路連接到專用雲網路](on-premises-connection.md)可建立全球覆蓋連接。  該連接使用邊界閘道協定 （BGP） 在本地網路、私有雲網路和 Azure 網路之間交換路由。

| 名稱/用於             | 描述                                                                                                                                                                             | 位址範圍 |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| 快速路由對等 CIDR | 使用 ExpressRoute 全球覆蓋實現本地連接時是必需的。 當通過支援票證發出全域覆蓋連接請求時，必須提供此 CIDR。 | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>使用網站到網站 VPN 連接到本地網路所需的網路位址範圍

[使用網站到本地 VPN 從本地網路連接到私有雲網路](vpn-gateway.md)需要以下 IP 位址、本地網路和識別碼。 

| 位址/位址範圍 | 描述                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 對等 IP               | 本地 VPN 閘道公共 IP 位址。 需要在本地資料中心和雲簡單服務區域之間建立網站到網站 VPN 連接。 在網站到網站 VPN 閘道創建期間，需要此 IP 位址。                                         |
| 對等識別碼       | 本地 VPN 閘道的對等識別碼。 這通常與**對等 IP**相同。  如果在本地 VPN 閘道上指定了唯一識別碼，則必須指定該識別碼。  網站到網站 VPN 閘道創建期間需要對等 ID。   |
| 本地網路   | 需要訪問區域中的 CloudSimple 網路的本地首碼。  包括將訪問 CloudSimple 網路的本地網路的所有首碼，包括使用者從其中訪問網路的用戶端網路。                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>使用點對點 VPN 連接所需的網路位址範圍

點對點 VPN 連接允許從用戶端電腦訪問 CloudSimple 網路。  [要設置點對點 VPN，](vpn-gateway.md)必須指定以下網路位址範圍。

| 位址/位址範圍 | 描述                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 用戶端子網         | 使用點對點 VPN 進行連接時，用戶端子網提供 DHCP 位址。 當您在 CloudSimple 門戶上創建點對點 VPN 閘道時，需要此子網。  網路分為兩個子網;一個用於 UDP 連接，另一個用於 TCP 連接。 |

## <a name="next-steps"></a>後續步驟

* [用於訪問私有雲的本地防火牆設置](on-premises-firewall-configuration.md)
* [快速入門 - 創建雲簡單服務](quickstart-create-cloudsimple-service.md)
* [快速入門 - 配置私有雲](quickstart-create-private-cloud.md)
* 瞭解有關[Azure 網路連接](cloudsimple-azure-network-connection.md)的更多資訊
* 瞭解有關[VPN 閘道](cloudsimple-vpn-gateways.md)的更多
