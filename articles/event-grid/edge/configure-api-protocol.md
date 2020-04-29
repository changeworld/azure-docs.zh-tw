---
title: 設定 API 通訊協定-Azure Event Grid IoT Edge |Microsoft Docs
description: 在 IoT Edge 上設定事件方格所公開的 API 通訊協定。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76841805"
---
# <a name="configure-event-grid-api-protocols"></a>設定事件方格 API 通訊協定

本指南提供事件方格模組可能通訊協定設定的範例。 事件方格模組會公開 API 以進行其管理和執行時間作業。 下表將會捕捉通訊協定和埠。

| 通訊協定 | 連接埠 | 描述 |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | 預設會關閉。 只有在測試期間才有用。 不適合用于生產工作負載。
| HTTPS | 4438 | 預設值

如需所有可能的設定，請參閱[安全性和驗證](security-authentication.md)指南。

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
> **即可**區段可讓您將內部埠對應至容器主機的埠。 如果 IoT Edge 裝置可以公開連線，這項功能可讓您從 IoT Edge 容器網路外部連線到事件方格模組。

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>向相同邊緣網路上的 IoT 模組公開 HTTP 和 HTTPS

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
> 根據預設，每個 IoT 模組都是橋接器網路所建立 IoT Edge 執行時間的一部分。 它可讓相同網路上的不同 IoT 模組彼此通訊。 **即可**可讓您將容器內部埠對應至主機電腦，讓任何人都能從外部存取事件方格模組的埠。

>[!IMPORTANT]
> 雖然可以讓埠在 IoT Edge 網路外存取，但用戶端驗證會強制執行實際允許呼叫模組的人員。
