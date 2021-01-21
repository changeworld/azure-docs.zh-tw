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
ms.openlocfilehash: 2259d0114d91af2e688df66ff8dc4e8d293c2b03
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624824"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>選取自訂視覺專案的網域

您可以從自訂視覺專案的 [設定] 索引標籤中，選取專案的網域。 選擇最接近您案例的網域。 如果您要透過用戶端程式庫或 REST API 存取自訂視覺，您必須在建立專案時指定網域識別碼。 您可以取得具有 [取得網域](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab)的網域識別碼清單，或使用下表。

## <a name="image-classification"></a>影像分類

|網域|目的|
|---|---|
|__一般__| 已針對廣泛的影像分類工作進行最佳化。 如果沒有任何其他網域適合，或如果您不確定要選擇哪一個領域，請選取 [一般] 網域。 識別碼：`ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__一般 [A1]__| 使用可比較的推斷時間做為一般網域，針對較佳的精確度進行優化。 建議用於較大型的資料集或更困難的使用者案例。 此網域需要更多訓練時間。 識別碼：`a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|__食物__|已針對菜餚相片進行最佳化，如同您在餐廳菜單上看見的一樣。 如果您想要將個別水果或蔬菜的相片分類，請使用「食物」領域。 識別碼：`c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__地標__|已針對可辨識的地標 (包括自然和人工) 進行最佳化。 地標在相片中清楚顯示時，此領域的效果最佳。 即使地標前面的人稍微阻擋到該地標，此領域還是能夠運作。 識別碼：`ca455789-012d-4b50-9fec-5bb63841c793`|
|__零售__|已針對在購物目錄或購物網站上找到的影像進行最佳化。 如果您想要在連衣裙、褲子和襯衫之間進行高精確度的分類，請使用此網域。 識別碼：`b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__精簡領域__| 已針對 edge 裝置上的即時分類條件約束進行優化。|

## <a name="object-detection"></a>物件偵測

|網域|目的|
|---|---|
|__一般__| 已針對廣泛的物件偵測工作進行最佳化。 如果沒有任何其他網域適合，或您不確定要選擇哪一個領域，請選取 [一般] 網域。 識別碼：`da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__一般 [A1]__| 使用可比較的推斷時間做為一般網域，針對較佳的精確度進行優化。 建議您提供更精確的區域位置需求、較大型的資料集，或更難使用的使用者案例。 此網域需要更多定型時間，而且結果不具決定性：預期有 +-1% 的對應差異與提供的相同定型資料。 識別碼：`9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__標誌__|已針對尋找影像中的品牌標誌進行最佳化。 識別碼：`1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__貨架上的產品__|已針對在貨架上偵測和分類產品進行最佳化。 識別碼：`3780a898-81c3-4516-81ae-3a139614e1f3`|
|__精簡領域__| 已針對邊緣裝置上的即時物件偵測條件約束進行優化。|

## <a name="compact-domains"></a>精簡領域

精簡領域所產生的模型可以匯出到本機執行。 在自訂視覺3.4 公開預覽 API 中，您可以藉由呼叫 GetDomains API 取得 compact 網域的可匯出平臺清單。

模型效能會因選取的網域而異。 在下表中，我們會報告 Intel 桌面 CPU 和 NVidia GPU 1 上的模型大小和推斷時間 \[ \] 。 這些數位不包括前置處理和後處理時間。

|Task|網域|識別碼|模型大小|CPU 推斷時間|GPU 推斷時間|
|---|---|---|---|---|---|
|分類|一般 (精簡)|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 MB|10 毫秒|5 毫秒|
|分類|一般 (compact) [S1]|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43 MB|50毫秒|5 毫秒|
|物件偵測|一般 (精簡)|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35毫秒|5 毫秒|
|物件偵測|一般 (compact) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27毫秒|7 毫秒|

>[!NOTE]
>用於物件偵測的 __一般 (compact)__ 網域需要特殊的後處理邏輯。 如需詳細資料，請參閱匯出的 zip 套件中的範例腳本。 如果您需要不含後處理邏輯的模型，請使用 __一般 (compact) [S1]__。

>[!IMPORTANT]
>無法保證匯出的模型會提供與雲端上的預測 API 完全相同的結果。 在執行中的平臺或前置處理的實作為微差異，可能會導致模型輸出中有較大的差異。 如需前置處理邏輯的詳細資料，請參閱 [這份檔](quickstarts/image-classification.md)。

\[1 \] Intel 以上的 E5-2690 CPU 和 NVIDIA Tesla M60
