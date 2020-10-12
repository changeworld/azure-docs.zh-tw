---
title: Azure VMware Solution by CloudSimple-服務
description: 瞭解 CloudSimple 服務的總覽。 建立服務可讓您購買節點、保留節點，以及建立私人雲端。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ec77209c0995bf5aec0957c8d4d2269f27d743bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142123"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 服務總覽

CloudSimple 服務可讓您使用 Azure VMware Solution by CloudSimple。  建立服務可讓您購買節點、保留節點，以及建立私人雲端。  您可以在可使用 CloudSimple 服務的每個 Azure 區域中建立 CloudSimple 服務。 服務會定義 Azure VMware Solution by CloudSimple 的邊緣網路。 Edge 網路支援的服務包括 VPN、ExpressRoute 和私人雲端的網際網路連線能力。

## <a name="gateway-subnet"></a>閘道子網路

每個 CloudSimple 服務都需要閘道子網，而且在其建立所在的區域中是唯一的。 閘道子網會在建立 edge 網路時使用，而且需要/28 CIDR 區塊。  閘道子網位址空間必須是唯一的。 它不能與任何與 CloudSimple 環境通訊的網路重迭。 與 CloudSimple 通訊的網路包括內部部署網路和 Azure 虛擬網路。  建立閘道子網後即無法刪除。  刪除服務時，會移除閘道子網。

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [在 Azure 上建立 CloudSimple 服務](quickstart-create-cloudsimple-service.md)。
