---
title: 配置撥入電話的用戶端身份驗證 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 配置 IoT 邊緣上事件網格公開的 API 協定。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841786"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>配置撥入電話的用戶端身份驗證

本指南提供了事件網格模組的可能用戶端身份驗證配置的示例。 事件網格模組支援兩種類型的用戶端身份驗證：

* 基於金鑰的共用訪問簽名 （SAS）
* 基於證書

有關所有可能的配置，請參閱[安全和身份驗證](security-authentication.md)指南。

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>啟用基於證書的用戶端身份驗證，無需自簽章憑證

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

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>啟用基於證書的用戶端身份驗證，允許自簽章憑證

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
>將**inbound__clientAuth__clientCert__allowUnknownCA屬性**設置為僅在測試環境中**為 true，** 因為通常可以使用自簽章憑證。 對於生產工作負載，我們建議您將此屬性設置為**false**和憑證授權單位 （CA） 的證書。

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>啟用基於證書和基於沙鍵的用戶端身份驗證

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
>基於 SAS 金鑰的用戶端身份驗證允許非 IoT 邊緣模組執行管理和運行時操作，前提是 API 埠當然可以在 IoT 邊緣網路之外訪問。
