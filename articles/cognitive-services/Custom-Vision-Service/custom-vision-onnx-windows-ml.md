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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82594290"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>搭配使用自訂視覺中的 ONNX 模型與 Windows ML (預覽)

了解如何搭配使用從自訂視覺匯出的 ONNX 模型與 Windows ML (預覽)。

在本指南中，您將瞭解如何使用透過 Windows ML 從自訂視覺服務匯出的 ONNX 檔案。 您將使用範例 UWP 應用程式搭配您自己的定型影像分類器。

## <a name="prerequisites"></a>Prerequisites

* Windows 10 (版本 1809 或更高版本)
* 適用於組建 17763 或更新版本的 Windows SDK
* 啟用__通用 Windows 平台開發__工作負載的 Visual Studio 2017 15.7 版或更新版本。
* 在您的電腦上啟用開發人員模式。 如需詳細資訊，請參閱 [啟用您的裝置以進行開發](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)。

## <a name="about-the-example-app"></a>關於範例應用程式

包含的應用程式是一般的 Windows UWP 應用程式。 它可讓您從電腦選取影像，然後由本機儲存的分類模型進行處理。 模型所傳回的標籤和分數會顯示在映像旁邊。

## <a name="get-the-example-code"></a>取得範例程式碼

您可以在 GitHub 上的 [認知服務 ONNX 自訂視覺範例](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) 存放庫中取得範例應用程式。 將它複製到您的本機電腦，並在 Visual Studio 中開啟 *SampleOnnxEvaluationApp .sln* 。

## <a name="test-the-application"></a>測試應用程式

1. 使用 `F5` 鍵從 Visual Studio 中啟動應用程式。 系統可能會提示您啟用開發人員模式。
1. 當應用程式啟動時，使用此按鈕來選取映像進行評分。 預設的 ONNX 模型會經過定型，以分類不同類型的 plankton。

## <a name="use-your-own-model"></a>使用自有模型

若要使用您自己的影像分類器模型，請遵循下列步驟：

1. 使用自訂視覺服務建立及訓練分類器。 如需有關如何進行此動作的指示，請參閱 [建立和定型分類器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)。 使用其中一個 **compact** 網域，例如 **一般 (compact) **。 
   * 如果您有現有的分類器使用不同的網域，您可以在專案設定中將它轉換成 **compact** 。 然後，重新訓練您的專案，再繼續進行。
1. 匯出您的模型。 切換至 [效能] 索引標籤，然後選取以 **compact** 網域定型的反復專案。 選取出現的 [ **匯出** ] 按鈕。 然後選取 [ **ONNX**]，再選取 [ **匯出**]。 檔案準備就緒後，請選取 [下載]**** 按鈕。 如需匯出選項的詳細資訊，請參閱 [匯出模型](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)。
1. 開啟所下載的 *.zip* 檔案，並從中解壓縮 *onnx* 檔案。 此檔案包含您的分類器模型。
1. 在 Visual Studio 的方案總管中，以滑鼠右鍵按一下 [ **資產** ] 資料夾，然後選取 [ __加入現有專案__]。 選取您的 ONNX 檔案。
1. 在方案總管中，以滑鼠右鍵按一下 ONNX 檔案，然後選取 [ **屬性**]。 變更檔案的下列屬性：
   * __建置動作__ -> __內容__
   * __複製到輸出目錄__ -> __有更新時才複製__
1. 然後開啟 _MainPage.xaml.cs_ ，並將的值變更 `_ourOnnxFileName` 為 ONNX 檔的名稱。
1. 使用 `F5` 建立及執行專案。
1. 按一下按鈕以選取要評估的映像。

## <a name="next-steps"></a>接下來的步驟

若要探索其他匯出及使用自訂視覺模型的方法，請參閱下列文件：

* [匯出您的模型](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [在 Android 應用程式中使用匯出的 Tensorflow 模型](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [在 Swift iOS 應用程式中使用匯出的 CoreML 模型](https://go.microsoft.com/fwlink/?linkid=857726)
* [搭配使用 iOS 應用程式中的 CoreML 模型與 Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

如需有關搭配使用 ONNX 模型與 Windows ML 的詳細資訊，請參閱使用 [WINDOWS ml 將模型整合到您的應用程式](/windows/ai/windows-ml/integrate-model)。
