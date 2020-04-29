---
title: 將模型匯出至行動裝置 - 自訂視覺服務
titleSuffix: Azure Cognitive Services
description: 本文將說明如何匯出您的模型，以用於建立行動應用程式，或在本機執行以進行即時分類。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73718946"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>匯出您的模型以用於行動裝置

自訂視覺服務可讓分類器匯出而離線執行。 您可以將匯出的分類器內嵌到應用程式中，並裝置的本機執行以進行即時分類。

## <a name="export-options"></a>匯出選項

自訂視覺服務支援下列匯出：

* __Android__ 的 __Tensorflow__。
* __iOS11__ 的 __CoreML__。
* __Windows ML__ 的 __ONNX__。
* __[視覺 AI 開發人員套件](https://azure.github.io/Vision-AI-DevKit-Pages/)__。
* 適用于 Windows、Linux 或 ARM 架構的__Docker 容器__。 容器包含 Tensorflow 模型和服務程式代碼，以使用自訂視覺 API。

> [!IMPORTANT]
> 自訂視覺服務只會匯出__密集__區域。 密集區域所產生的模型會針對行動裝置的即時分類條件約束進行最佳化。 以密集區域建置的分類器精確性可能會略低於具有等量訓練資料的標準區域。
>
> 如需改善分類器的相關資訊，請參閱[改善您的分類器](getting-started-improving-your-classifier.md)文件。

## <a name="convert-to-a-compact-domain"></a>轉換為密集區域

> [!NOTE]
> 本節中的步驟僅適用于您有未設定為 compact domain 的現有模型時。

若要轉換現有模型的網域，請執行下列步驟：

1. 在[自訂視覺網站](https://customvision.ai)上，選取 [__首頁__] 圖示，以查看您的專案清單。

    ![首頁圖示和專案清單的影像](./media/export-your-model/projects-list.png)

1. 選取專案，然後選取頁面右上方的 [齒輪]____ 圖示。

    ![齒輪圖示的影像](./media/export-your-model/gear-icon.png)

1. 在 [__網域__] 區段中，選取其中一個__compact__網域。 選取 [儲存變更]____ 以儲存變更。 

    > [!NOTE]
    > 針對視覺 AI 開發工具組，必須使用__一般（精簡）__ 網域來建立專案，而且您必須在 [**匯出功能**] 區段下指定 [**視覺 ai 開發工具組**] 選項。

    ![區域選取項目的影像](./media/export-your-model/domains.png)

1. 在頁面頂端選取 [訓練]____，以使用新的區域進行重新訓練。

## <a name="export-your-model"></a>匯出您的模型

若要在重新訓練之後匯出模型，請使用下列步驟：

1. 移至 [效能]**** 索引標籤，並選取 [匯出]____。 

    ![匯出圖示的影像](./media/export-your-model/export.png)

    > [!TIP]
    > 如果 [匯出]____ 項目無法使用，則選取的反覆項目不會使用密集區域。 請使用此頁面的 [反覆項目]____ 區段選取使用密集區域的反覆項目，然後選取 [匯出]____。

1. 選取您想要的匯出格式，然後選取 [__匯出__] 以下載模型。

## <a name="next-steps"></a>後續步驟

藉由流覽下列其中一個文章或範例，將您匯出的模型整合到應用程式：

* [搭配 Python 使用您的 Tensorflow 模型](export-model-python.md)
* [搭配 Windows Machine Learning 使用您的 ONNX 模型](custom-vision-onnx-windows-ml.md)
* 請參閱[iOS 應用程式中的 CoreML 模型](https://go.microsoft.com/fwlink/?linkid=857726)範例，以取得使用 Swift 的即時影像分類。
* 請參閱[android 應用程式中的 Tensorflow 模型](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)範例，以取得 android 上的即時影像分類。
* 如需 Xamarin iOS 應用程式中的即時影像分類，請參閱[使用 xamarin 進行 CoreML 模型](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)的範例。
