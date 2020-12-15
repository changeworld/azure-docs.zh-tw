---
title: 建立新的 IoT 模擬 - Azure | Microsoft Docs
description: 在本教學課程中，您將使用裝置模擬來建立和執行新的模擬。
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: e0d7310c4863c8dd431b991a2c249f2d8e257aeb
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852348"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>教學課程：建立和執行 IoT 裝置模擬

在本教學課程中，您將使用裝置模擬和一或多個模擬裝置來建立及執行新的 IoT 模擬。

在本教學課程中，您：

>[!div class="checklist"]
> * 檢視所有作用中和歷史模擬
> * 建立和執行新的模擬
> * 檢視模擬的計量
> * 停止模擬

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您在 Azure 訂用帳戶中必須要有一個已部署的裝置模擬執行個體。

如果您尚未部署裝置模擬，請參閱 GitHub 上的[裝置模擬部署](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)。

## <a name="view-simulations"></a>檢視模擬

選取功能表列中的 [模擬]。 [模擬] 頁面會顯示所有可用模擬的相關資訊。 在此頁面中，您可以檢視目前正在執行及先前曾執行的模擬。 若要重新執行先前的模擬，請按一下模擬圖格以開啟模擬的詳細資料：

![模擬](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>建立模擬

若要建立模擬，請按一下位於右上角的 [+ 新增模擬]。

由一或多個裝置型號組成的模擬。 裝置型號會針對要模擬的裝置定義其行為、遙測和訊息格式。

若要新增裝置型號，請按一下 [+ 新增裝置類型]，並從清單中選取裝置型號。 您的模擬可以有多個裝置型號。 每個裝置型號可以有不同的裝置計數和訊息頻率。

新增模擬表單完成後，請按一下 [開始模擬] 以開始進行模擬。

根據模擬裝置的數目，模擬可能需要幾分鐘才能完成設定並啟動：

![新增模擬](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>停止模擬

按一下 [開始模擬] 後，您會看到模擬詳細資料頁面。 此詳細資料頁面會顯示來自 IoT 中樞的即時模擬統計資料和計量。 您也可以在 [模擬] 頁面上按一下模擬的圖格，以瀏覽至此詳細資料頁面。

若要停止模擬，請在右上方的動作列中按一下 [停止模擬]。

![停止模擬](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立、執行和停止模擬。 您也已了解如何檢視模擬詳細資料。 若要深入了解如何執行模擬，請繼續進行下一個教學課程：

> [!div class="nextstepaction"]
> [建立自訂模擬裝置](iot-accelerators-device-simulation-create-custom-device.md)
