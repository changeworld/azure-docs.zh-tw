---
title: 安全性與驗證-Azure 事件方格 IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格中的安全性和驗證。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 42f6107ff79d6262cdc0a35cf972cf65d3a9a802
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171375"
---
# <a name="security-and-authentication"></a>安全性和驗證

安全性和驗證是一種先進的概念，而且需要先熟悉事件方格的基本概念。 如果您不熟悉 IoT Edge 上的事件方格，請從 [這裡](concepts.md) 開始。 事件方格模組是以 IoT Edge 上現有的安全性基礎結構為基礎。 如需詳細資料和設定，請參閱 [此檔](../../iot-edge/security.md) 。

下列各節將詳細說明如何保護和驗證這些設定：

* TLS 設定
* 輸入用戶端驗證
* 輸出伺服器驗證
* 輸出用戶端驗證

>[!IMPORTANT]
>事件方格模組安全性和驗證利用 IoT Edge 上可用的現有基礎結構。 假設 IoT Edge 子系統是安全的。

>[!IMPORTANT]
>事件方格設定 **預設是安全的**。 下列小節說明您可以用來覆寫驗證層面的所有選項和可能的值 (s) 。 在進行任何變更之前，先瞭解影響。 若要讓任何變更生效，必須重新部署事件方格模組。

## <a name="tls-configuration-aka-server-authentication"></a> (的 TLS 設定：伺服器驗證) 

事件方格模組會裝載 HTTP 和 HTTPS 端點。 IoT Edge 的安全性背景程式會為每個 IoT Edge 模組指派伺服器憑證。 我們使用伺服器憑證來保護端點。 在到期時，模組會自動以 IoT Edge security daemon 的新憑證重新整理。

預設只允許 HTTPS 通訊。 您可以透過  **inbound__serverAuth__tlsPolicy** 設定覆寫此行為。 下表會捕捉此屬性 (s) 的可能值。

| 可能的值 | 說明 |
| ---------------- | ------------ |
| Strict | 預設值。 僅啟用 HTTPS
| 啟用 | 同時啟用 HTTP 和 HTTPS
| 已停用 | 僅啟用 HTTP

## <a name="inbound-client-authentication"></a>輸入用戶端驗證

用戶端是執行管理及/或執行時間作業的實體。 用戶端可以是其他 IoT Edge 模組、非 IoT 應用程式。

事件方格模組支援兩種類型的用戶端驗證：

* 共用存取簽章 (SAS) 金鑰型
* 以憑證為基礎

根據預設，事件方格模組設定為僅接受以憑證為基礎的驗證。 在啟動時，事件方格模組會從 IoT Edge 的安全性守護程式抓取 "TrustBundle"，並使用它來驗證任何用戶端憑證。 未解析為此鏈的用戶端憑證將會遭到拒絕 `UnAuthorized` 。

### <a name="certificate-based-client-authentication"></a>以憑證為基礎的用戶端驗證

依預設，會開啟以憑證為基礎的驗證。 您可以選擇透過屬性 **inbound__clientAuth__clientCert__enabled**停用憑證型驗證。 下表 (s) 中捕捉到可能的值。

| 可能的值 | 說明 |
| ----------------  | ------------ |
| true | 預設值。 需要事件方格模組中的所有要求，才能呈現用戶端憑證。 此外，您也必須設定 **inbound__clientAuth__clientCert__source**。
| false | 不要強制用戶端轉譯憑證。

下錶針對**inbound__clientAuth__clientCert__source** ， (s) 中捕捉可能的值

| 可能的值 | 說明 |
| ---------------- | ------------ |
| IoT Edge | 預設值。 使用 IoT Edge 的 Trustbundle 來驗證所有用戶端憑證。

如果用戶端提供自我簽署，則事件方格模組預設會拒絕這類要求。 您可以透過 **inbound__clientAuth__clientCert__allowUnknownCA** 屬性，選擇允許自我簽署用戶端憑證。 下表 (s) 中捕捉到可能的值。

| 可能的值 | 說明 |
| ----------------  | ------------|
| true | 預設值。 允許成功呈現自我簽署憑證。
| false | 如果顯示自我簽署憑證，將會導致要求失敗。

>[!IMPORTANT]
>在生產案例中，您可能會想要將 **inbound__clientAuth__clientCert__allowUnknownCA** 設定為 **false**。

### <a name="sas-key-based-client-authentication"></a>以 SAS 金鑰為基礎的用戶端驗證

除了以憑證為基礎的驗證之外，事件方格模組也可以進行 SAS 金鑰型驗證。 SAS 金鑰就像是在事件方格模組中設定的密碼，它應該用來驗證所有的連入呼叫。 用戶端必須在 HTTP 標頭 ' aeg-event-type-金鑰 ' 中指定秘密。 如果要求不相符，將會拒絕要求 `UnAuthorized` 。

