---
title: 配置 API 協定 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 配置 IoT 邊緣上事件網格公開的 API 協定。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841805"
---
# <a name="configure-event-grid-api-protocols"></a>配置事件網格 API 協定

本指南提供了事件網格模組的可能協定配置的示例。 事件網格模組公開 API 以進行管理和運行時操作。 下表捕獲協定和埠。

| 通訊協定 | 連接埠 | 描述 |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | 預設情況下關閉。 僅在測試期間有用。 不適合生產工作負載。
| HTTPS | 4438 | 預設

有關所有可能的配置，請參閱[安全和身份驗證](security-authentication.md)指南。

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>將 HTTPS 公開給同一邊緣網路上的 IoT 模組

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>將 HTTPS 啟用到其他 IoT 模組和非 IoT 工作負載

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
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

>[!NOTE]
> **PortBindings**部分允許您將內部埠映射到容器主機的埠。 此功能使得如果 IoT 邊緣設備可以公開訪問，則可以從 IoT 邊緣容器網路外部訪問事件網格模組。

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>在同一邊緣網路上的 IoT 模組中公開 HTTP 和 HTTPS

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>將 HTTP 和 HTTPS 啟用到其他 IoT 模組和非 IoT 工作負載

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> 預設情況下，每個 IoT 模組都是橋接器網路創建的 IoT Edge 運行時的一部分。 它使同一網路上的不同 IoT 模組能夠相互通信。 **PortBindings**允許您將容器內部埠映射到主機上，從而允許任何人能夠從外部訪問事件網格模組的埠。

>[!IMPORTANT]
> 雖然可以在 IoT Edge 網路之外訪問這些埠，但用戶端身份驗證強制實際允許誰向模組進行調用。
