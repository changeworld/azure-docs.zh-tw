---
title: 排除 Azure IoT 中心錯誤 404104 設備連接遠端關閉
description: 瞭解如何修復錯誤 404104 設備連接關閉遠端
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 686946b65b4b7540f404a291c87c5ad9c7b7a0ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960798"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

本文介紹了**404104 設備連接閉合遠端**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

### <a name="symptom-1"></a>症狀 1

設備定期斷開連接（例如每 65 分鐘一次），在 IoT 中心診斷日誌中看到**404104 設備連接已關閉**。 有時，您還會在不到一分鐘後看到**401003 IoTHub 未經授權**和成功的設備連接事件。

### <a name="symptom-2"></a>症狀 2

設備隨機斷開連接，在 IoT 中心診斷日誌中看到**404104 設備連接已關閉**。

### <a name="symptom-3"></a>症狀3

許多設備一次斷開連接，您看到[連接的設備指標](iot-hub-metrics.md)下降，並且診斷日誌中有**404104 設備連接已關閉**，[診斷日誌中有 500xxx 內部錯誤](iot-hub-troubleshoot-error-500xxx-internal-errors.md)。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 1

[用於連接到 IoT 中心的 SAS 權杖](iot-hub-devguide-security.md#security-tokens)已過期，從而導致 IoT 中心斷開設備。 當設備刷新權杖時，將重新建立連接。 例如[，預設情況下，C SDK 的 SAS 權杖每小時過期一次](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)，這可能導致定期斷開連接。

要瞭解更多資訊，請參閱[401003 IoTHub 未授權原因](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)。

### <a name="cause-2"></a>原因 2

一些可能性包括：

- 設備失去的基礎網路連接的時間比[MQTT 保持活動](iot-hub-mqtt-support.md#default-keep-alive-timeout)時間長，從而導致遠端空閒超時。 MQTT 保持活動設置可以不同每個設備。

- 設備發送了 TCP/IP 級重置，但沒有發送應用程式級`MQTT DISCONNECT`。 基本上，設備突然關閉了基礎通訊端連接。 有時，此問題是由 Azure IoT SDK 的舊版本中的錯誤引起的。

- 設備端應用程式崩潰。

### <a name="cause-3"></a>原因 3

IoT 中心可能遇到暫時性問題。 請參閱[IoT 中心內部伺服器錯誤原因](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause)。

## <a name="solutions"></a>方案

### <a name="solution-1"></a>解決方案 1

請參閱[401003 IoTHub 授權解決方案 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>解決方案 2

- 通過[測試連接](tutorial-connectivity.md)，確保設備與 IoT 中心具有良好的連接。 如果網路不可靠或間歇性，我們不建議增加保持活動值，因為它可能導致檢測（例如通過 Azure 監視器警報）需要更長的時間。 

- 使用最新版本的[IoT SDK](iot-hub-devguide-sdks.md)。

### <a name="solution-3"></a>解決方案 3

請參閱[IoT 中心內部伺服器錯誤的解決方案](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)。

## <a name="next-steps"></a>後續步驟

我們建議使用 Azure IoT 設備 SDK 可靠地管理連接。 要瞭解更多資訊，請參閱[使用 Azure IoT 中心設備 SDK 管理連接和可靠消息傳遞](iot-hub-reliability-features-in-sdks.md)