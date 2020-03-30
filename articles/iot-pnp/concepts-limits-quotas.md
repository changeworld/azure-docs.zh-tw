---
title: 限制和配額 IoT 隨插即用預覽 |微軟文檔
description: 瞭解使用 IoT 隨插即用預覽時適用的限制、配額和限制。
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75531372"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT 隨插即用預覽限制、配額和限制

本文介紹了在公共預覽版中應用的特定于 IoT 的隨插即用限制、配額和限制。 還有適用的現有[IoT 中心配額和限制](../iot-hub/iot-hub-devguide-quotas-throttling.md)。

## <a name="iot-hub"></a>IoT 中樞

對於公共預覽版，以下限制和配額適用于 IoT 中心：

| 限制、限制和限制 | 值 | 注意 |
|-----|-----|-----|
| 每個中心可以註冊的裝置功能模型 （DCM） 或介面的數量 | 1500 ||
| 每個設備可註冊的最大介面數 | 40 ||
| 每個設備可註冊的最大 DCM 數量 | 1 ||
| 介面/DCM 檔的最大大小 | 512 字元 ||
| 介面名稱的最大大小 | 256 字元 ||
| 屬性名稱的最大大小  | 64 位元組，7 個深度級別（第一個級別保留為`$iotin`） | 允許的字元：a-z、A-Z、0-9（不作為第一個字元）和底線。 |
| 屬性值的最大大小 | 512 個位元組 ||
| 命令名稱的最大大小 | 100 位元組 ||
| 裝置對應項大小 | 與[IoT 中心限制相同](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| 跨 SKU 解析 API 呼叫（無論單位數如何） | 100 請求/秒 ||

## <a name="model-repository"></a>模型存儲庫

對於公共預覽，以下限制和配額適用于模型存儲庫：

| 限制、限制和限制| 值 |
|-----|-----|
| 每個 Azure 活動目錄租戶的公司模型存儲庫數 | 1 |
| 每個模型存儲庫的授權金鑰數 | 10  |
| 每個公司模型存儲庫的模型（DCM 或介面）數| 1500  |
| 每個 Azure 活動目錄租戶的公共模型存儲庫中的模型（DCM 或介面）數| 1500  |
| 在公司模型存儲庫中正在刪除的 DcM 或介面數 | 每秒 10 個查詢 （QPS）|
| 租戶正在創建/更新的模型存儲庫數| 1 QPS |
| 在模型存儲庫中創建/更新/刪除的授權金鑰數 | 1 QPS|
| 在公司模型存儲庫中創建的 DCM 數量 | 10 QPS |
| 在公司模型存儲庫中創建的介面數 | 10 QPS|
| 在公共模型存儲庫中創建的 DCM 數量 | 10 QPS|
| 在公共模型存儲庫中創建的介面數 | 10 QPS|

## <a name="parser-library"></a>解析器庫

解析器庫遵循適用于[數位雙定義語言](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)的限制。

## <a name="next-steps"></a>後續步驟

建議的下一步是學習如何[連接到 IoT 隨插即用裝置並與之交互](./howto-develop-solution.md)。
