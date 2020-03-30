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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174413"
---
## <a name="view-device-telemetry"></a>檢視裝置遙測資料

您可以在解決方案中的 **"設備資源管理器"** 頁上查看從設備發送的遙測資料。

1. 在 **"設備資源管理器"** 頁上的設備清單中選擇預配的設備。 面板會顯示您裝置的相關資訊，包括裝置遙測繪圖：

    ![請參閱裝置詳細資料](media/iot-suite-visualize-connecting/devicesdetail.png)

1. 選擇 [壓力]**** 以變更遙測顯示器：

    ![檢視壓力遙測](media/iot-suite-visualize-connecting/devicespressure.png)

1. 若要檢視有關您裝置的診斷資訊，請向下捲動至 [診斷]****：

    ![檢視裝置診斷](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>在裝置上採取行動

要在設備上調用方法，請使用遠端監視解決方案中的 **"設備資源管理器**"頁。 例如，在遠端監視解決方案**中，冷水機組**設備實現**重新開機**方法。

1. 選擇 **"設備"** 以導航到解決方案中的 **"設備資源管理器"** 頁。

1. 在 **"設備資源管理器"** 頁上的設備清單中選擇預配的設備：

    ![選取您的實體裝置](media/iot-suite-visualize-connecting/devicesselect.png)

1. 要顯示可以在設備上調用的方法的清單，請選擇 **"作業**"，然後選擇 **"方法**"。 若要排程可在多個裝置上執行的作業，您可以在清單中選取多個裝置。 [作業]**** 面板會顯示所有您選取之裝置的通用方法。

1. 選擇**重新開機**，將作業名稱設置為**重新開機物理冷位，** 然後選擇 **"應用**：

    ![排程韌體更新](media/iot-suite-visualize-connecting/deviceschedule.png)

1. 當模擬裝置處理該方法時，會在執行裝置程式碼的主控台中顯示一系列訊息。

> [!NOTE]
> 要跟蹤解決方案中的作業狀態，請選擇"**查看作業狀態**"。

## <a name="next-steps"></a>後續步驟

"[自訂遠端監視"解決方案加速器](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md)介紹了自訂解決方案加速器的一些方法。