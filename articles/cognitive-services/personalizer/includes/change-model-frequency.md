---
title: 包含檔案
description: 包含檔案
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 08/25/2020
ms.openlocfilehash: 4eacc1c4e863ad1a278a4974bb0f6c101aafe7e0
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055364"
---
### <a name="change-the-model-update-frequency"></a>變更模型更新頻率

在 Azure 入口網站中，於個人化工具資源的 [設定]  頁面上，將 [模型更新頻率]  變更為 10 秒。 這個較短的時間將會快速定型服務，讓您可以查看名次最高的動作會如何針對每個反覆項目變更。

![變更模型更新頻率](../media/settings/configure-model-update-frequency-settings.png)

第一次具現化個人化工具迴圈時，因為已經沒有可進行訓練的獎勵 API 呼叫，所以沒有模型。 排名呼叫會針對每個項目傳回相等的機率。 您的應用程式應該仍會一直使用 RewardActionId 的輸出來排名內容。