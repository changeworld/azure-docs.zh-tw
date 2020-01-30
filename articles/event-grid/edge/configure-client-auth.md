---
title: 設定連入呼叫的用戶端驗證-Azure Event Grid IoT Edge |Microsoft Docs
description: 在 IoT Edge 上設定事件方格所公開的 API 通訊協定。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841786"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>設定連入呼叫的用戶端驗證

本指南提供事件方格模組的可能用戶端驗證設定範例。 事件方格模組支援兩種類型的用戶端驗證：

* 共用存取簽章（SAS）金鑰型
* 以憑證為基礎

如需所有可能的設定，請參閱[安全性和驗證](security-authentication.md)指南。

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>啟用以憑證為基礎的用戶端驗證，沒有自我簽署憑證

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>啟用以憑證為基礎的用戶端驗證，允許自我簽署憑證

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>只有在測試環境中，才將屬性**inbound__clientAuth__clientCert__allowUnknownCA**設定為**true** ，因為您通常會使用自我簽署憑證。 對於生產工作負載，建議您將此屬性設定為**false** ，並將憑證從憑證授權單位單位（CA）。

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>啟用以憑證為基礎和以 sas 為基礎的用戶端驗證

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>以 SAS 金鑰為基礎的用戶端驗證可讓非 IoT edge 模組執行管理和執行時間作業，前提是 API 埠可以在 IoT Edge 網路外部存取。
