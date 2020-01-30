---
title: 安全性和驗證-Azure Event Grid IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格中的安全性和驗證。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844508"
---
# <a name="security-and-authentication"></a>安全性和驗證

安全性和驗證是一種先進的概念，它必須先熟悉事件方格的基本知識。 如果您不熟悉 IoT Edge 上的事件方格，請從[這裡](concepts.md)開始。 事件方格模組是以 IoT Edge 上的現有安全性基礎結構為基礎。 如需詳細資訊和設定，請參閱[此檔](../../iot-edge/security.md)。

下列各節詳細說明如何保護和驗證這些設定：

* TLS 設定
* 輸入用戶端驗證
* 輸出伺服器驗證
* 輸出用戶端驗證

>[!IMPORTANT]
>Event Grid 模組安全性和驗證利用現有的基礎結構，可在 IoT Edge 上取得。 假設 IoT Edge 子系統是安全的。

>[!IMPORTANT]
>根據預設，事件方格設定是**安全的**。 下列小節說明您可以用來覆寫驗證層面的所有選項和可能的值。 在進行任何變更之前，請先瞭解影響。 若要讓變更生效，必須重新部署事件方格模組。

## <a name="tls-configuration-aka-server-authentication"></a>TLS 設定（a. k. 伺服器驗證）

事件方格模組會同時裝載 HTTP 和 HTTPS 端點。 IoT Edge 的安全性守護程式會為每個 IoT Edge 模組指派一個伺服器憑證。 我們會使用伺服器憑證來保護端點。 當到期時，模組會自動以 IoT Edge security daemon 的新憑證重新整理。

根據預設，只允許 HTTPS 通訊。 您可以透過**inbound__serverAuth__tlsPolicy**設定來覆寫此行為。 下表會捕捉此屬性的可能值。

| 可能的值 | 說明 |
| ---------------- | ------------ |
| 嚴格 | 預設值。 僅啟用 HTTPS
| 啟用 | 同時啟用 HTTP 和 HTTPS
| 已停用 | 僅啟用 HTTP

## <a name="inbound-client-authentication"></a>輸入用戶端驗證

用戶端是執行管理和/或執行時間作業的實體。 用戶端可以是其他 IoT Edge 模組、非 IoT 應用程式。

事件方格模組支援兩種類型的用戶端驗證：

* 共用存取簽章（SAS）金鑰型
* 以憑證為基礎

根據預設，事件方格模組已設定為僅接受以憑證為基礎的驗證。 在啟動時，事件方格模組會從 IoT Edge 安全性守護程式抓取 "TrustBundle"，並使用它來驗證任何用戶端憑證。 無法解析為此鏈的用戶端憑證將會拒絕 `UnAuthorized`。

### <a name="certificate-based-client-authentication"></a>以憑證為基礎的用戶端驗證

預設會開啟以憑證為基礎的驗證。 您可以選擇透過**inbound__clientAuth__clientCert__enabled**屬性來停用憑證型驗證。 下表會捕捉可能的值。

| 可能的值 | 說明 |
| ----------------  | ------------ |
| true | 預設值。 需要所有要求進入事件方格模組，才能出示用戶端憑證。 此外，您也必須設定**inbound__clientAuth__clientCert__source**。
| false | 不要強制用戶端轉譯憑證。

下表會針對**inbound__clientAuth__clientCert__source**捕捉可能的值

| 可能的值 | 說明 |
| ---------------- | ------------ |
| IoT Edge | 預設值。 會使用 IoT Edge 的 Trustbundle 來驗證所有用戶端憑證。

如果用戶端出示自我簽署，則事件方格模組預設會拒絕這類要求。 您可以透過**inbound__clientAuth__clientCert__allowUnknownCA**屬性，選擇允許自我簽署的用戶端憑證。 下表會捕捉可能的值。

| 可能的值 | 說明 |
| ----------------  | ------------|
| true | 預設值。 允許成功呈現自我簽署憑證。
| false | 如果提供自我簽署憑證，將會導致要求失敗。

>[!IMPORTANT]
>在生產案例中，您可能會想要將**inbound__clientAuth__clientCert__allowUnknownCA**設定為**false**。

### <a name="sas-key-based-client-authentication"></a>以 SAS 金鑰為基礎的用戶端驗證

除了以憑證為基礎的驗證之外，事件方格模組也可以執行以 SAS 金鑰為基礎的驗證。 SAS 金鑰就像是在事件方格模組中設定的密碼，它應該用來驗證所有傳入的呼叫。 用戶端必須指定 HTTP 標頭 ' aeg-event-type-sas 金鑰 ' 中的密碼。 如果要求不相符，`UnAuthorized` 將會遭到拒絕。

