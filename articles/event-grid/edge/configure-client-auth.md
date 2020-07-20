---
title: 設定連入呼叫的用戶端驗證-Azure Event Grid IoT Edge |Microsoft Docs
description: 在 IoT Edge 上設定事件方格所公開的 API 通訊協定。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: a0bba9559c2a0b4ff6c8a4e5f2287692e27f8a1a
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171698"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>設定連入呼叫的用戶端驗證

本指南提供事件方格模組的可能用戶端驗證設定範例。 事件方格模組支援兩種類型的用戶端驗證：

*  (SAS) 以金鑰為基礎的共用存取簽章
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
>只有在測試環境中，才將屬性**inbound__clientAuth__clientCert__allowUnknownCA**設定為**true** ，因為您通常會使用自我簽署憑證。 針對生產工作負載，建議您將此屬性設定為**false** ，並將憑證授權單位單位的憑證 (CA) 。

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
