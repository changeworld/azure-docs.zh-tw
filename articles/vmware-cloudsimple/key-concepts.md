---
title: 通過雲簡單管理 Azure VMware 解決方案的關鍵概念
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述按雲簡單管理 Azure VMware 解決方案的關鍵概念
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efe6cc132b062e833dc85c13cf2f6c5f6289484c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564614"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>通過雲簡單管理 Azure VMware 解決方案的關鍵概念

通過雲簡單管理 Azure VMware 解決方案需要瞭解以下概念：

* 雲簡單服務，按雲簡單顯示為 Azure VMware 解決方案 - 服務
* 雲簡單節點，按雲簡單顯示為 Azure VMware 解決方案 - 節點
* 雲簡單私有雲
* 服務網路
* 雲簡單虛擬機器，它顯示為 Azure VMware 解決方案（由雲簡單 - 虛擬機器）

## <a name="cloudsimple-service"></a>雲簡單服務

借助 CloudSimple 服務，您可以通過雲簡單創建和管理與 VMware 解決方案關聯的所有資源。這些資源來自 Azure 門戶。 在要使用該服務的每個區域創建服務資源。

瞭解有關[雲簡單服務的更多詳細資訊](cloudsimple-service.md)。

## <a name="cloudsimple-node"></a>雲簡單節點

CloudSimple 節點是一個專用的裸機超融合計算和存儲主機，其中部署了 VMware ESXi 虛擬機器管理程式。 然後，此節點將合併到 VMware vSphere、vCenter、vSAN 和 NSX 平臺中。 雲簡單網路服務和邊緣網路服務也已啟用。 每個節點都用作計算和存儲容量的單元，您可以預配這些單元來創建[雲簡單私有雲](cloudsimple-private-cloud.md)。 在雲簡單服務可用的區域中預配或保留節點。

瞭解有關[雲簡單節點](cloudsimple-node.md)的更多。

## <a name="cloudsimple-private-cloud"></a>雲簡單私有雲

CloudSimple 私有雲是由 vCenter 伺服器在其自己的管理域中管理的隔離 VMware 堆疊環境。 VMware 堆疊包括 ESXi 主機、vSphere、vCenter、vSAN 和 NSX。 堆疊在專用節點（專用和隔離裸機硬體）上運行，使用者通過包括 vCenter 和 NSX 管理器的本機 VMware 工具使用。 專用節點部署在 Azure 位置，並由 Azure 管理。 通過使用網路服務（如 VLAN、子網和防火牆表）可以對每個私有雲進行分段和保護。 使用安全專用 VPN 和 Azure 快速路由連接創建到本地環境和 Azure 網路的連接。

瞭解有關[雲簡單私有雲的更多。](cloudsimple-private-cloud.md)

## <a name="service-networking"></a>服務網路

CloudSimple 服務提供部署雲簡單服務的每個區域的網路。 網路是單個 TCP 第 3 層位址空間，預設情況下啟用路由。 在此區域中創建的所有私有雲和子網相互通信，無需任何其他配置。 通過使用 VLAN 在 vCenter 上創建分散式埠組。 您可以使用以下網路功能在私有雲中配置和保護工作負載資源：

* [VLAN 和子網路](cloudsimple-vlans-subnets.md)
* [防火牆資料表](cloudsimple-firewall-tables.md)
* [VPN 閘道](cloudsimple-vpn-gateways.md)
* [公共智慧財產權](cloudsimple-public-ip-address.md)
* [Azure 網路連線](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>雲簡單虛擬機器

使用 CloudSimple 服務，可以從 Azure 門戶管理 VMware 虛擬機器。 可以從 vSphere 環境中的一個或多個群集或資源池映射到創建服務的訂閱。

深入了解：

* [雲簡單虛擬機器](cloudsimple-virtual-machines.md)
* [Azure 訂閱映射](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
