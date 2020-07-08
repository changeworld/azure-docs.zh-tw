---
author: rothja
ms.service: iot-dps
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: bbe928084a758335e5fa3b0531726c77a6d599b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85839069"
---
下表列出適用於 Azure IoT 中樞裝置佈建服務資源的限制。

| 資源 | 限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的裝置佈建服務上限 | 10 |
| 註冊數目上限 | 1,000,000 |
| 登錄數目上限 | 1,000,000 |
| 註冊群組數目上限 | 100 |
| CA 數目上限 | 25 |
| 連結的 IoT 中樞數目上限 | 50 |
| 訊息大小上限 | 96 KB|

> [!NOTE]
> 若要提高佈建服務的註冊和登錄數目，請連絡 [Microsoft 支援服務](https://azure.microsoft.com/support/options/)。

> [!NOTE]
> 不支援增加 CA 數目上限。

裝置佈建服務會在超過下列配額時開始對要求進行節流。

| 節流 | 每個單位值 |
| --- | --- |
| 作業 | 200/分鐘/服務 |
| 裝置登錄 | 200/分鐘/服務 |
| 裝置輪詢作業 | 5/10 秒/裝置 |
