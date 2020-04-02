---
title: 限制和配額 IoT 隨插即用預覽 |微軟文件
description: 瞭解使用 IoT 隨插即用預覽時適用的限制、配額和限制。
author: miagdp
ms.author: miag
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cce99b7d9de09134fd01afb36c41bce3966e8536
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518177"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT 隨插即用預覽限制、配額和限制

本文介紹了在公共預覽版中應用的特定於 IoT 的隨插即用限制、配額和限制。 還有適用的現有[IoT 中心配額和限制](../iot-hub/iot-hub-devguide-quotas-throttling.md)。

## <a name="iot-hub"></a>IoT 中樞

對於公共預覽版,以下限制和配額適用於 IoT 中心:

| 限制、限制和限制 | 值 | 注意 |
|-----|-----|-----|
| 每個中心可以註冊的裝置功能模型 (DCM) 或介面的數量 | 1500 ||
| 每個裝置可註冊的最大介面數 | 40 ||
| 每個裝置可註冊的最大 DCM 數量 | 1 ||
| 介面/DCM 檔案的最大大小 | 512 KB ||
| 介面名稱的最大大小 | 256 字元 ||
| 屬性名稱的最大大小  | 64 位元組,7 個深度等級(第一個`$iotin`等級保留為 ) | 允許的字元:a-z、A-Z、0-9(不作為第一個字元)和下劃線。 |
| 屬性值的最大大小 | 512 個位元組 ||
| 命令名稱的最大大小 | 100 位元組 ||
| 裝置對應項大小 | 與[IoT 中心限制相同](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| 跨 SKU 解析 API 呼叫(無論單位數如何) | 100 個要求/秒 ||

## <a name="model-repository"></a>模型儲存庫

對於公共預覽,以下限制和配額適用於模型存儲庫:

| 限制、限制和限制 | 值 |
|-----|-----|
| 每個 Azure 活動目錄租戶的公司模型儲存庫數 | 1 |
| 每個模型儲存函式庫的授權金鑰數 | 10  |
| 每個公司模型儲存庫的模型(DCM 或介面)數| 1500  |
| 每個 Azure 活動目錄租戶的公共模型儲存庫中的模型(DCM 或介面)數| 1500  |
| 在公司模型儲存庫中正在移除的 DcM 或介面數 | 每秒 10 個查詢 (QPS)|
| 租戶正在建立/更新的模型儲存庫數| 1 QPS |
| 在模型儲存庫中建立/更新/刪除的授權金鑰數 | 1 QPS|
| 在公司模型儲存庫中建立的 DCM 數量 | 10 QPS |
| 在公司模型儲存庫中建立的介面數 | 10 QPS|
| 在公共模型儲存庫中建立的 DCM 數量 | 10 QPS|
| 在公共模型儲存庫中建立的介面數 | 10 QPS|

## <a name="parser-library"></a>解析程式庫

解析器庫遵循適用於[數位雙定義語言](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)的限制。

## <a name="next-steps"></a>後續步驟

建議的下一步是學習如何[連線到 IoT 插即用裝置並與之互動](./howto-develop-solution.md)。
