---
title: 管理 Azure VMware 解決方案（AVS）的重要概念
description: 說明管理 Azure VMware 解決方案（AVS）的重要概念
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025226"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>管理 Azure VMware 解決方案（AVS）的重要概念

管理 Azure VMware 解決方案（AVS）需要瞭解下列概念：

* 顯示為 Azure VMware 解決方案（AVS）服務的 AVS 服務
* 顯示為 Azure VMware 解決方案（AVS）節點的 AVS 節點
* AVS 私用雲端
* 服務網路
* 顯示為 Azure VMware 解決方案（AVS）-虛擬機器的 AVS 虛擬機器

## <a name="avs-service"></a>AVS 服務

使用 AVS 服務，您可以從 Azure 入口網站建立及管理與 VMware 解決方案（AVS）相關聯的所有資源。 在您想要使用服務的每個區域中建立服務資源。

深入瞭解[AVS 服務](cloudsimple-service.md)。

## <a name="avs-node"></a>AVS 節點

AVS 節點是在其中部署 VMware ESXi 程式管理元件的專用、裸機、超大範圍計算和儲存體主機。 此節點接著會併入 VMware vSphere、vCenter、vSAN 和 NSX 平臺中。 也會啟用 [AVS 網路服務] 和 [edge 網路服務]。 每個節點都可做為計算和儲存體容量的單位，以供您布建以建立[AVS 私人](cloudsimple-private-cloud.md)雲端。 您會在可使用 AVS 服務的區域中布建或保留節點。

深入瞭解[AVS 節點](cloudsimple-node.md)。

## <a name="avs-private-cloud"></a>AVS 私用雲端

AVS 私人雲端是一種隔離的 VMware 堆疊環境，受其本身管理網域中的 vCenter server 所管理。 VMware 堆疊包含 ESXi 主機、vSphere、vCenter、vSAN 和 NSX。 堆疊會在專用節點（專用和隔離的裸機硬體）上執行，並由使用者透過包含 vCenter 和 NSX Manager 的原生 VMware 工具取用。 專用節點會部署在 Azure 位置，並由 Azure 管理。 每個 AVS 私人雲端都可以使用網路服務（例如 Vlan 和子網和防火牆表格）進行分割和保護。 您的內部部署環境與 Azure 網路的連線是使用安全、私人 VPN 和 Azure ExpressRoute 連線所建立。

深入瞭解[AVS 私用雲端](cloudsimple-private-cloud.md)。

## <a name="service-networking"></a>服務網路

AVS 服務會為您部署您的 AVS 服務的每個區域提供一個網路。 網路是單一的 TCP 第3層位址空間，預設會啟用路由。 在此區域中建立的所有 AVS 私人雲端和子網，不需要進行任何額外設定即可彼此通訊。 您可以使用 Vlan 在 vCenter 上建立分散式通訊埠群組。 您可以使用下列網路功能來設定及保護您的 AVS 私人雲端中的工作負載資源：

* [Vlan 和子網](cloudsimple-vlans-subnets.md)
* [防火牆資料表](cloudsimple-firewall-tables.md)
* [VPN 閘道](cloudsimple-vpn-gateways.md)
* [公用 IP](cloudsimple-public-ip-address.md)
* [Azure 網路連線](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>AVS 虛擬機器

使用 AVS 服務，您可以從 Azure 入口網站管理 VMware 虛擬機器。 您 vSphere 環境中的一或多個叢集或資源集區，可以對應到建立服務所在的訂用帳戶。

深入了解：

* [AVS 虛擬機器](cloudsimple-virtual-machines.md)
* [Azure 訂用帳戶對應](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
