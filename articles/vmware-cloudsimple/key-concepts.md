---
title: 管理 Azure VMware Solution by CloudSimple 的重要概念
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述透過 CloudSimple 管理 Azure VMware 解決方案的重要概念
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8677bc7e4b9f4c2b24b5439aeefb307b9c544ed3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86525019"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>依 CloudSimple 管理 Azure VMware 解決方案的重要概念

依 CloudSimple 管理 Azure VMware 解決方案需要瞭解下列概念：

* CloudSimple 服務，以 CloudSimple 服務的形式顯示為 Azure VMware 解決方案
* CloudSimple 節點，此節點會顯示為 Azure VMware 解決方案 by CloudSimple-Node
* CloudSimple 私人雲端
* 服務網路
* CloudSimple 虛擬機器，其會顯示為 Azure VMware 解決方案 by CloudSimple-虛擬機器

## <a name="cloudsimple-service"></a>CloudSimple 服務

使用 CloudSimple 服務，您可以從 Azure 入口網站 CloudSimple，以建立和管理與 VMware 解決方案相關聯的所有資源。 在您想要使用服務的每個區域中建立服務資源。

深入瞭解 [CloudSimple 服務](cloudsimple-service.md)。

## <a name="cloudsimple-node"></a>CloudSimple 節點

CloudSimple 節點是一種專用的裸機超融合式計算和儲存體主機，其中部署了 VMware ESXi 的虛擬程式。 此節點接著會併入 VMware vSphere、vCenter、vSAN 和 NSX 平臺中。 此外，也會啟用 CloudSimple 網路服務和邊緣網路服務。 每個節點都可作為計算和儲存體容量的單位，您可以布建這些資源以建立 [CloudSimple 私人](cloudsimple-private-cloud.md)雲端。 您可以在 CloudSimple 服務可用的區域中布建或保留節點。

深入瞭解 [CloudSimple 節點](cloudsimple-node.md)。

## <a name="cloudsimple-private-cloud"></a>CloudSimple 私人雲端

CloudSimple 私人雲端是一種獨立的 VMware 堆疊環境，由它自己的管理網域中的 vCenter server 所管理。 VMware 堆疊包含 ESXi 主機、vSphere、vCenter、vSAN 和 NSX。 堆疊會在專用節點上執行， (專用且獨立的裸機硬體) ，而且使用者會透過包含 vCenter 和 NSX Manager 的原生 VMware 工具來取用。 專用節點會部署在 Azure 位置中，並由 Azure 管理。 您可以使用網路服務（例如 Vlan 和子網和防火牆資料表）來分割和保護每個私人雲端。 您可以使用安全、私人 VPN 和 Azure ExpressRoute 連線來建立內部部署環境和 Azure 網路的連線。

深入瞭解 [CloudSimple 私用雲端](cloudsimple-private-cloud.md)。

## <a name="service-networking"></a>服務網路

CloudSimple 服務會為您的 CloudSimple 服務部署所在的每個區域提供一個網路。 網路是單一 TCP 第3層位址空間，預設會啟用路由。 在此區域中建立的所有私人雲端和子網都會彼此通訊，而不需要任何額外的設定。 您可以使用 Vlan 在 vCenter 上建立分散式通訊埠群組。 您可以使用下列網路功能來設定和保護私用雲端中的工作負載資源：

* [VLAN 和子網路](cloudsimple-vlans-subnets.md)
* [防火牆資料表](cloudsimple-firewall-tables.md)
* [VPN 閘道](cloudsimple-vpn-gateways.md)
* [公用 IP](cloudsimple-public-ip-address.md)
* [Azure 網路連線](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple 虛擬機器

使用 CloudSimple 服務，您可以從 Azure 入口網站管理 VMware 虛擬機器。 您 vSphere 環境中的一或多個叢集或資源集區可對應至建立服務的訂用帳戶。

深入了解：

* [CloudSimple 虛擬機器](cloudsimple-virtual-machines.md)
* [Azure 訂用帳戶對應](./azure-subscription-mapping.md)
