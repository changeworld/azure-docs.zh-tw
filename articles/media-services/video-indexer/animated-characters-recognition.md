---
title: 使用影片索引子進行動畫字元偵測
titleSuffix: Azure Media Services
description: 本主題示範如何使用影片索引子的動畫字元偵測。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854745"
---
# <a name="animated-character-detection-preview"></a>動畫角色偵測 (預覽)

Azure 媒體服務影片索引子可透過與 [認知服務自訂視覺](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)的整合，支援在動畫內容中偵測、群組和辨識字元。 這項功能可透過入口網站和 API 來取得。

上傳具有特定動畫模型的動畫影片之後，影片索引子會將主要畫面格解壓縮、偵測這些畫面格中的動畫字元、將類似的字元分組，然後選擇最佳範例。 然後，它會將群組字元傳送給自訂視覺，以根據其定型的模型來識別字元。 

開始訓練模型之前，會 namelessly 字元偵測到。 當您新增名稱並將模型定型時，影片索引子會辨識這些字元並據以命名。

## <a name="flow-diagram"></a>流程圖

下圖示范動畫字元偵測程式的流程。

![流程圖](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>帳戶

視您的影片索引子帳戶類型而定，有不同的功能組可用。 如需有關如何將您的帳戶連線至 Azure 的資訊，請參閱 [建立連線到 azure 的影片索引子帳戶](connect-to-azure.md)。

* 試用帳戶：影片索引子會使用內部自訂視覺帳戶來建立模型，並將其連接到您的影片索引子帳戶。 
* 付費帳戶：您將自訂視覺帳戶連接到您的影片索引子帳戶 (如果您還沒有帳戶，則必須先) 建立帳戶。

### <a name="trial-vs-paid"></a>試用版與付費

|功能|試用版|已支付|
|---|---|---|
|自訂視覺帳戶|由影片索引子在幕後進行管理。 |您的自訂視覺帳戶已連線到影片索引子。|
|動畫模型的數目|一個|每個帳戶最多100個模型 (自訂視覺限制) 。|
|定型模型|影片索引子會為新字元定型模型，以尋找現有字元的其他範例。|帳戶擁有者準備好進行變更時，會訓練模型。|
|自訂視覺中的 Advanced 選項|無法存取自訂視覺入口網站。|您可以在自訂視覺入口網站中自行調整模型。|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>透過入口網站和 API 使用動畫字元偵測

如需詳細資訊，請參閱搭配 [使用動畫字元偵測和入口網站和 API](animated-characters-recognition-how-to.md)。

## <a name="limitations"></a>限制

* East-Asia 區域目前不支援「動畫識別」功能。
* 如果影片的品質很差，則可能無法正確識別出現在影片中的字元。
* 建議您針對每一組動畫字元使用模型 (例如，每個動畫系列) 。

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)