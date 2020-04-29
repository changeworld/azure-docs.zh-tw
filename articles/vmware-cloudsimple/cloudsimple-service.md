---
title: Azure VMware Solution by CloudSimple-服務
description: 提供 CloudSimple 服務和概念的總覽。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024944"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 服務總覽

CloudSimple 服務可讓您使用 CloudSimple 的 Azure VMware 解決方案。  建立服務可讓您購買節點、保留節點，以及建立私人雲端。  您會在可使用 CloudSimple 服務的每個 Azure 區域中建立 CloudSimple 服務。 服務會依 CloudSimple 定義 Azure VMware 解決方案的邊緣網路。 Edge 網路支援包含 VPN、ExpressRoute 和網際網路連線的服務，可連接到您的私人雲端。

## <a name="gateway-subnet"></a>閘道器子網路

每個 CloudSimple 服務都需要閘道子網，而且對其建立所在的區域而言是唯一的。 建立 edge 網路時，會使用閘道子網，而且需要/28 CIDR 區塊。  閘道子網位址空間必須是唯一的。 它不得與任何與 CloudSimple 環境通訊的網路重迭。 與 CloudSimple 通訊的網路包括內部部署網路和 Azure 虛擬網路。  建立閘道子網之後，就無法刪除它。  刪除服務時，會移除閘道子網。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[在 Azure 上建立 CloudSimple 服務](quickstart-create-cloudsimple-service.md)。
