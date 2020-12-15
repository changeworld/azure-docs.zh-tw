---
title: 建立自訂模擬裝置 - Azure | Microsoft Docs
description: 在本教學課程中，您將使用裝置模擬來建立要在模擬中使用的模擬裝置。
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 1fe86aef832223a7485036343b4b12d8bb526e06
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852382"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>教學課程：建立自訂模擬裝置

在本教學課程中，您將使用裝置模擬來建立要在模擬中使用的模擬裝置。 若要開始使用裝置模擬，您可以使用其中一個隨附的範例模擬裝置。 您也可以依照本文中的說明建立自訂模擬的裝置。 如需其他自訂選項，請參閱[建立進階裝置模型](iot-accelerators-device-simulation-advanced-device.md)。

在本教學課程中，您：

>[!div class="checklist"]
> * 檢視您的模擬裝置型號清單
> * 建立自訂模擬裝置
> * 複製裝置型號
> * 刪除裝置型號

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您在 Azure 訂用帳戶中必須要有一個已部署的裝置模擬執行個體。

如果您尚未部署裝置模擬，請參閱 GitHub 上的[裝置模擬部署](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)。

## <a name="view-your-device-models"></a>檢視您的裝置型號

選取功能表列中的 [裝置型號]  。 [裝置型號]  頁面會列出此裝置模擬執行個體中所有可用的裝置型號：

![裝置型號](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>建立裝置型號

按一下頁面右上角的 [+ 新增裝置型號]  ：

![新增裝置型號](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

在本教學課程中，您會建立同時傳送溫度和溼度資料的模擬冰箱。

在表單中填寫以下資訊：

| 設定             | 值                                                |
| ------------------- | ---------------------------------------------------- |
| 裝置型號名稱   | 冰箱                                         |
| 模型描述   | 具有溫度和溼度感應器的冰箱 |
| 版本             | 1.0                                                  |

> [!NOTE]
> 裝置型號名稱必須是唯一的。

按一下 [+ 新增資料點]  ，以下列值新增溫度和溼度的資料點：

| 資料點          | 行為        | 最小值 | 最大值 | 單位 |
| ------------------- | --------------- | --------- | --------- | ---- |
| 溫度         | 隨機          | -50       | 100       | F    |
| 溼度            | 隨機          | 0         | 100       | %    |

按一下 [儲存]  以儲存裝置型號。

![建立裝置型號](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

您的冰箱現在已包含在裝置型號清單中。 您可能需要按 [下一步]  移至另一個頁面，以查看您的冰箱。

## <a name="clone-a-device-model"></a>複製裝置型號

複製裝置型號，可讓您建立現有裝置型號的複本。 接著，您可以本身的特定需求編輯該複本。 在需要建立類似的裝置型號時，複製將可節省時間。

複製裝置型號，請核取型號旁的方塊，然後按一下動作列中的 [複製]  ：

![醒目提示所選模型和 [複製] 按鈕的螢幕擷取畫面。](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>刪除裝置型號

您可以刪除任何自訂裝置型號。 若要刪除裝置型號，請核取型號旁的方塊，然後按一下動作列中的 [刪除]  ：

![刪除裝置型號](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立、複製和刪除自訂裝置型號。 若要深入了解裝置型號，請參閱下列操作說明文章：

> [!div class="nextstepaction"]
> [建立進階裝置型號](iot-accelerators-device-simulation-advanced-device.md)