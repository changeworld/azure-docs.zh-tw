---
title: 建立環境 - Azure 時間序列深入解析 | Microsoft Docs
description: 瞭解如何使用 Azure 入口網站建立新的 Azure 時間序列深入解析環境。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 401d22ee68ba0652eb684b03b06fddf0b0fe3d72
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569454"
---
# <a name="create-a-new-azure-time-series-insights-gen1-environment-in-the-azure-portal"></a>在 Azure 入口網站中建立新的 Azure 時間序列深入解析 Gen1 環境

> [!CAUTION]
> 這是 Gen1 文章。

本文說明如何使用 Azure 入口網站建立新的 Azure 時間序列深入解析環境。

Azure 時間序列深入解析可讓您在幾分鐘內開始視覺化和查詢流入 Azure IoT 中樞和事件中樞的資料，讓您可以在數秒內查詢大量的時間序列資料。  它是針對物聯網 (IoT) 規模所設計的，而且可以處理數 TB 的資料。

## <a name="steps-to-create-the-environment"></a>用以建立環境的步驟

請遵循下列步驟來建立環境：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [ **+ 建立資源** ] 按鈕。

1. 選取 [物聯網]**** 類別，然後選取 [時間序列深入解析]****。

   [![建立 Azure 時間序列深入解析環境](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. 在 [時間序列深入解析]**** 分頁上，選取 [建立]****。

1. 填入必要參數。 下表說明每個參數：

   [![建立 Azure 時間序列深入解析資源群組](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   設定|建議的值|描述
   ---|---|---
   環境名稱 | 唯一的名稱 | 這個名稱代表[時間序列 Explorer](https://insights.timeseries.azure.com)中的環境
   訂用帳戶 | 您的訂用帳戶 | 如果您有多個訂用帳戶，最好是選擇包含事件來源的訂用帳戶。 Azure 時間序列深入解析可以自動偵測相同訂用帳戶中現有的 Azure IoT 中樞和事件中樞資源。
   資源群組 | 建立新的或使用現有的 | 資源群組是一起使用之 Azure 資源的集合。 您可以選擇現有的資源群組，例如包含事件中樞或 IoT 中樞的資源群組。 或者，如果此資源與其他資源不相關，您可以製作新的群組。
   Location | 最接近您的事件來源 | 最好是選擇包含事件來源資料的相同資料中心位置，以避免跨區域和跨地區頻寬成本，以及在將資料移出區域時增加延遲。
   定價層 | S1 | 選擇所需的輸送量。 如需最低成本和入門容量，選取 S1。
   Capacity | 1 | 容量是套用至輸入速率、儲存體容量以及與選取之 SKU 相關聯成本的乘數。  您可以在環境建立後變更其容量。 如需最低成本，選取 1 作為容量。
  
1. 選取 [建立]**** 以開始佈建程序。 這可能需要幾分鐘的時間。

1. 若要監視部署程序，選取 [通知]**** 符號 (鈴鐺圖示)。

   [![觀看通知](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. 在資源 **總覽**中，確認您的部署設定。

   [![建立 Azure 時間序列深入解析釘選到儀表板](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. ** (選擇性) ** 選取右上角的 **釘選圖示** ，以在未來輕鬆存取您的 Azure 時間序列深入解析環境。

## <a name="next-steps"></a>後續步驟

* [定義資料存取原則](time-series-insights-data-access.md)來保護環境。

* [新增事件中樞事件來源](time-series-insights-how-to-add-an-event-source-eventhub.md)到您的 Azure 時間序列深入解析環境。

* [將事件傳送](time-series-insights-send-events.md) 至事件來源。

* 在 [Azure 時間序列深入解析 Explorer](https://insights.timeseries.azure.com)中查看您的環境。
