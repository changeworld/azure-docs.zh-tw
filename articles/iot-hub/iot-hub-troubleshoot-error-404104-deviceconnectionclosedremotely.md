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
ms.openlocfilehash: 673a76417739fa59a91979cca7c6807a584868f0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538250"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

本文說明 **404104 DeviceConnectionClosedRemotely** 錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

### <a name="symptom-1"></a>徵兆1

裝置會定期中斷連線 (每65分鐘，例如) ，您會在 IoT 中樞資源記錄中看到 **404104 DeviceConnectionClosedRemotely** 。 有時，您也會看到 **401003 IoTHubUnauthorized** ，而成功的裝置線上活動則會在一分鐘後不到一分鐘。

### <a name="symptom-2"></a>徵兆2

裝置會隨機中斷連線，而您會在 IoT 中樞資源記錄中看到 **404104 DeviceConnectionClosedRemotely** 。

### <a name="symptom-3"></a>徵兆3

許多裝置一次中斷連線，您會在連線的裝置中看到 dip [ (connectedDeviceCount)](monitor-iot-hub-reference.md)計量，而且 Azure 監視器記錄檔中的 DeviceConnectionClosedRemotely 和 [500xxx 內部錯誤](iot-hub-troubleshoot-error-500xxx-internal-errors.md)比平常更 **404104** 多。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 1

[用來連線到 Iot 中樞的 SAS 權杖](iot-hub-devguide-security.md#security-tokens)已過期，這會導致 IoT 中樞中斷裝置連線。 裝置重新整理權杖時，會重新建立連接。 例如， [每小時的 SAS 權杖預設會針對 C SDK 到期](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)，這可能會導致一般中斷連線。

若要深入瞭解，請參閱 [401003 IoTHubUnauthorized 原因](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)。

### <a name="cause-2"></a>原因 2

有一些可能的原因包括：

- 裝置中斷基礎網路連接時間超過 [MQTT keep-alive](iot-hub-mqtt-support.md#default-keep-alive-timeout)，因而導致遠端閒置的超時時間。 每個裝置的 MQTT keep-alive 設定可能不同。

- 裝置傳送了 TCP/IP 層級重設，但未傳送應用層級 `MQTT DISCONNECT` 。 基本上，裝置突然關閉基礎通訊端連線。 有時候，此問題是因為較舊版本的 Azure IoT SDK 錯誤所造成。

- 裝置端應用程式損毀。

### <a name="cause-3"></a>原因 3

IoT 中樞可能遇到暫時性問題。 請參閱 [IoT 中樞內部伺服器錯誤原因](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause)。

## <a name="solutions"></a>方案

### <a name="solution-1"></a>解決方案 1

請參閱 [401003 IoTHubUnauthorized 方案 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>解決方案 2

- 藉由 [測試](tutorial-connectivity.md)連線，確定裝置可連線至 IoT 中樞。 如果網路不可靠或間歇性，我們不建議您增加 keep-alive 值，因為它可能會導致偵測 (透過 Azure 監視器警示，例如) 花更久的時間。 

- 使用最新版本的 [IoT sdk](iot-hub-devguide-sdks.md)。

### <a name="solution-3"></a>解決方案 3

請參閱 [IoT 中樞內部伺服器錯誤的解決方案](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)。

## <a name="next-steps"></a>下一步

我們建議使用 Azure IoT 裝置 Sdk 來可靠地管理連接。 若要深入瞭解，請參閱 [使用 Azure IoT 中樞裝置 sdk 來管理連線能力和可靠的訊息](iot-hub-reliability-features-in-sdks.md)