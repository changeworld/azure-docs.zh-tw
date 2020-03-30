---
title: 安全性和身份驗證 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: IoT 邊緣事件網格中的安全性和身份驗證。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844508"
---
# <a name="security-and-authentication"></a>安全性和驗證

安全性和身份驗證是一個高級概念，它首先需要熟悉事件網格基礎知識。 如果您對 IoT 邊緣上的事件網格是新資料庫，則[從這裡](concepts.md)開始。 事件網格模組基於 IoT Edge 上的現有安全基礎結構構建。 有關詳細資訊和設置，請參閱[本文檔](../../iot-edge/security.md)。

以下各節詳細介紹了如何對這些設置進行保護和管理：

* TLS 配置
* 入站用戶端身份驗證
* 出站伺服器身份驗證
* 出站用戶端身份驗證

>[!IMPORTANT]
>事件網格模組安全性和身份驗證利用了 IoT Edge 上可用的現有基礎結構。 假設 IoT Edge 子系統是安全的。

>[!IMPORTANT]
>預設情況下，事件網格配置**是安全的**。 以下小節將解釋可用於重寫身份驗證方面的所有選項和可能值。 在進行任何更改之前，瞭解影響。 要使任何更改生效，需要重新部署事件網格模組。

## <a name="tls-configuration-aka-server-authentication"></a>TLS 配置（即伺服器身份驗證）

事件網格模組同時承載 HTTP 和 HTTPS 終結點。 每個 IoT Edge 模組都由 IoT Edge 的安全守護進程分配伺服器憑證。 我們使用伺服器憑證來保護終結點。 到期後，模組將自動刷新 IoT Edge 安全守護進程的新證書。

預設情況下，只允許 HTTPS 通信。 您可以通過**inbound__serverAuth__tlsPolicy**配置覆蓋此行為。 下表捕獲此屬性的可能值。

| 可能的值 | 描述 |
| ---------------- | ------------ |
| Strict | 預設值。 僅啟用 HTTPS
| 啟用 | 同時啟用 HTTP 和 HTTPS
| 已停用 | 僅啟用 HTTP

## <a name="inbound-client-authentication"></a>入站用戶端身份驗證

用戶端是執行管理和/或運行時操作的實體。 用戶端可以是其他 IoT 邊緣模組，非 IoT 應用程式。

事件網格模組支援兩種類型的用戶端身份驗證：

* 基於金鑰的共用訪問簽名 （SAS）
* 基於證書

預設情況下，事件網格模組配置為僅接受基於證書的身份驗證。 啟動時，事件網格模組從 IoT 邊緣安全守護程式檢索"信任捆綁包"，並使用它驗證任何用戶端憑證。 不解析為此鏈的用戶端憑證將被拒絕。 `UnAuthorized`

### <a name="certificate-based-client-authentication"></a>基於證書的用戶端身份驗證

預設情況下，基於證書的身份驗證處於打開狀態。 您可以選擇通過屬性**inbound__clientAuth__clientCert__enabled**禁用基於證書的身份驗證。 下表捕獲可能的值。

| 可能的值 | 描述 |
| ----------------  | ------------ |
| true | 預設值。 要求事件網格模組中的所有請求都提供用戶端憑證。 此外，您需要配置**inbound__clientAuth__clientCert__source**。
| false | 不要強制用戶端提供證書。

下表捕獲**inbound__clientAuth__clientCert__source**的可能值

| 可能的值 | 描述 |
| ---------------- | ------------ |
| IoT Edge | 預設值。 使用 IoT 邊緣的信任捆綁包驗證所有用戶端憑證。

預設情況下，如果用戶端顯示自簽名，事件網格模組將拒絕此類請求。 您可以選擇通過**inbound__clientAuth__clientCert__allowUnknownCA**屬性允許自簽名用戶端憑證。 下表捕獲可能的值。

| 可能的值 | 描述 |
| ----------------  | ------------|
| true | 預設值。 允許成功顯示自簽章憑證。
| false | 如果顯示自簽章憑證，則請求失敗。

>[!IMPORTANT]
>在生產方案中，您可能希望**將inbound__clientAuth__clientCert__allowUnknownCA**設置為**false**。

### <a name="sas-key-based-client-authentication"></a>基於 SAS 金鑰的用戶端身份驗證

除了基於證書的身份驗證外，事件網格模組還可以執行基於 SAS 金鑰的身份驗證。 SAS 金鑰類似于事件網格模組中配置的機密，它應該用於驗證所有撥入電話。 用戶端需要在 HTTP 標頭"aeg-sas-key"中指定機密。 如果請求不匹配，`UnAuthorized`將被拒絕。

