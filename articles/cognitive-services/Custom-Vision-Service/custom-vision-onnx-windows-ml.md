---
title: 搭配 Windows ML 使用 ONNX 模型 - 自訂視覺服務
titleSuffix: Azure Cognitive Services
description: 了解如何建立 Windows UWP 應用程式來使用從 Azure 認知服務匯出的 ONNX 模型。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: d6bcb5485833fbfaa3eb72191ee54b1ee69bab04
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594290"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>搭配使用自訂視覺中的 ONNX 模型與 Windows ML (預覽)

了解如何搭配使用從自訂視覺匯出的 ONNX 模型與 Windows ML (預覽)。

在本指南中，您將瞭解如何使用從自訂視覺服務匯出的 ONNX 檔案與 Windows ML。 您會將範例 UWP 應用程式與您自己的定型影像分類器搭配使用。

## <a name="prerequisites"></a>Prerequisites

* Windows 10 1809 版或更高版本
* 適用於組建 17763 或更新版本的 Windows SDK
* 啟用__通用 Windows 平台開發__工作負載的 Visual Studio 2017 15.7 版或更新版本。
* 已在您的電腦上啟用開發人員模式。 如需詳細資訊，請參閱[啟用您的裝置以進行開發](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)。

## <a name="about-the-example-app"></a>關於範例應用程式

所包含的應用程式是一般 Windows UWP 應用程式。 它可讓您從電腦選取映射，然後由本機儲存的分類模型進行處理。 模型所傳回的標籤和分數會顯示在映像旁邊。

## <a name="get-the-example-code"></a>取得範例程式碼

範例應用程式可于 GitHub 上的[認知服務 ONNX 自訂視覺範例](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample)存放庫取得。 將它複製到本機電腦，然後在 Visual Studio 中開啟*SampleOnnxEvaluationApp* 。

## <a name="test-the-application"></a>測試應用程式

1. 使用 `F5` 鍵從 Visual Studio 中啟動應用程式。 系統可能會提示您啟用開發人員模式。
1. 當應用程式啟動時，使用此按鈕來選取映像進行評分。 預設的 ONNX 模型已定型，可分類不同類型的 plankton。

## <a name="use-your-own-model"></a>使用自有模型

若要使用您自己的影像分類器模型，請遵循下列步驟：

1. 使用自訂視覺服務建立及訓練分類器。 如需如何執行此動作的指示，請參閱[建立和定型分類器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)。 使用其中一個**精簡**網域 **（例如一般（compact））**。 
   * 如果您有使用不同網域的現有分類器，您可以在專案設定中將它轉換為**compact** 。 然後，重新訓練您的專案，再繼續進行。
1. 匯出您的模型。 切換至 [效能] 索引標籤，然後選取使用**精簡**網域定型的反復專案。 選取出現的 [**匯出**] 按鈕。 然後選取 [ **ONNX**]，然後選取 [**匯出**]。 檔案準備就緒後，請選取 [下載]**** 按鈕。 如需匯出選項的詳細資訊，請參閱[匯出您的模型](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)。
1. 開啟下載的 *.zip*檔案，並從該檔案解壓縮*onnx*檔案。 此檔案包含您的分類器模型。
1. 在 Visual Studio 的方案總管中，以滑鼠右鍵按一下 [**資產**] 資料夾，然後選取 [新增] [__現有專案__]。 選取您的 ONNX 檔案。
1. 在方案總管中，以滑鼠右鍵按一下 ONNX 檔案，然後選取 [**屬性**]。 變更檔案的下列屬性：
   * __建置動作__ -> __內容__
   * __複製到輸出目錄__ -> __有更新時才複製__
1. 然後開啟_MainPage.xaml.cs_ ，並將的值`_ourOnnxFileName`變更為 ONNX 檔的名稱。
1. 使用`F5`來建立及執行專案。
1. 按一下按鈕以選取要評估的映像。

## <a name="next-steps"></a>後續步驟

若要探索其他匯出及使用自訂視覺模型的方法，請參閱下列文件：

* [匯出您的模型](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [在 Android 應用程式中使用匯出的 Tensorflow 模型](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [在 Swift iOS 應用程式中使用匯出的 CoreML 模型](https://go.microsoft.com/fwlink/?linkid=857726)
* [搭配使用 iOS 應用程式中的 CoreML 模型與 Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

如需搭配 Windows ML 使用 ONNX 模型的詳細資訊，請參閱使用[WINDOWS ml 將模型整合到您的應用程式](/windows/ai/windows-ml/integrate-model)。
