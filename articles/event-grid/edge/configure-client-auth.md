---
title: 設定連入呼叫的用戶端驗證-Azure 事件方格 IoT Edge |Microsoft Docs
description: 深入瞭解事件方格模組的可能用戶端驗證設定。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 9525da4204e270d033f0c2354318bd71874eaf54
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91290767"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>設定連入呼叫的用戶端驗證

本指南提供事件方格模組的可能用戶端驗證設定範例。 事件方格模組支援兩種類型的用戶端驗證：

* 共用存取簽章 (SAS) 金鑰型
* 以憑證為基礎

如需所有可能的設定，請參閱 [安全性和驗證](security-authentication.md) 指南。

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>啟用以憑證為基礎的用戶端驗證，沒有自我簽署的憑證

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

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>啟用以憑證為基礎的用戶端驗證，允許自我簽署的憑證

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
>請只在測試環境中將屬性 **inbound__clientAuth__clientCert__allowUnknownCA** 設定為 **true** ，因為您通常會使用自我簽署憑證。 針對生產工作負載，建議您將此屬性設定為 **false** ，並將憑證授權單位單位的憑證 (CA) 。

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
>SAS 以金鑰為基礎的用戶端驗證允許非 IoT edge 模組進行管理和執行時間作業，當然，API 埠可在 IoT Edge 網路之外存取。
