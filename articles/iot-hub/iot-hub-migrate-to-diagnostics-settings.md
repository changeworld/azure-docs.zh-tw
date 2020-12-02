---
title: 將 Azure IoT 中樞作業監視遷移至 Azure 監視器中的 IoT 中樞資源記錄 |Microsoft Docs
description: 如何更新 Azure IoT 中樞以使用 Azure 監視器而不是作業監視來即時監視 IoT 中樞上的作業狀態。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: dfd819d82f5e35183802e33e5d423cad4de36c38
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461587"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>將 IoT 中樞從作業監視遷移至 Azure 監視器資源記錄

使用 [作業監視](iot-hub-operations-monitoring.md) 來追蹤 IoT 中樞作業狀態的客戶可以將該工作流程遷移至 [Azure 監視器資源記錄](../azure-monitor/platform/platform-logs-overview.md)，這是 Azure 監視器的功能。 資源記錄提供許多 Azure 服務的資源層級診斷資訊。

**IoT 中樞的作業監視功能已被取代**，並已從入口網站中移除。 本文提供將工作負載從作業監視移至 Azure 監視器資源記錄的步驟。 如需有關淘汰時間表的詳細資訊，請參閱[使用 Azure 監視器和 Azure 資源健康狀態來監視您的 Azure IoT 解決方案](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)。

## <a name="update-iot-hub"></a>更新 IoT 中樞

若要更新 Azure 入口網站中的 IoT 中樞，請先建立診斷設定，然後關閉作業監視。  

### <a name="create-a--diagnostic-setting"></a>建立診斷設定

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

1. 在左窗格的 [ **監視**] 底下，選取 [ **診斷設定**]。 然後選取 [新增診斷設定]。

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="螢幕擷取畫面：醒目提示 [監視] 區段中的 [診斷設定]。":::

1. 在 [ **診斷設定** ] 窗格中，提供診斷設定的名稱。

1. 在 [ **類別目錄詳細資料**] 底下，選取您要監視之作業的類別。 如需 IoT 中樞可用作業類別的詳細資訊，請參閱 [資源記錄](monitor-iot-hub-reference.md#resource-logs)檔。

1. 在 [ **目的地詳細資料**] 底下，選擇您要傳送記錄的位置。 您可以選取這些目的地的任意組合：

   * 封存至儲存體帳戶
   * 串流至事件中樞
   * 透過 Log Analytics 工作區傳送至 Azure 監視器記錄

   下列螢幕擷取畫面顯示診斷設定，可將連線和裝置遙測類別中的作業路由傳送至 Log Analytics 工作區：

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="顯示已完成之診斷設定的螢幕擷取畫面。":::

1. 選取 [儲存]  以儲存設定。

新的設定大約會在 10 分鐘內生效。 之後，記錄會出現在設定的目的地中。 如需有關設定診斷的詳細資訊，請參閱 [收集和取用來自 Azure 資源的記錄資料](../azure-monitor/platform/platform-logs-overview.md)。

如需有關如何建立診斷設定的詳細資訊，包括 PowerShell 和 Azure CLI，請參閱 Azure 監視器檔中的 [診斷設定](../azure-monitor/platform/diagnostic-settings.md) 。

### <a name="turn-off-operations-monitoring"></a>關閉作業監視

> [!NOTE]
> 自2019年3月11日起，已從 IoT 中樞的 Azure 入口網站介面移除作業監視功能。 下列步驟不再適用。 若要遷移，請確定已將正確的類別路由傳送至具有上述 Azure 監視器診斷設定的目的地。

在您的工作流程中測試新的診斷設定之後，您就可以關閉作業監視功能。 

1. 在您的「IoT 中樞」功能表上，選取 [作業監視]。

2. 在每個監視類別底下，選取 [無]。

3. 儲存作業監視變更。

## <a name="update-applications-that-use-operations-monitoring"></a>更新使用作業監視的應用程式

作業監視和資源記錄的架構會稍有不同。 請務必更新使用作業監視的應用程式，以對應至資源記錄所使用的架構。

此外，IoT 中樞資源記錄會提供五個新的類別來進行追蹤。 在您針對現有的結構描述更新應用程式之後，請一併新增新的類別：

* 雲端到裝置對應項作業
* 裝置到雲端對應項作業
* 對應項查詢
* 作業的操作
* 直接方法

如需特定的架構結構，請參閱 [資源記錄](monitor-iot-hub-reference.md#resource-logs)檔。

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>監視低延遲的裝置連線和中斷連接事件

若要在生產環境中監視裝置連線和中斷線上活動，建議您訂閱事件方格上的 [**裝置中斷** 線上活動](iot-hub-event-grid.md#event-types) ，以取得警示並監視裝置線上狀態。 使用此[教學課程](iot-hub-how-to-order-connection-state-events.md)可了解如何將 IoT 中樞的「裝置連線」和「裝置中斷連線」事件整合至 IoT 解決方案中。

## <a name="next-steps"></a>後續步驟

[監視 IoT 中樞](monitor-iot-hub.md)