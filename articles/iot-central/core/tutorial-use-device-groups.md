---
title: 在 Azure IoT 集中應用程式中使用裝置群組 |微軟文件
description: 作為操作員,瞭解如何使用設備組分析 Azure IoT 集中應用程式中設備的遙測數據。
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 58fc71ab05c34e8acd252e7a1984c55996d1b3a7
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80999029"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>教學:使用裝置組分析設備遙測

本文介紹作為操作員如何使用設備組分析 Azure IoT 集中應用程式中的設備遙測。

設備組是分組在一起的設備的清單,因為它們符合某些指定的條件。 設備組通過將設備分組到較小的邏輯組,説明您大規模管理、可視化和分析設備。 例如,您可以創建一個設備組來列出西雅圖的所有空調設備,使技術人員能夠找到他們負責的設備。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立裝置群組
> * 使用裝置群組分析裝置遙測

## <a name="prerequisites"></a>Prerequisites

開始之前，請先完成[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md) (英文) 和[將模擬裝置新增至 IoT Central 應用程式](./quick-create-simulated-device.md) (英文) 的快速入門，以便建立要使用的 **MXChip IoT DevKit** 裝置範本。

## <a name="create-simulated-devices"></a>建立模擬裝置

在建立裝置群組之前,請從**MXChip IoT DevKit**裝置樣本中添加至少五個模擬設備,以在本教學中使用:

![五個模擬感應器裝置](./media/tutorial-use-device-groups/simulated-devices.png)

對於四個模擬感應器裝置,請使用 **「管理裝置」** 檢視將客戶名稱設定為*Contoso:*

![將客戶名稱設定為 Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>建立裝置群組

要建立裝置群組,請:

1. 選擇左邊窗格上的**裝置群組**。

1. 選擇**+**:

    ![新裝置群組](media/tutorial-use-device-groups/image1.png)

1. 為設備組指定*Contoso 裝置*的名稱。 您也可以新增描述。 設備組只能包含單個設備範本中的設備。 選擇要用於此組的**MXChip IoT DevKit**設備範本。

1. 要自訂裝置組以僅包括屬於**Contoso**的裝置,請選擇 **『篩選器**』。 選擇 **「客戶名稱**」屬性 **、「等於」** 比較運算子和**Contoso**作為值。 您可以添加多個滿足**所有**篩選器條件的篩選器和設備,這些篩選器和設備將放置在設備群組中。 有權存取應用程式的任何人都可以存取您建立的裝置群組,因此任何人都可以查看、修改或刪除裝置群組:

    ![裝置組查詢](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > 設備組是動態查詢。 您每次檢視裝置清單時所看到的裝置可能會不同。 清單內容取決於哪些裝置目前符合查詢準則。

1. 選擇 [儲存]****。

> [!NOTE]
> 對於 Azure IoT 邊緣設備,請選擇 Azure IoT 邊緣範本以創建設備組。

## <a name="analytics"></a>分析

您可以將**Analytics"分析"** 與設備組一起分析組中設備的遙測數據。 例如,您可以繪製所有 Contoso 環境感測器報告的平均溫度。

要分析裝置群組的遙測資料,請處理:

1. 在左側窗格中選擇 **「分析**」。

1. 選擇您建立的**Contoso 裝置**裝置群組。 然後新增**溫度**與**濕度**遙測類型:

    ![建立分析](./media/tutorial-use-device-groups/create-analysis.png)

    使用遙測類型旁邊的齒輪圖標選擇聚合類型。 默認值為 **「平均值**」。 使用 **「拆分」** 可更改聚合資料的顯示方式。 例如,如果按設備 ID 拆分,則當您選擇 **「分析**」時,會看到每個裝置的繪圖。

1. 選擇 **「分析」** 以檢視平均遙測值:

    ![檢視分析](./media/tutorial-use-device-groups/view-analysis.png)

    您可以自定義檢視、更改顯示的時間段以及匯出數據。

## <a name="next-steps"></a>後續步驟

現在,您已經瞭解如何在 Azure IoT 中央應用程式中使用裝置組,下面是建議的下一步:

> [!div class="nextstepaction"]
> [如何建立遙測規則](tutorial-create-telemetry-rules.md)
