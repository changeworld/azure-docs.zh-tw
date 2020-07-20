---
title: 疑難排解 Azure IoT 中樞錯誤 409002 LinkCreationConflict
description: 瞭解如何修正錯誤 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 20d39b1f5a11f20eb5d12f34337787b382c820f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758749"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

本文說明**409002 LinkCreationConflict**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

您會看到錯誤**409002 LinkCreationConflict**記錄在診斷記錄中，以及裝置中斷連線或雲端到裝置訊息失敗。 

<!-- When using AMQP? -->

## <a name="cause"></a>原因

一般來說，當 IoT 中樞偵測到用戶端有一個以上的連接時，就會發生此錯誤。 事實上，當具有現有連線的裝置有新的連線要求時，IoT 中樞關閉此錯誤的現有連線。

### <a name="cause-1"></a>原因 1

在最常見的情況下，個別的問題（例如[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)）會導致裝置中斷連線。 裝置會嘗試立即重新建立連線，但 IoT 中樞仍會將裝置視為已連接。 IoT 中樞會關閉先前的連線，並記錄此錯誤。

### <a name="cause-2"></a>原因 2

錯誤的裝置端邏輯會導致裝置在已開啟的狀態時建立連接。

## <a name="solution"></a>解決方案

此錯誤通常會顯現為不同暫時性問題的副作用，所以請尋找記錄中的其他錯誤，進一步進行疑難排解。 否則，請務必在連線中斷後才發出新的連線要求。
