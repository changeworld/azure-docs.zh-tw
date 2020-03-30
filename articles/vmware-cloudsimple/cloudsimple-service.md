---
title: Azure VMware 解決方案（按雲簡單 ） 服務
description: 提供雲簡單服務和概念的概述。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024944"
---
# <a name="cloudsimple-service-overview"></a>雲簡單服務概述

雲簡單服務允許您使用 Azure VMware 解決方案（按雲簡單。  通過創建服務，您可以購買節點、保留節點和創建私有雲。  您可以在雲簡單服務可用的每個 Azure 區域創建雲簡單服務。 該服務通過雲簡單定義 Azure VMware 解決方案的邊緣網路。 邊緣網路支援包括 VPN、ExpressRoute 和 Internet 連接到私有雲的服務。

## <a name="gateway-subnet"></a>閘道器子網路

每個 CloudSimple 服務都需要閘道子網，並且對於創建閘道子網的區域是唯一的。 創建邊緣網路時使用閘道子網，並且需要 /28 CIDR 塊。  閘道子網位址空間必須是唯一的。 它不得與任何與 CloudSimple 環境通信的網路重疊。 與 CloudSimple 通信的網路包括本地網路和 Azure 虛擬網路。  創建閘道子網後無法刪除該子網。  刪除服務時，將刪除閘道子網。

## <a name="next-steps"></a>後續步驟

* 瞭解如何在[Azure 上創建雲簡單服務](quickstart-create-cloudsimple-service.md)。
