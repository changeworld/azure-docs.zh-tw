---
title: 選取自訂視覺專案的網域-電腦視覺
titleSuffix: Azure Cognitive Services
description: 本文將示範如何在自訂視覺服務中為您的專案選取網域。
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1fb30cc0634224213dc9a188a16902e07d379904
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82127764"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>選取自訂視覺專案的網域

您可以從自訂視覺專案的 [設定] 分頁中，為您的專案選取一個網域。 選擇最接近您案例的網域。

## <a name="image-classification"></a>影像分類

|網域|目的|
|---|---|
|__泛型__| 已針對廣泛的影像分類工作進行最佳化。 如果沒有其他適用的領域，或您不確定要選擇哪一個領域，請選取「泛型」領域。|
|__食物__|已針對菜餚相片進行最佳化，如同您在餐廳菜單上看見的一樣。 如果您想要將個別水果或蔬菜的相片分類，請使用「食物」領域。|
|__地標__|已針對可辨識的地標 (包括自然和人工) 進行最佳化。 地標在相片中清楚顯示時，此領域的效果最佳。 即使地標前面的人稍微阻擋到該地標，此領域還是能夠運作。|
|__零售__|已針對在購物目錄或購物網站上找到的影像進行最佳化。 如果您想在連衣裙、褲子和襯衫之間進行高精確度的分類，請使用此領域。|
|__精簡領域__| 已針對 edge 裝置上的即時分類條件約束進行優化。|

## <a name="object-detection"></a>物件偵測

|網域|目的|
|---|---|
|__一般__| 已針對廣泛的物件偵測工作進行最佳化。 如果沒有其他適用的領域，或您不確定要選擇哪一個領域，請選取「泛型」領域。|
|__標誌__|已針對尋找影像中的品牌標誌進行最佳化。|
|__貨架上的產品__|已針對在貨架上偵測和分類產品進行最佳化。|
|__精簡領域__| 已針對邊緣裝置上的即時物件偵測條件約束進行優化。|

## <a name="compact-domains"></a>精簡領域

精簡領域所產生的模型可以匯出到本機執行。 模型效能會因選取的網域而異。 在下表中，我們會報告 Intel 桌面 CPU 和 NVidia GPU 1 上的模型大小和推斷時間 \[ \] 。 

> [!NOTE]
> 這些數位不包括前置處理和後處理時間。

|Task|網域|模型大小|CPU 推斷時間|GPU 推斷時間|
|---|---|---|---|---|
|分類|一般 (精簡)|5 MB|13 毫秒|5 毫秒|
|物件偵測|一般 (精簡)|45 MB|35毫秒|5 毫秒|
|物件偵測|一般 (compact) [S1]|14 MB|27毫秒|7 毫秒|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (視覺 AI 開發工具組) 

如果選取了 compact 網域，則會提供額外的「匯出功能」選項，讓您能夠區別「基本平臺」和「視覺 AI 開發工具組」。

在 [ _匯出功能_ ] 下，兩個選項如下：

- 基本平臺 (Tensorflow、CoreML、ONNX 等 ) 
- 視覺 AI 開發工具組。

當您選取 [ _視覺 AI 開發工具組_ ] 時，[ _一般_]、[ _地標_] 和 [ _零售_ ]，而非 [ _食物_ ] compact 網域可用於影像分類，而 _一般 (compact) _ 和 _一般 (compact) [S1]_ 可用於物件偵測。

>[!NOTE]
>用於物件偵測的__一般 (compact) __網域需要特殊的後處理邏輯。 如需詳細資料，請參閱匯出的 zip 套件中的範例腳本。 如果您需要不含後處理邏輯的模型，請使用 __一般 (compact) [S1]__。

>[!IMPORTANT]
>無法保證匯出的模型會提供與雲端上的預測 API 完全相同的結果。 在執行中的平臺或前置處理的實作為微差異，可能會導致模型輸出中有較大的差異。 如需前置處理邏輯的詳細資料，請參閱 [這份檔](quickstarts/image-classification.md)。

\[1 \] Intel 以上的 E5-2690 CPU 和 NVIDIA Tesla M60
