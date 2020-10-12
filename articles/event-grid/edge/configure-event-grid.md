---
title: 設定-Azure 事件方格 IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格中的設定。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 632227579fd021a0d2ce1d0b1bb0b8a8288c5f47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171664"
---
# <a name="event-grid-configuration"></a>事件方格設定

事件方格提供許多可針對每個環境修改的設定。 下一節是所有可用選項及其預設值的參考。

## <a name="tls-configuration"></a>TLS 設定

若要深入瞭解用戶端驗證的一般資訊，請參閱 [安全性和驗證](security-authentication.md)。 您可以在 [本文](configure-api-protocol.md)中找到其使用方式的範例。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| 事件方格模組的 TLS 原則。 預設值為僅限 HTTPS。
|`inbound__serverAuth__serverCert__source`| 事件方格模組用於其 TLS 設定的伺服器憑證來源。 預設值為 IoT Edge。

## <a name="incoming-client-authentication"></a>傳入用戶端驗證

若要深入瞭解用戶端驗證的一般資訊，請參閱 [安全性和驗證](security-authentication.md)。 您可以在 [本文](configure-client-auth.md)中找到範例。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| 開啟/關閉以憑證為基礎的用戶端驗證。 預設值為 true。
|`inbound__clientAuth__clientCert__source`| 驗證用戶端憑證的來源。 預設值為 IoT Edge。
|`inbound__clientAuth__clientCert__allowUnknownCA`| 允許自我簽署用戶端憑證的原則。 預設值為 true。
|`inbound__clientAuth__sasKeys__enabled`| 開啟/關閉以 SAS 金鑰為基礎的用戶端驗證。 預設值為 off。
|`inbound__clientAuth__sasKeys__key1`| 用來驗證傳入要求的其中一個值。
|`inbound__clientAuth__sasKeys__key2`| 用來驗證傳入要求的選擇性第二個值。

## <a name="outgoing-client-authentication"></a>外寄用戶端驗證
若要深入瞭解用戶端驗證的一般資訊，請參閱 [安全性和驗證](security-authentication.md)。 您可以在 [本文](configure-identity-auth.md)中找到範例。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| 針對外寄要求開啟/關閉附加身分識別憑證。 預設值為 true。
|`outbound__clientAuth__clientCert__source`| 用於抓取事件方格模組外寄憑證的來源。 預設值為 IoT Edge。

## <a name="webhook-event-handlers"></a>Webhook 事件處理常式

若要深入瞭解用戶端驗證的一般資訊，請參閱 [安全性和驗證](security-authentication.md)。 您可以在 [本文](configure-webhook-subscriber-auth.md)中找到範例。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| 控制是否只允許 HTTPS 訂閱者的原則。 預設值為 true (只有 HTTPS) 。
|`outbound__webhook__skipServerCertValidation`| 用來控制是否要驗證訂閱者憑證的旗標。 預設值為 true。
|`outbound__webhook__allowUnknownCA`| 用來控制訂閱者是否可以顯示自我簽署憑證的原則。 預設值為 true。 

## <a name="delivery-and-retry"></a>傳遞和重試

若要瞭解這項功能的一般資訊，請參閱 [傳遞和重試](delivery-retry.md)。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 嘗試傳遞事件的次數上限。 預設值為 30。
| `broker__defaultEventTimeToLiveInSeconds` | 存留時間 (TTL) （以秒為單位），在這段時間之後，如果未傳遞，就會捨棄事件。 預設值為  **7200** 秒

## <a name="output-batching"></a>輸出批次處理

若要深入瞭解這項功能的一般資訊，請參閱 [傳遞和輸出批次處理](delivery-output-batching.md)。

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | 旋鈕允許的最大值 `ApproxBatchSizeInBytes` 。 預設值為 `1_058_576`。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | 旋鈕允許的最大值 `MaxEventsPerBatch` 。 預設值為 `50`。
| `broker__defaultMaxBatchSizeInBytes` | 只有指定時的傳遞要求大小上限 `MaxEventsPerBatch` 。 預設值為 `1_058_576`。
| `broker__defaultMaxEventsPerBatch` | 只有在指定時要加入至批次的最大事件數目 `MaxBatchSizeInBytes` 。 預設值為 `10`。

## <a name="metrics"></a>計量

若要瞭解如何在 IoT Edge 上搭配事件方格使用計量，請參閱 [監視主題和訂閱](monitor-topics-subscriptions.md)

| 屬性名稱 | 描述 |
| ---------------- | ------------ |
| `metrics__reporterType` | 計量端點的報告器類型。 預設值為 `none` ，並停用計量。 將設定為，以 `prometheus` 啟用 Prometheus 展示格式的計量。