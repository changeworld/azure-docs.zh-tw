---
title: Azure VMware 解決方案（AVS）-服務
description: 提供 AVS 服務和概念的總覽。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024944"
---
# <a name="avs-service-overview"></a>AVS 服務總覽

AVS 服務可讓您使用 AVS 的 Azure VMware 解決方案。 建立服務可讓您購買節點、保留節點，以及建立 AVS 私人雲端。 您會在可使用 AVS 服務的每個 Azure 區域中建立 AVS 服務。 服務會定義由 AVS 組成的 Azure VMware 解決方案 edge 網路。 Edge 網路支援包含 VPN、ExpressRoute 和網際網路連線到您的 AVS 私人雲端的服務。

## <a name="gateway-subnet"></a>閘道器子網路

每個 AVS 服務都需要閘道子網，而且對其建立所在的區域而言是唯一的。 建立 edge 網路時，會使用閘道子網，而且需要/28 CIDR 區塊。 閘道子網位址空間必須是唯一的。 它不得與任何與 AVS 環境通訊的網路重迭。 與 AVS 通訊的網路包含內部部署網路和 Azure 虛擬網路。 建立閘道子網之後，就無法刪除它。 刪除服務時，會移除閘道子網。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[在 Azure 上建立 AVS 服務](quickstart-create-cloudsimple-service.md)。
