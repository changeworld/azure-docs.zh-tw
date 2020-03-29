---
title: 配置 Webhook 訂閱者身份驗證 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 設定 Webhook 訂閱者驗證
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841725"
---
# <a name="configure-webhook-subscriber-authentication"></a>設定 Webhook 訂閱者驗證

本指南提供了事件網格模組的可能 Webhook 訂閱者配置的示例。 預設情況下，Webhook 訂閱者僅接受 HTTPS 終結點。 如果訂閱者提供自簽章憑證，事件網格模組將拒絕。

## <a name="allow-only-https-subscriber"></a>僅允許 HTTPS 訂戶

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>允許具有自簽章憑證的 HTTPS 訂閱者

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>將屬性`outbound__webhook__allowUnknownCA`設置為`true`僅在測試環境中，因為通常可以使用自簽章憑證。 對於生產工作負載，我們建議將它們設置為**false**。

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>允許 HTTPS 訂閱者，但跳過證書驗證

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>將屬性`outbound__webhook__skipServerCertValidation`設置為`true`僅在測試環境中，因為您可能沒有提供需要身份驗證的證書。 對於生產工作負載，我們建議將它們設置為**false**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>允許使用自簽章憑證的 HTTP 和 HTTPS

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>將屬性`outbound__webhook__httpsOnly`設置為`false`僅在測試環境中，因為您可能想要首先啟動 HTTP 訂閱伺服器。 對於生產工作負載，我們建議將它們設置為**true**