控制基於 SAS 金鑰的身份驗證的配置**inbound__clientAuth__sasKeys__enabled**。

| 可能的值 | 描述  |
| ----------------  | ------------ |
| true | 允許基於 SAS 金鑰的身份驗證。 需要**inbound__clientAuth__sasKeys__key1**或**inbound__clientAuth__sasKeys__key2**
| false | 預設值。 禁用基於 SAS 金鑰的身份驗證。

 **inbound__clientAuth__sasKeys__key1**和**inbound__clientAuth__sasKeys__key2**是配置事件網格模組以檢查傳入請求的金鑰。 至少需要配置其中一個金鑰。 發出請求的用戶端需要將金鑰作為請求標頭 **"aeg-sas-key"** 的一部分呈現。 如果同時配置了兩個金鑰，則用戶端可以顯示任一個金鑰。

> [!NOTE]
>可以配置兩種身份驗證方法。 在這種情況下，首先檢查 SAS 金鑰，並且僅當失敗時，才執行基於證書的身份驗證。 請求要成功，只需一種身份驗證方法才能成功。

## <a name="outbound-client-authentication"></a>出站用戶端身份驗證

出站上下文中的用戶端引用事件網格模組。 正在執行的操作是向訂閱者傳遞事件。 訂閱模組被視為伺服器。

每個 IoT 邊緣模組都由 IoT Edge 的安全守護進程分配一個標識證書。 我們將標識證書用於撥出電話。 到期後，模組將自動刷新 IoT Edge 安全守護進程的新證書。

控制出站用戶端身份驗證的配置**outbound__clientAuth__clientCert__enabled**。

| 可能的值 | 描述 |
| ----------------  | ------------ |
| true | 預設值。 需要來自事件網格模組的所有傳出請求來顯示證書。 需要配置**outbound__clientAuth__clientCert__source**。
| false | 不需要事件網格模組來顯示其證書。

控制證書源的配置**outbound__clientAuth__clientCert__source。**

| 可能的值 | 描述 |
| ---------------- | ------------ |
| IoT Edge | 預設值。 使用由 IoT Edge 安全守護進程配置的模組標識證書。

### <a name="outbound-server-authentication"></a>出站伺服器身份驗證

事件網格訂閱者的目標型別之一是"Webhook"。 預設情況下，此類訂閱者僅接受 HTTPS 終結點。

用於控制 webhook 目標策略**的配置outbound__webhook__HTTPsOnly**。

| 可能的值 | 描述 |
| ----------------  | ------------ |
| true | 預設值。 僅允許具有 HTTPS 終結點的訂閱者。
| false | 允許具有 HTTP 或 HTTPS 終結點的訂閱者。

預設情況下，事件網格模組將驗證訂閱者的伺服器憑證。 您可以通過重寫**outbound__webhook__skipServerCertValidation**跳過驗證。 可能的值包括：

| 可能的值 | 描述 |
| ----------------  | ------------ |
| true | 不要驗證訂閱者的伺服器憑證。
| false | 預設值。 驗證訂閱者的伺服器憑證。

如果訂閱者的證書是自簽名的，則預設情況下事件網格模組將拒絕此類訂閱者。 要允許自簽章憑證，可以重寫**outbound__webhook__allowUnknownCA**。 下表捕獲了可能的值。

| 可能的值 | 描述 |
| ----------------  | ------------ |
| true | 預設值。 允許成功顯示自簽章憑證。
| false | 如果顯示自簽章憑證，則請求失敗。

>[!IMPORTANT]
>在生產方案中，您需要將**outbound__webhook__allowUnknownCA**設置為**false**。

> [!NOTE]
>IoT 邊緣環境生成自簽章憑證。 建議是生成由授權 CA 為生產工作負載頒發的證書，並將入站和出站上的**未知CA**屬性設置為**false。**

## <a name="summary"></a>總結

預設情況下，事件網格模組**是安全的**。 我們建議保留這些預設值，用於生產部署。

以下是配置時使用的指導原則：

* 只允許 HTTPS 請求進入模組。
* 僅允許基於證書的用戶端身份驗證。 僅允許由知名的 C 頒發的證書。 禁止自簽章憑證。
* 禁止基於 SASKey 的用戶端身份驗證。
* 始終在撥出電話上顯示事件網格模組的標識證書。
* 僅允許 HTTPS 訂閱者訪問 Webhook 目標型別。
* 始終驗證 Webhook 目標型別的訂閱者的伺服器憑證。 僅允許由知名的 C 頒發的證書。 禁止自簽章憑證。

預設情況下，事件網格模組部署具有以下配置：

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