控制以 SAS 金鑰為基礎之驗證的設定**inbound__clientAuth__sasKeys__enabled**。

| 可能的值 | 說明  |
| ----------------  | ------------ |
| true | 允許以 SAS 金鑰為基礎的驗證。 需要**inbound__clientAuth__sasKeys__key1**或**inbound__clientAuth__sasKeys__key2**
| false | 預設值。 已停用以 SAS 金鑰為基礎的驗證。

 **inbound__clientAuth__sasKeys__key1**和**inbound__clientAuth__sasKeys__key2**是您設定事件方格模組來檢查傳入要求的索引鍵。 至少需要設定其中一個金鑰。 提出要求的用戶端必須將金鑰呈現為要求標頭 '**aeg-event-type-sas-金鑰**' 的一部分。 如果兩個金鑰都已設定，則用戶端可以呈現其中一個金鑰。

> [!NOTE]
>您可以設定這兩種驗證方法。 在這種情況下，會先檢查 SAS 金鑰，而且只有在失敗時，才會執行以憑證為基礎的驗證。 要求若要成功，只有其中一個驗證方法需要成功。

## <a name="outbound-client-authentication"></a>輸出用戶端驗證

輸出內容中的用戶端是指事件方格模組。 正在進行的作業是將事件傳遞給訂閱者。 訂閱模組會視為伺服器。

IoT Edge 的安全性守護程式會為每個 IoT Edge 模組指派一個身分識別憑證。 我們會使用身分識別憑證來進行外寄呼叫。 當到期時，模組會自動以 IoT Edge security daemon 的新憑證重新整理。

控制輸出用戶端驗證的設定是**outbound__clientAuth__clientCert__enabled**。

| 可能的值 | 說明 |
| ----------------  | ------------ |
| true | 預設值。 需要來自事件方格模組的所有連出要求，才能出示憑證。 需要設定**outbound__clientAuth__clientCert__source**。
| false | 不需要事件方格模組來呈現其憑證。

控制憑證來源的設定**outbound__clientAuth__clientCert__source**。

| 可能的值 | 說明 |
| ---------------- | ------------ |
| IoT Edge | 預設值。 會使用 IoT Edge 安全性守護程式所設定的模組身分識別憑證。

### <a name="outbound-server-authentication"></a>輸出伺服器驗證

事件方格訂閱者的其中一個目的地類型是 "Webhook"。 根據預設，這類訂閱者只接受 HTTPS 端點。

控制 webhook 目的地原則**outbound__webhook__HTTPsOnly**的設定。

| 可能的值 | 說明 |
| ----------------  | ------------ |
| true | 預設值。 僅允許具有 HTTPS 端點的訂閱者。
| false | 允許使用 HTTP 或 HTTPS 端點的訂閱者。

根據預設，事件方格模組會驗證訂閱者的伺服器憑證。 您可以藉由覆寫**outbound__webhook__skipServerCertValidation**來略過驗證。 可能的值包括：

| 可能的值 | 說明 |
| ----------------  | ------------ |
| true | 不要驗證訂閱者的伺服器憑證。
| false | 預設值。 驗證訂閱者的伺服器憑證。

如果訂閱者的憑證是自我簽署的，則根據預設，事件方格模組會拒絕這類訂閱者。 若要允許自我簽署憑證，您可以覆寫**outbound__webhook__allowUnknownCA**。 下表會捕捉可能的值。

| 可能的值 | 說明 |
| ----------------  | ------------ |
| true | 預設值。 允許成功呈現自我簽署憑證。
| false | 如果提供自我簽署憑證，將會導致要求失敗。

>[!IMPORTANT]
>在生產案例中，您會想要將**outbound__webhook__allowUnknownCA**設定為**false**。

> [!NOTE]
>IoT Edge 環境會產生自我簽署憑證。 建議為生產工作負載產生授權 Ca 所發出的憑證，並將輸入和輸出的**allowUnknownCA**屬性設定為**false**。

## <a name="summary"></a>摘要

事件方格模組預設是**安全的**。 建議您保留生產部署的這些預設值。

以下是設定時要使用的指導原則：

* 僅允許對模組進行 HTTPS 要求。
* 僅允許以憑證為基礎的用戶端驗證。 只允許知名 Ca 所發行的憑證。 不允許自我簽署憑證。
* 不允許以 SASKey 為基礎的用戶端驗證。
* 在撥出電話上一律會顯示事件方格模組的身分識別憑證。
* 僅允許 Webhook 目的地類型的 HTTPS 訂閱者。
* 針對 Webhook 目的地類型，一律驗證訂閱者的伺服器憑證。 只允許知名 Ca 所發行的憑證。 不允許自我簽署憑證。

根據預設，會使用下列設定來部署事件方格模組：

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
