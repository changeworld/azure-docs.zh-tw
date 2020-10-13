---
title: 設定 API 通訊協定-Azure 事件方格 IoT Edge |Microsoft Docs
description: 深入瞭解事件方格模組的可能通訊協定設定。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: a9cf6088201ffeaed76d99a9b211e5bcd1ea139a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322575"
---
# <a name="configure-event-grid-api-protocols"></a>設定事件方格 API 通訊協定

本指南提供事件方格模組可能通訊協定設定的範例。 事件方格模組會公開 API 來進行管理和執行時間作業。 下表將會捕捉通訊協定和埠。

| 通訊協定 | 連接埠 | 描述 |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | 預設為關閉。 只在測試期間很有用。 不適合生產工作負載。
| HTTPS | 4438 | 預設

如需所有可能的設定，請參閱 [安全性和驗證](security-authentication.md) 指南。

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>將 HTTPS 公開至相同邊緣網路上的 IoT 模組

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>啟用其他 IoT 模組和非 IoT 工作負載的 HTTPS

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
> **即可**區段可讓您將內部埠對應至容器主機的埠。 如果 IoT Edge 裝置可公開連線，這項功能可讓您從 IoT Edge 的容器網路外部連接到事件方格模組。

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>將 HTTP 和 HTTPS 公開給相同邊緣網路上的 IoT 模組

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>啟用其他 IoT 模組和非 IoT 工作負載的 HTTP 和 HTTPS

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
> 根據預設，每個 IoT 模組都是橋接器網路所建立的 IoT Edge 執行時間的一部分。 它可在相同網路上啟用不同的 IoT 模組來彼此通訊。 **即可** 可讓您將容器內部埠對應到主機電腦，讓任何人都能夠從外部存取事件方格模組的埠。

>[!IMPORTANT]
> 雖然可以在 IoT Edge 網路之外存取埠，用戶端驗證仍會強制執行實際允許呼叫模組的人員。
