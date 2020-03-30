---
title: 包含檔案
description: 包含檔案
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2514325c93f0f72aa979f871e86e465093479402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "69558738"
---
## <a name="customize-and-extend-the-device-management-actions"></a>自訂及延伸裝置管理動作

您的 IoT 解決方案可以透過使用裝置對應項和雲端到裝置方法基元，擴充一組已定義的裝置管理模式或啟用自訂模式。 其他裝置管理動作範例還包括恢復出廠預設值、韌體更新、軟體更新、電源管理、網路和連線管理，以及資料加密。

## <a name="device-maintenance-windows"></a>裝置維護期間

一般而言，您會設定讓裝置在產生最短中斷和停機時間的時機執行動作。 裝置維護期間是用來定義裝置組態更新時機的常用模式。 您的後端解決方案可以使用所需的裝置對應項 (twin) 屬性，在您的裝置上定義可啟用維護期間的原則並啟用該原則。 當裝置收到維護期間原則時，它可以使用回報的裝置對應項 (twin) 屬性來回報原則的狀態。 接著，後端 App 便可使用裝置對應項 (twin) 查詢來證明是否符合裝置及每個原則的規定。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用直接方法在裝置上觸發遠端重新開機。 您已使用報告屬性來從裝置回報上次重新開機時間，以及查詢裝置對應項來從雲端探索裝置的上次重新開機時間。

要繼續使用 IoT 中心和裝置管理模式（如遠端通過空氣固件更新）入門，請參閱[如何執行固件更新](../articles/iot-hub/tutorial-firmware-update.md)。

要瞭解如何在多台設備上擴展 IoT 解決方案和計畫方法調用，請參閱[計畫和廣播作業](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md)。