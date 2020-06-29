---
title: 包含檔案
description: 包含檔案
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "67174413"
---
## <a name="view-device-telemetry"></a>檢視裝置遙測資料

您可在解決方案的 [裝置總管] 頁面中，檢視從裝置傳送的遙測。

1. 在 [裝置總管] 頁面上選取裝置清單中已佈建的裝置。 面板會顯示您裝置的相關資訊，包括裝置遙測繪圖：

    ![請參閱裝置詳細資料](media/iot-suite-visualize-connecting/devicesdetail.png)

1. 選擇 [壓力] 以變更遙測顯示器：

    ![檢視壓力遙測](media/iot-suite-visualize-connecting/devicespressure.png)

1. 若要檢視有關您裝置的診斷資訊，請向下捲動至 [診斷]：

    ![檢視裝置診斷](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>在裝置上採取行動

若要在裝置上叫用方法，請使用遠端監視解決方案中的 [裝置總管] 頁面。 例如，在遠端監視解決方案中，**Chiller** 裝置會實作 **Reboot** 方法。

1. 選擇 [裝置] 以巡覽至解決方案的 [裝置總管] 頁面。

1. 在 [裝置總管] 頁面中選取裝置清單中已佈建的裝置：

    ![選取您的實體裝置](media/iot-suite-visualize-connecting/devicesselect.png)

1. 若要顯示可在裝置上呼叫的方法清單，請依序選擇 [作業] 和 [方法]。 若要排程可在多個裝置上執行的作業，您可以在清單中選取多個裝置。 [作業] 面板會顯示所有您選取之裝置的通用方法。

1. 選擇 [重新開機]，將作業名稱設定為 **RebootPhysicalChiller**，然後選擇 [套用]：

    ![排程韌體更新](media/iot-suite-visualize-connecting/deviceschedule.png)

1. 當模擬裝置處理該方法時，會在執行裝置程式碼的主控台中顯示一系列訊息。

> [!NOTE]
> 若要追蹤解決方案中的作業狀態，請選擇 [檢視作業狀態]。

## <a name="next-steps"></a>後續步驟

[自訂遠端監視解決方案加速器](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md)一文中，說明了一些自訂解決方案加速器的方法。