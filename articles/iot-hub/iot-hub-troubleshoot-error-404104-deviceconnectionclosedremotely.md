---
title: 疑難排解 Azure IoT 中樞錯誤 404104 DeviceConnectionClosedRemotely
description: 瞭解如何修正錯誤 404104 DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: c8cb91aa0c7ce1610320d4107db282d3c34407ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758718"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

本文說明**404104 DeviceConnectionClosedRemotely**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

### <a name="symptom-1"></a>徵兆1

裝置會定期中斷連線（例如每隔65分鐘），而您會在 IoT 中樞診斷記錄中看到**404104 DeviceConnectionClosedRemotely** 。 有時候，您也會看到**401003 IoTHubUnauthorized**和一個成功的裝置線上活動不到一分鐘之後。

### <a name="symptom-2"></a>徵兆2

裝置會隨機中斷連線，而您會在 IoT 中樞診斷記錄中看到**404104 DeviceConnectionClosedRemotely** 。

### <a name="symptom-3"></a>徵兆3

許多裝置一次都會中斷連線，您會在 [[連接的裝置](iot-hub-metrics.md)] 計量中看到 dip，而診斷記錄中會有比平常更多的**404104 DeviceConnectionClosedRemotely**和[500xxx 內部錯誤](iot-hub-troubleshoot-error-500xxx-internal-errors.md)。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 1

[用來連線到 IoT 中樞過期的 SAS 權杖](iot-hub-devguide-security.md#security-tokens)，這會導致 IoT 中樞中斷裝置的連線。 當裝置重新整理權杖時，會重新建立連線。 例如，[針對 C SDK，SAS 權杖預設會每小時到期](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)，這可能會導致一般中斷連線。

若要深入瞭解，請參閱[401003 IoTHubUnauthorized 原因](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)。

### <a name="cause-2"></a>原因 2

一些可能的原因包括：

- 裝置遺失的基礎網路連線時間比[MQTT keep-alive](iot-hub-mqtt-support.md#default-keep-alive-timeout)長，因而導致遠端閒置超時。 每個裝置的 MQTT keep-alive 設定可能不同。

- 裝置已傳送 TCP/IP 層級重設，但未傳送應用層級`MQTT DISCONNECT`。 基本上，裝置突然關閉基礎通訊端連接。 有時候，此問題是由較舊版本的 Azure IoT SDK 中的錯誤所造成。

- 裝置端應用程式當機。

### <a name="cause-3"></a>原因 3

IoT 中樞可能會遇到暫時性的問題。 請參閱[IoT 中樞內部伺服器錯誤原因](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause)。

## <a name="solutions"></a>方案

### <a name="solution-1"></a>解決方案 1

請參閱[401003 IoTHubUnauthorized 解決方案 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>解決方案 2

- 藉由[測試](tutorial-connectivity.md)連線，確定裝置具有 IoT 中樞的良好連線能力。 如果網路不可靠或斷斷續續，我們不建議增加 keep-alive 值，因為它可能會導致偵測（透過 Azure 監視器的警示）花費較長的時間。 

- 使用[IoT sdk](iot-hub-devguide-sdks.md)的最新版本。

### <a name="solution-3"></a>解決方案 3

請參閱[IoT 中樞內部伺服器錯誤的解決方案](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)。

## <a name="next-steps"></a>後續步驟

我們建議使用 Azure IoT 裝置 Sdk 來可靠地管理連接。 若要深入瞭解，請參閱[使用 Azure IoT 中樞裝置 sdk 來管理連線能力和可靠訊息](iot-hub-reliability-features-in-sdks.md)