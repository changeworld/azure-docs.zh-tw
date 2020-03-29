---
title: 為自訂語音 - 語音服務部署模型
titleSuffix: Azure Cognitive Services
description: 在本文檔中，您將瞭解如何使用自訂語音門戶創建和部署終結點。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: e41653e4644b09cc357510223e5ee86c3806ae2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806108"
---
# <a name="deploy-a-custom-model"></a>部署自訂模型

上傳和檢查資料、評估準確性並訓練自訂模型後，可以部署自訂終結點以與應用、工具和產品一起使用。 在本文檔中，您將學習如何使用[自訂語音門戶](https://speech.microsoft.com/customspeech)創建和部署終結點。

## <a name="create-a-custom-endpoint"></a>創建自訂終結點

要創建新的自訂終結點，請登錄到["自訂語音"門戶](https://speech.microsoft.com/customspeech)，然後從頁面頂部的"自訂語音"功能表中選擇 **"部署**"。 如果這是您第一次運行，您會注意到表中未列出終結點。 創建終結點後，將使用此頁跟蹤每個已部署的終結點。

接下來，選擇 **"添加終結點**"並輸入自訂終結點**的名稱**和**說明**。 然後選擇要與此終結點關聯的自訂模型。 在此頁面中，您還可以啟用日誌記錄。 日誌記錄允許您監視終結點流量。 如果禁用，將不會存儲流量。

![如何部署模型](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 不要忘記接受使用條款和定價詳細資訊。

接下來，選取 [建立]****。 此操作將返回到 **"部署"** 頁。 該表現在包含一個對應于自訂終結點的條目。 終結點的狀態顯示其目前狀態。 使用自訂模型具現化新終結點最多可能需要 30 分鐘。 當部署的狀態更改為 **"完成"** 時，終結點已準備就緒，可以使用。

部署終結點後，終結點名稱將顯示為連結。 按一下連結可顯示特定于終結點的資訊，例如終結點金鑰、終結點 URL 和示例代碼。

## <a name="view-logging-data"></a>查看日誌記錄資料

日誌記錄資料可在 **"端點>詳細資訊**"下下載。

## <a name="next-steps"></a>後續步驟

* 將自訂終結點與語音[SDK](speech-sdk.md)一起使用

## <a name="additional-resources"></a>其他資源

* [準備和測試資料](how-to-custom-speech-test-data.md)
* [檢查資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [定型模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
