---
title: 在 Azure IoT 集中應用程式中使用設備組 |微軟文檔
description: 作為操作員，瞭解如何使用設備組分析 Azure IoT 集中應用程式中設備的遙測資料。
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 758ac037fcd224d02f62239b3408b41b50390147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167253"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>教程：使用設備組分析設備遙測

本文介紹作為操作員如何使用設備組分析 Azure IoT 集中應用程式中的設備遙測。

設備組是分組在一起的設備的清單，因為它們符合某些指定的條件。 設備組通過將設備分組到較小的邏輯組，説明您大規模管理、視覺化和分析設備。 例如，您可以創建一個設備組來列出西雅圖的所有空調設備，使技術人員能夠找到他們負責的設備。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立裝置群組
> * 使用設備組分析設備遙測

## <a name="prerequisites"></a>Prerequisites

開始之前，請先完成[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md) (英文) 和[將模擬裝置新增至 IoT Central 應用程式](./quick-create-pnp-device.md) (英文) 的快速入門，以便建立要使用的 **MXChip IoT DevKit** 裝置範本。

## <a name="create-simulated-devices"></a>創建類比設備

在創建設備組之前，請從**MXChip IoT DevKit**設備範本中添加至少五個類比設備，以在本教程中使用：

![五個類比感應器設備](./media/tutorial-use-device-groups/simulated-devices.png)

對於四個類比感應器設備，請使用 **"管理設備"** 視圖將客戶名稱設置為*Contoso：*

![將客戶名稱設置為 Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>建立裝置群組

要創建設備組，請：

1. 選擇左側窗格上的**設備組**。

1. 選擇**+**：

    ![新設備組](media/tutorial-use-device-groups/image1.png)

1. 為設備組指定*Contoso 設備*的名稱。 您也可以新增描述。 設備組只能包含單個設備範本中的設備。 選擇要用於此組的**MXChip IoT DevKit**設備範本。

1. 要自訂設備組以僅包括屬於**Contoso**的設備，請選擇 **"篩選器**"。 選擇 **"客戶名稱**"屬性 **、"等於"** 比較運算子和**Contoso**作為值。 您可以添加多個滿足**所有**篩選器條件的篩選器和設備，這些篩選器和設備將放置在設備組中。 有權訪問應用程式的任何人都可以訪問您創建的設備組，因此任何人都可以查看、修改或刪除設備組：

    ![設備組查詢](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > 設備組是動態查詢。 您每次檢視裝置清單時所看到的裝置可能會不同。 清單內容取決於哪些裝置目前符合查詢準則。

1. 選擇 [儲存]****。

> [!NOTE]
> 對於 Azure IoT 邊緣設備，請選擇 Azure IoT 邊緣範本以創建設備組。

## <a name="analytics"></a>分析

您可以將**Analytics"分析"** 與設備組一起分析組中設備的遙測資料。 例如，您可以繪製所有 Contoso 環境感應器報告的平均溫度。

要分析設備組的遙測資料，請處理：

1. 在左側窗格中選擇 **"分析**"。

1. 選擇您創建的**Contoso 設備**設備組。 然後添加**溫度**和**濕度**遙測類型：

    ![創建分析](./media/tutorial-use-device-groups/create-analysis.png)

    使用遙測類型旁邊的齒輪圖示選擇聚合類型。 預設值為 **"平均值**"。 使用 **"拆分"** 可更改聚合資料的顯示方式。 例如，如果按裝置識別碼 拆分，則當您選擇 **"分析**"時，會看到每個設備的繪圖。

1. 選擇 **"分析"** 以查看平均遙測值：

    ![查看分析](./media/tutorial-use-device-groups/view-analysis.png)

    您可以自訂視圖、更改顯示的時間段以及匯出資料。

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何在 Azure IoT 中央應用程式中使用設備組，下面是建議的下一步：

> [!div class="nextstepaction"]
> [如何建立遙測規則](tutorial-create-telemetry-rules.md)
