---
title: 教學課程 - 在 Azure IoT Central 應用程式中使用裝置群組 | Microsoft Docs
description: 教學課程 - 身為操作員，了解如何在 Azure IoT Central 應用程式中使用裝置群組分析來自裝置的遙測。
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 3192a9f121d4380a3e681747596fc91997662bf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967938"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>教學課程：使用裝置群組來分析裝置遙測

本文說明身為操作員如何在 Azure IoT Central 應用程式中使用裝置群組來分析裝置遙測。

裝置群組是一份裝置清單，這些裝置因為都符合某些指定準則而群組在一起。 裝置群組可藉由將裝置分組到較小的邏輯群組，來協助您大規模地對裝置進行管理、視覺化和分析。 例如，您可以建立裝置群組以列出西雅圖所有空調裝置，讓技師可以找到其負責的裝置。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立裝置群組
> * 使用裝置群組來分析裝置遙測

## <a name="prerequisites"></a>必要條件

開始之前，請先完成[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md) (英文) 和[將模擬裝置新增至 IoT Central 應用程式](./quick-create-simulated-device.md) (英文) 的快速入門，以便建立要使用的 **MXChip IoT DevKit** 裝置範本。

## <a name="create-simulated-devices"></a>建立模擬裝置

建立裝置群組之前，請先從 **MXChip IoT DevKit** 裝置範本新增至少五部模擬裝置，以在本教學課程中使用：

![五個模擬感應器裝置](./media/tutorial-use-device-groups/simulated-devices.png)

若為四個模擬感應器裝置，請使用 [管理裝置] 檢視，將客戶名稱設定為「Contoso」：

![將客戶名稱設定為 Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>建立裝置群組

若要建立裝置群組：

1. 選擇左側面板上的 [裝置群組]。

1. 選取 **+** ：

    ![新增裝置群組](media/tutorial-use-device-groups/image1.png)

1. 為裝置群組提供「Contoso 裝置」這個名稱。 您也可以新增描述。 裝置群組只能包含來自單一裝置範本的裝置。 選擇要用於此群組的 **MXChip IoT DevKit** 裝置範本。

1. 若要自訂裝置群組，僅包含屬於 **Contoso** 的裝置，請選取 [+ 篩選]。 選取**客戶名稱**屬性、**等於**比較運算子，然後選取 **Contoso** 作為值。 您可以新增多個篩選條件，而符合**所有**準則的裝置則會放置在裝置群組中。 能存取應用程式的任何人都能存取您所建立的裝置群組，因此任何人都能檢視、修改或刪除裝置群組：

    ![裝置群組查詢](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > 裝置群組是動態的查詢。 您每次檢視裝置清單時所看到的裝置可能會不同。 清單內容取決於哪些裝置目前符合查詢準則。

1. 選擇 [儲存]  。

> [!NOTE]
> 針對 Azure IoT Edge 裝置，請選取 Azure IoT Edge 範本來建立裝置群組。

## <a name="analytics"></a>分析

您可以搭配裝置群組使用 [分析]，從群組中的裝置分析遙測。 例如，您可以繪製所有 Contoso 環境感應器所報告的平均溫度。

若要分析裝置群組的遙測：

1. 選擇左窗格中的 [分析]。

1. 選取您建立的 **Contoso 裝置**裝置群組。 然後，新增 [溫度] 和 [濕度] 遙測類型：

    ![建立分析](./media/tutorial-use-device-groups/create-analysis.png)

    使用遙測類型旁的齒輪圖示來選取彙總類型。 預設值為 [平均]。 使用 [分割依據] 來變更彙總資料的顯示方式。 例如，如果您依裝置識別碼分割，當您選取 [分析] 時，就會看到每個裝置的繪圖。

1. 選取 [分析] 以檢視平均遙測值：

    ![檢視分析](./media/tutorial-use-device-groups/view-analysis.png)

    您可以自訂此檢視、變更所顯示的時間週期，以及匯出資料。

## <a name="next-steps"></a>下一步

您現在已了解如何在 Azure IoT Central 應用程式中使用裝置群組，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [如何建立遙測規則](tutorial-create-telemetry-rules.md)
