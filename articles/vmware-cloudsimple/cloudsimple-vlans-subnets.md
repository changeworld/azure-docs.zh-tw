---
title: Azure VMware Solution by CloudSimple 中的 Vlan 和子網
description: 瞭解 CloudSimple 私人雲端中的 Vlan 和子網，以及 CloudSimple 提供 CloudSimple 服務部署位置的網路。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e3235b394f739e8af846f30dfe51705d0926c843
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140661"
---
# <a name="vlans-and-subnets-overview"></a>Vlan 和子網總覽

CloudSimple 會為您的 CloudSimple 服務部署所在的每個區域提供一個網路。  網路是單一 TCP 第3層位址空間，預設會啟用路由。  在此區域中建立的所有私人雲端和子網都可以彼此通訊，而不需要任何額外的設定。  您可以使用 Vlan 在 vCenter 上建立分散式通訊埠群組。

![CloudSimple 網路拓撲](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

針對每個私人雲端建立 VLAN (第2層網路) 。  第2層流量會維持在私用雲端的界限內，讓您能夠隔離私人雲端內的本機流量。  私人雲端上建立的 VLAN 只能用來在該私用雲端中建立分散式埠群組。  在私人雲端上建立的 VLAN 會自動在所有連線到私人雲端主機的交換器上進行設定。

## <a name="subnets"></a>子網路

您可以藉由定義子網的位址空間，在建立 VLAN 時建立子網。 位址空間中的 IP 位址會指派為子網閘道。 針對每個客戶和區域指派單一私人第3層位址空間。 您可以在網路區域中，使用內部部署網路或 Azure 虛擬網路來設定任何 RFC 1918 非重迭的位址空間。

根據預設，所有子網都可以彼此通訊，以降低私人雲端之間路由的設定額外負荷。 在相同區域中，跨電腦的東亞資料會維持在相同的第3層網路，並透過區域內的區域網路基礎結構進行傳輸。 區域中的私人雲端之間的通訊不需要輸出。 這種方法可在不同的私人雲端中部署不同的工作負載時，消除任何 WAN/輸出效能的影響。

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN 子網 CIDR 範圍

私人雲端會建立為隔離的 VMware 堆疊， (ESXi 主機、vCenter、vSAN 以及由 vCenter 伺服器管理的 NSX) 環境。  系統會將管理元件部署在針對 vSphere/vSAN 子網 CIDR 選取的網路中。  網路 CIDR 範圍會在部署期間分成不同的子網。

* 最小 vSphere/vSAN 子網 CIDR 範圍前置詞： **/24**
* 最大 vSphere/vSAN 子網 CIDR 範圍前置詞： **/21**

> [!CAUTION]
> VSphere/vSAN CIDR 範圍中的 IP 位址會保留供私用雲端基礎結構使用。  請勿在任何虛擬機器上使用此範圍內的 IP 位址。

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN 子網 CIDR 範圍限制

選取 vSphere/vSAN 子網 CIDR 範圍大小會影響您私人雲端的大小。  下表根據 vSphere/vSAN 子網 CIDR 的大小，顯示您可以擁有的節點數目上限。

| 指定的 vSphere/vSAN 子網 CIDR 前置長度 | 節點數目上限 |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>在私人雲端上建立的管理子網

當您建立私人雲端時，會建立下列管理子網。

* **系統管理**。 ESXi 主機管理網路、DNS 伺服器、vCenter server 的 VLAN 和子網。
* **VMotion**。 ESXi 主機 vMotion 網路的 VLAN 和子網。
* **VSAN**。 適用于 ESXi 主機 vSAN 網路的 VLAN 和子網。
* **NsxtEdgeUplink1**。 Vlan 和子網，適用于 VLAN 上行連結至外部網路。
* **NsxtEdgeUplink2**。 Vlan 和子網，適用于 VLAN 上行連結至外部網路。
* **NsxtEdgeTransport**。 傳輸區域的 VLAN 和子網可控制 NSX-T 中第2層網路的範圍。
* **NsxtHostTransport**。 主機傳輸區域的 VLAN 和子網。

### <a name="management-network-cidr-range-breakdown"></a>管理網路 CIDR 範圍細目

指定的 vSphere/vSAN 子網 CIDR 範圍會分割成多個子網。  下表顯示允許的首碼的明細範例。  此範例使用192.168.0.0 作為 CIDR 範圍。

範例：

| 指定的 vSphere/vSAN 子網 CIDR/首碼 | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| 系統管理 | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T 主機傳輸 | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T Edge Transport | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>後續步驟

* [建立及管理 VLAN 和子網路](create-vlan-subnet.md)