**Inbound__clientAuth__sasKeys__enabled**控制 SAS 金鑰型驗證的設定。

| 可能的值 | 說明  |
| ----------------  | ------------ |
| true | 允許 SAS 金鑰型驗證。 需要 **inbound__clientAuth__sasKeys__key1** 或 **inbound__clientAuth__sasKeys__key2**
| false | 預設值。 停用 SAS 金鑰型驗證。

 **inbound__clientAuth__sasKeys__key1** 和 **inbound__clientAuth__sasKeys__key2** 是您設定事件方格模組以針對傳入要求進行檢查的索引鍵。 至少需要設定其中一個金鑰。 提出要求的用戶端必須將金鑰顯示為要求標頭 '**aeg-event-type-sas 金鑰**' 的一部分。 如果兩個金鑰都已設定，用戶端就可以顯示其中一個金鑰。

> [!NOTE]
>您可以設定這兩種驗證方法。 在這種情況下，會先檢查 SAS 金鑰，而且只有在該金鑰失敗時，才會執行以憑證為基礎的驗證。 如果要求成功，則只有其中一個驗證方法必須成功。

## <a name="outbound-client-authentication"></a>輸出用戶端驗證

輸出內容中的用戶端是指事件方格模組。 完成的作業是將事件傳遞給訂閱者。 訂閱模組會被視為伺服器。

系統會透過 IoT Edge 的安全性背景程式，將身分識別憑證指派給每個 IoT Edge 模組。 我們會使用身分識別憑證進行外寄呼叫。 在到期時，模組會自動以 IoT Edge security daemon 的新憑證重新整理。

**Outbound__clientAuth__clientCert__enabled**設定來控制輸出用戶端驗證。

| 可能的值 | 說明 |
| ----------------  | ------------ |
| true | 預設值。 需要來自事件方格模組的所有傳出要求，才能呈現憑證。 需要設定 **outbound__clientAuth__clientCert__source**。
| false | 不需要事件方格模組來呈現其憑證。

會 **outbound__clientAuth__clientCert__source**控制憑證來源的設定。

| 可能的值 | 說明 |
| ---------------- | ------------ |
| IoT Edge | 預設值。 使用由 IoT Edge security daemon 設定的模組身分識別憑證。

### <a name="outbound-server-authentication"></a>輸出伺服器驗證

「事件方格」訂閱者的其中一個目的地類型為「Webhook」。 根據預設，這類訂閱者只會接受 HTTPS 端點。

控制 webhook 目的地原則 **outbound__webhook__HTTPsOnly**的設定。

| 可能的值 | 說明 |
| ----------------  | ------------ |
| true | 預設值。 只允許具有 HTTPS 端點的「訂閱者」。
| false | 允許具有 HTTP 或 HTTPS 端點的訂閱者。

根據預設，事件方格模組會驗證訂閱者的伺服器憑證。 您可以藉由覆寫 **outbound__webhook__skipServerCertValidation**來略過驗證。 可能的值包括：

| 可能的值 | 說明 |
| ----------------  | ------------ |
| true | 不要驗證訂閱者的伺服器憑證。
| false | 預設值。 驗證訂閱者的伺服器憑證。

如果訂閱者的憑證是自我簽署的，則根據預設，事件方格模組會拒絕這類訂閱者。 若要允許自我簽署憑證，您可以覆寫 **outbound__webhook__allowUnknownCA**。 下表將 (s) 的可能值加以捕捉。

| 可能的值 | 說明 |
| ----------------  | ------------ |
| true | 預設值。 允許成功呈現自我簽署憑證。
| false | 如果顯示自我簽署憑證，將會導致要求失敗。

>[!IMPORTANT]
>在生產案例中，您會想要將 **outbound__webhook__allowUnknownCA** 設定為 **false**。

> [!NOTE]
>IoT Edge 環境會產生自我簽署的憑證。 建議您為生產工作負載產生授權 Ca 所發行的憑證，並將輸入和輸出上的 **allowUnknownCA** 屬性設定為 **false**。

## <a name="summary"></a>摘要

事件方格模組預設是 **安全**的。 建議您在生產環境部署中保留這些預設值。

以下是設定時要使用的指導準則：

* 只允許進入模組的 HTTPS 要求。
* 只允許以憑證為基礎的用戶端驗證。 只允許知名 Ca 所發行的憑證。 不允許自我簽署的憑證。
* 不允許以 SASKey 為基礎的用戶端驗證。
* 一律在外寄呼叫上呈現事件方格模組的身分識別憑證。
* 僅允許 Webhook 目的地類型的 HTTPS 訂閱者。
* 一律驗證使用者的 Webhook 目的地類型的伺服器憑證。 只允許知名 Ca 所發行的憑證。 不允許自我簽署的憑證。

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
