---
title: 設定 webhook 訂閱者驗證-Azure 事件方格 IoT Edge |Microsoft Docs
description: 設定 Webhook 訂閱者驗證
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d78b6e80005e9533ccd5ebfaea853f35ae6004be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171647"
---
# <a name="configure-webhook-subscriber-authentication"></a>設定 Webhook 訂閱者驗證

本指南提供事件方格模組可能的 webhook 訂閱者設定範例。 根據預設，webhook 訂閱者只接受 HTTPS 端點。 如果訂閱者出示自我簽署的憑證，事件方格模組將會拒絕。

## <a name="allow-only-https-subscriber"></a>只允許 HTTPS 訂閱者

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
>`outbound__webhook__allowUnknownCA` `true` 請只在測試環境中將屬性設為，因為您通常會使用自我簽署憑證。 針對生產工作負載，建議將它們設為 **false**。

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>允許 HTTPS 訂閱者但略過憑證驗證

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
>`outbound__webhook__skipServerCertValidation` `true` 請只在測試環境中將屬性設為，因為您可能不會呈現需要驗證的憑證。 針對生產工作負載，建議您將其設定為 **false**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>同時允許 HTTP 和 HTTPS 搭配自我簽署憑證

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
>請 `outbound__webhook__httpsOnly` `false` 在測試環境中只將屬性設為，因為您可能會想要先顯示 HTTP 訂閱者。 針對生產工作負載，建議您將其設定為 **true**
