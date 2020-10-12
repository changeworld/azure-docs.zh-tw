---
title: 管理 Azure IoT 中樞的公用網路存取
description: 有關如何停用和啟用 IoT 中樞的公用網路存取的檔
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85937500"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>管理 IoT 中樞的公用網路存取

若要限制只能存取 [您的 VNet 中 IoT 中樞的私人端點](virtual-network-support.md)，請停用公用網路存取。 若要這樣做，請使用 Azure 入口網站或 `publicNetworkAccess` API。 

## <a name="turn-off-public-network-access-using-azure-portal"></a>使用 Azure 入口網站關閉公用網路存取

1. 造訪 [Azure 入口網站](https://portal.azure.com)
2. 瀏覽至您的 IoT 中樞。
3. 從左側功能表中選取 [ **網路** ]。
4. 在 [允許公用網路存取] 下，選取 [**已停用**]
5. 選取 [儲存]****。

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="顯示 Azure 入口網站關閉公用網路存取權的影像" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

若要開啟公用網路存取，請選取 [ **已啟用**]，然後 **儲存**。

## <a name="ip-filter"></a>IP 篩選器 

如果公用網路存取已停用，則會忽略所有 [IP 篩選](iot-hub-ip-filtering.md) 規則。 這是因為公用網際網路的所有 Ip 都會遭到封鎖。 若要使用 IP 篩選器，請使用 [ **選取的 ip 範圍** ] 選項。

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>使用內建事件中樞相容端點修正錯誤

IoT 中樞有一個錯誤，其中 [內建的事件中樞相容端點](iot-hub-devguide-messages-read-builtin.md) 可在對 IoT 中樞的公用網路存取停用時，繼續透過公用網際網路存取。 若要深入瞭解並與我們分享此錯誤的相關資訊，請參閱 [停用 IoT 中樞的公用網路存取會停用內建事件中樞端點的存取權](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)。