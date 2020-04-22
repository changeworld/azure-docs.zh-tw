---
title: 容毀毀 Azure IoT 中心錯誤 409002 連結建立衝突
description: 瞭解如何修復錯誤 409002 連結建立衝突
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 20d39b1f5a11f20eb5d12f34337787b382c820f6
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758749"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

本文介紹了**409002 連結創建衝突**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

您將看到錯誤**409002 Link創造衝突**記錄在診斷日誌中,以及設備斷開或雲到設備消息失敗。 

<!-- When using AMQP? -->

## <a name="cause"></a>原因

通常,當 IoT 中心檢測到用戶端具有多個連接時,會發生此錯誤。 事實上,當新連接請求到達具有現有連接的設備時,IoT 中心會使用此錯誤關閉現有連接。

### <a name="cause-1"></a>原因 1

在最常見的情況下,單獨的問題(如[404104 設備連接關閉](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)遠端)會導致設備斷開連接。 設備嘗試立即重新建立連接,但IoT中心仍考慮設備已連接。 IoT 中樞會關閉先前的連線，並記錄此錯誤。

### <a name="cause-2"></a>原因 2

設備端邏輯錯誤會導致設備在連接已打開時建立連接。

## <a name="solution"></a>解決方法

此錯誤通常會顯現為不同暫時性問題的副作用，所以請尋找記錄中的其他錯誤，進一步進行疑難排解。 否則，請務必在連線中斷後才發出新的連線要求。
