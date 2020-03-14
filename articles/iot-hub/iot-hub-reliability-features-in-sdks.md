---
title: 管理 IoT 中樞連線 & 可靠的訊息，w/裝置 Sdk
description: 了解如何在使用 Azure IoT 中樞裝置 SDK 時改進裝置的連線能力和傳訊
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: 1ca7219824a00a5af0bed7d42da75fc06ce2010d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284443"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>使用 Azure IoT 中樞裝置 SDK 來管理連線能力和可靠傳訊

本文將提供概略指引，協助您設計更具復原能力的裝置應用程式。 文中說明如何利用 Azure IoT 裝置 SDK 中的連線能力和可靠的傳訊功能。 本指南的目標是要協助您管理下列案例：

* 修正已中斷的網路連線

* 在不同網路連線之間切換

* 因服務暫時性連線錯誤而重新連線

實作詳細資料可能隨語言而不同。 如需詳細資訊，請參閱 API 文件或特定 SDK：

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [PYTHON SDK](https://github.com/Azure/azure-iot-sdk-python) （可靠性尚未實行）

## <a name="designing-for-resiliency"></a>針對復原而設計

IoT 裝置常須仰賴非連續或不穩定的網路連線 (例如 GSM 或衛星網路)。 當裝置與雲端服務互動時，即可能因服務的間歇性中斷和基礎結構層級的暫時性錯誤，而發生錯誤。 在裝置上執行的應用程式必須管理連線、重新連線的機制，以及傳送和接收訊息的重試邏輯。 此外，重試策略需求主要取決於裝置的 IoT 案例、內容和功能。

Azure IoT 中樞裝置 SDK 的目標是要簡化雲端到裝置以及裝置到雲端的連線和通訊。 這些 SDK 可提供連線至 Azure IoT 中樞的可靠方式，以及傳送和接收訊息的全方位選項集。 開發人員也可以修改現有的實作，以針對特定案例自訂更適合的重試策略。

下列各節涵蓋支援連線能力和可靠傳訊的相關 SDK 功能。

## <a name="connection-and-retry"></a>連線和重試

本節概述管理連接時可用的重新連線和重試模式。 文中將詳細說明在您的裝置應用程式中使用不同重試原則的實作指引，並列出裝置 SDK 中的相關 API。

### <a name="error-patterns"></a>錯誤模式

連線失敗有可能發生在許多層級上：

* 網路錯誤：中斷連線的通訊端和名稱解析錯誤

* HTTP、AMQP 和 MQTT 傳輸的通訊協定層級錯誤：中斷的連結或已過期的工作階段

* 因本機錯誤而造成的應用程式層級錯誤：無效的認證或服務行為 (例如，超出配額或節流)

裝置 SDK 可偵測這三種層級上的錯誤。 裝置 SDK 不會偵測和處理與 OS 相關聯的錯誤和硬體錯誤。 SDK 設計的基礎是來自 Azure 架構中心的[暫時性錯誤處理指引](/azure/architecture/best-practices/transient-faults#general-guidelines)。

### <a name="retry-patterns"></a>重試模式

下列步驟說明偵測到連線錯誤時的重試程序：

1. SDK 偵測到錯誤以及在網路、通訊協定或應用程式中的相關錯誤。

2. SDK 使用錯誤篩選來判斷錯誤類型，並決定是否需要重試。

3. 如果 SDK 識別出**無法復原的錯誤**，連線、傳送和接收之類的作業就會停止。 SDK 會通知使用者。 舉例來說，無法復原的錯誤包括驗證錯誤和不正確的端點錯誤。

4. 如果 SDK 識別出**可復原的錯誤**，則會根據指定的重試原則進行重試，直到定義的逾時結束為止。  請注意，SDK 預設會使用**含隨機跳躍的指數輪詢**重試原則。
5. 當定義的逾時到期後，SDK 就會停止嘗試連線或傳送。 它會通知使用者。

6. SDK 可讓使用者附加回呼，以接收連線狀態變更。

SDK 提供三個重試原則：

* **含隨機跳躍的指數輪詢**：此預設重試原則通常會在一開始積極執行，然後隨著時間減緩，直到達到延遲上限。 設計的基礎是 [Azure Architecture Center 的重試指引](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)。 

* **自訂重試**：針對某些 SDK 語言中，您可以設計您的案例較為適用的自訂重試原則，然後將其插入 RetryPolicy 中。 C SDK 上不提供自訂重試，而且 Python SDK 目前不支援此功能。 Python SDK 會視需要重新連接。

* **不重試**：您可以將重試原則設為「不重試」，而停用重試邏輯。 假設連線已建立，SDK 會嘗試連線一次，然後傳送訊息一次。 此原則通常用於在頻寬或成本方面有所顧慮的案例。 如果選擇此選項，無法傳送的訊息將會遺失，且無法復原。

### <a name="retry-policy-apis"></a>重試原則 API

   | SDK | SetRetryPolicy 方法 | 原則實作 | 實作指引 |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **預設值**：[IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**自訂：** 使用可用的 [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**不重試：** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/iOS 執行](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **預設值**：[ExponentialBackoffWithJitter 類別](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**自訂：** 實作 [RetryPolicy 介面](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**不重試：** [NoRetry 類別](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java 實作](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **預設值**：[ExponentialBackoff 類別](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**自訂**：實作 [IRetryPolicy 介面](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**不重試：** [NoRetry 類別](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C# 實作](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | 節點| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **預設值**：[ExponentialBackoffWithJitter 類別](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**自訂：** 實作 [RetryPolicy 介面](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**不重試：** [NoRetry 類別](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Node 實作](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| 目前不支援 | 目前不支援 | 目前不支援 |

以下是說明此流程的程式碼範例：

#### <a name="net-implementation-guidance"></a>.NET 實作指引

下列程式碼範例說明如何定義和設定預設重試原則：

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

為了避免高 CPU 使用率，如果程式碼立即失敗，重試將會節流。 例如，當沒有目的地的網路或路由時。 執行下一次重試的最短時間為 1 秒。

如果服務回應了節流錯誤，則重試原則不同，且無法透過公用 API 來變更：

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

重試機制會在 `DefaultOperationTimeoutInMilliseconds` 之後停止，目前設為 4 分鐘。

#### <a name="other-languages-implementation-guidance"></a>其他語言的實作指引

針對其他語言的程式碼範例，請檢閱下列實作文件。 存放庫中包含重試原則 API 用法的示範範例。

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python)

## <a name="next-steps"></a>後續步驟

* [使用裝置與服務 SDK](./iot-hub-devguide-sdks.md)

* [使用適用於 C 的 IoT 裝置 SDK](./iot-hub-device-sdk-c-intro.md)

* [為受限裝置而開發](./iot-hub-devguide-develop-for-constrained-devices.md)

* [為行動裝置而開發](./iot-hub-how-to-develop-for-mobile-devices.md)

* [針對裝置連線中斷進行疑難排解](iot-hub-troubleshoot-connectivity.md)
