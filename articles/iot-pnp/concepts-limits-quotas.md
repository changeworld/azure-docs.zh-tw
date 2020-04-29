---
title: 限制 IoT 隨插即用預覽的配額 |Microsoft Docs
description: 瞭解使用 IoT 隨插即用 Preview 時適用的限制、配額和節流。
author: miagdp
ms.author: miag
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cce99b7d9de09134fd01afb36c41bce3966e8536
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80518177"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT 隨插即用預覽限制、配額和節流

本文說明在公開預覽中適用的 IoT 隨插即用特定的限制、配額和節流。 現有的[IoT 中樞配額和節流](../iot-hub/iot-hub-devguide-quotas-throttling.md)也適用。

## <a name="iot-hub"></a>IoT 中樞

對於公開預覽，下列限制和配額適用于 IoT 中樞：

| 限制、限制和節流 | 值 | 備忘錄 |
|-----|-----|-----|
| 可在每個中樞註冊的裝置功能模型（DCMs）或介面數目 | 1500 ||
| 每個裝置可以註冊的介面數目上限 | 40 ||
| 每個裝置可以註冊的 DCMs 數目上限 | 1 ||
| 介面/DCM 檔案的大小上限 | 512 KB ||
| 介面名稱的大小上限 | 256個字元 ||
| 屬性名稱的大小上限  | 64個位元組，深度7個層級（而第一個層級`$iotin`是保留給） | 允許的字元： a-z、a-z、0-9 （不是第一個字元）和底線。 |
| 屬性值的大小上限 | 512 個位元組 ||
| 命令名稱的大小上限 | 100個位元組 ||
| 裝置對應項大小 | 與[IoT 中樞限制](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size)相同 ||
| 跨 SKU 的解析 API 呼叫（不論單位為何） | 每秒100要求 ||

## <a name="model-repository"></a>模型存放庫

對於公開預覽，下列限制和配額適用于模型存放庫：

| 限制、限制和節流 | 值 |
|-----|-----|
| 每個 Azure Active Directory 租使用者的公司型號存放庫數目 | 1 |
| 每個模型存放庫的授權金鑰數目 | 10  |
| 每個公司模型存放庫的模型數目（DCMs 或介面）| 1500  |
| 每個 Azure Active Directory 租使用者的公用模型存放庫中的模型數目（DCMs 或介面）| 1500  |
| 在公司模型存放庫中刪除的 DCMs 或介面數目 | 每秒10個查詢（QPS）|
| 租使用者所建立/更新的模型存放庫數目| 1 QPS |
| 在模型存放庫中建立/更新/刪除的授權金鑰數目 | 1 QPS|
| 在公司模型存放庫中建立的 DCMs 數目 | 10 QPS |
| 在公司模型存放庫中建立的介面數目 | 10 QPS|
| 在公用模型存放庫中建立的 DCMs 數目 | 10 QPS|
| 在公用模型存放庫中建立的介面數目 | 10 QPS|

## <a name="parser-library"></a>剖析器程式庫

剖析器程式庫遵循適用于數位對應項[定義語言](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)的限制。

## <a name="next-steps"></a>後續步驟

建議的下一個步驟是瞭解如何[連接 IoT 隨插即用裝置並與其互動](./howto-develop-solution.md)。
