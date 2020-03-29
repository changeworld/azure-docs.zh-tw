---
title: 配置 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: IoT 邊緣事件網格中的配置。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76846465"
---
# <a name="event-grid-configuration"></a>事件網格配置

事件網格提供了許多配置，可以按環境進行修改。 以下部分是對所有可用選項及其預設值的引用。

## <a name="tls-configuration"></a>TLS 配置

要瞭解用戶端身份驗證，請參閱[安全和身份驗證](security-authentication.md)。 其用法示例可[在本文中](configure-api-protocol.md)找到。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| 事件網格模組的 TLS 策略。 預設值僅為 HTTPS。
|`inbound__serverAuth__serverCert__source`| 事件網格模組用於其 TLS 配置的伺服器憑證源。 預設值為 IoT 邊緣。

## <a name="incoming-client-authentication"></a>傳入用戶端身份驗證

要瞭解用戶端身份驗證，請參閱[安全和身份驗證](security-authentication.md)。 [在本文中](configure-client-auth.md)可以找到示例。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| 打開/關閉基於證書的用戶端身份驗證。 預設值為 true。
|`inbound__clientAuth__clientCert__source`| 用於驗證用戶端憑證的源。 預設值為 IoT 邊緣。
|`inbound__clientAuth__clientCert__allowUnknownCA`| 允許自簽名用戶端憑證的策略。 預設值為 true。
|`inbound__clientAuth__sasKeys__enabled`| 打開/關閉基於 SAS 金鑰的用戶端身份驗證。 預設值已關閉。
|`inbound__clientAuth__sasKeys__key1`| 用於驗證傳入請求的值之一。
|`inbound__clientAuth__sasKeys__key2`| 用於驗證傳入請求的可選第二個值。

## <a name="outgoing-client-authentication"></a>傳出用戶端身份驗證
要瞭解用戶端身份驗證，請參閱[安全和身份驗證](security-authentication.md)。 [在本文中](configure-identity-auth.md)可以找到示例。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| 打開/關閉為傳出請求附加標識證書。 預設值為 true。
|`outbound__clientAuth__clientCert__source`| 用於檢索事件網格模組的傳出證書的源。 預設值為 IoT 邊緣。

## <a name="webhook-event-handlers"></a>Webhook 事件處理常式

要瞭解用戶端身份驗證，請參閱[安全和身份驗證](security-authentication.md)。 [在本文中](configure-webhook-subscriber-auth.md)可以找到示例。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| 用於控制是否僅允許 HTTPS 訂閱者的策略。 預設值為 true（僅 HTTPS）。
|`outbound__webhook__skipServerCertValidation`| 標誌以控制是否驗證訂閱者的證書。 預設值為 true。
|`outbound__webhook__allowUnknownCA`| 用於控制訂閱者是否可以顯示自簽章憑證的策略。 預設值為 true。 

## <a name="delivery-and-retry"></a>傳遞和重試

要瞭解此功能，請參閱[交付和重試](delivery-retry.md)。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 傳遞事件的最大嘗試次數。 預設值為 30。
| `broker__defaultEventTimeToLiveInSeconds` | 存留時間 （TTL） 以秒為單位，之後如果未傳遞事件，則丟棄事件。 預設值為**7200**秒

## <a name="output-batching"></a>輸出批次處理

要瞭解此功能，請參閱[交付和輸出批次處理](delivery-output-batching.md)。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | `ApproxBatchSizeInBytes`旋鈕允許的最大值。 預設值為 `1_058_576`。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch`旋鈕允許的最大值。 預設值為 `50`。
| `broker__defaultMaxBatchSizeInBytes` | 僅`MaxEventsPerBatch`指定時的最大傳遞請求大小。 預設值為 `1_058_576`。
| `broker__defaultMaxEventsPerBatch` | 僅`MaxBatchSizeInBytes`指定時要添加到批次處理的最大事件數。 預設值為 `10`。

## <a name="metrics"></a>計量

要瞭解如何在 IoT 邊緣上使用事件網格指標，請參閱[監視主題和訂閱](monitor-topics-subscriptions.md)

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
| `metrics__reporterType` | 報告者類型為指標 enpoint。 預設值為`none`並禁用指標。 設置為`prometheus`啟用普羅米古斯博覽會格式的指標。