---
title: 設定 webhook 訂閱者驗證-Azure Event Grid IoT Edge |Microsoft Docs
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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841725"
---
# <a name="configure-webhook-subscriber-authentication"></a>設定 Webhook 訂閱者驗證

本指南提供事件方格模組可能 webhook 訂閱者設定的範例。 根據預設，webhook 訂閱者只接受 HTTPS 端點。 如果訂閱者出示自我簽署憑證，事件方格模組將會拒絕。

## <a name="allow-only-https-subscriber"></a>僅允許 HTTPS 訂閱者

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>允許具有自我簽署憑證的 HTTPS 訂閱者

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
>將屬性 `outbound__webhook__allowUnknownCA` 設定為僅在測試環境中 `true`，因為您通常會使用自我簽署憑證。 對於生產工作負載，我們建議將它們設定為**false**。

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>允許 HTTPS 訂閱者，但略過憑證驗證

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
>將屬性 `outbound__webhook__skipServerCertValidation` 設定為僅在測試環境中 `true`，因為您可能不會呈現需要驗證的憑證。 對於生產工作負載，我們建議將它們設定為**false**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>同時允許 HTTP 和 HTTPS 與自我簽署憑證

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
>將屬性 `outbound__webhook__httpsOnly` 設定為僅在測試環境中 `false`，因為您可能想要先顯示 HTTP 訂閱者。 對於生產工作負載，我們建議將它們設定為**true**
