---
title: Azure IoT 中樞移轉至診斷設定 | Microsoft Docs
description: 如何將「Azure IoT 中樞」更新成使用 Azure 診斷設定 (取代作業監視) 來即時監視 IoT 中樞上作業的狀態。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 40c90142330b0530f1127beae1624ff27d7eb6ca
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541480"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>將 IoT 中樞從作業監視移轉至診斷設定

客戶如果使用[作業監視](iot-hub-operations-monitoring.md)來追蹤「IoT 中樞」內作業的狀態，可以將該工作流程遷移至 [Azure 診斷設定](../azure-monitor/platform/platform-logs-overview.md)，這是「Azure 監視器」的一項功能。 診斷設定可針對許多 Azure 服務提供資源層級的診斷資訊。

**IoT 中樞的作業監視功能已被取代** ，並已從入口網站中移除。 本文提供將您工作負載從作業監視移至診斷設定的步驟。 如需有關淘汰時間表的詳細資訊，請參閱[使用 Azure 監視器和 Azure 資源健康狀態來監視您的 Azure IoT 解決方案](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)。

## <a name="update-iot-hub"></a>更新 IoT 中樞

若要在 Azure 入口網站中更新您的「IoT 中樞」，請先開啟診斷設定，然後關閉作業監視。  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>關閉作業監視

> [!NOTE]
> 自2019年3月11日起，已從 IoT 中樞的 Azure 入口網站介面移除作業監視功能。 下列步驟不再適用。 若要遷移，請確定已在上述 Azure 監視器診斷設定中開啟正確的分類。

在您的工作流程中測試新的診斷設定之後，您就可以關閉作業監視功能。 

1. 在您的「IoT 中樞」功能表上，選取 [作業監視]  。

2. 在每個監視類別底下，選取 [無]  。

3. 儲存作業監視變更。

## <a name="update-applications-that-use-operations-monitoring"></a>更新使用作業監視的應用程式

作業監視與診斷設定的結構描述有些微差異。 請務必更新目前使用作業監視的應用程式，以對應診斷設定所使用的結構描述。 

此外，診斷設定還提供五個新的類別來進行追蹤。 在您針對現有的結構描述更新應用程式之後，請一併新增新的類別：

* 雲端到裝置對應項作業
* 裝置到雲端對應項作業
* 對應項查詢
* 作業的操作
* 直接方法

如需特定的架構結構，請參閱 [資源記錄](monitor-iot-hub-reference.md#resource-logs)檔。

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>監視低延遲的裝置連線和中斷連接事件

若要在生產環境中監視裝置連線和中斷線上活動，建議您訂閱事件方格上的 [**裝置中斷** 線上活動](iot-hub-event-grid.md#event-types) ，以取得警示並監視裝置線上狀態。 使用此[教學課程](iot-hub-how-to-order-connection-state-events.md)可了解如何將 IoT 中樞的「裝置連線」和「裝置中斷連線」事件整合至 IoT 解決方案中。

## <a name="next-steps"></a>下一步

[監視 IoT 中樞](monitor-iot-hub.md)
