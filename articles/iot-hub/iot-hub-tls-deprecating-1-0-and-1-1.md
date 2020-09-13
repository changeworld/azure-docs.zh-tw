---
title: 淘汰 IoT 中樞的 TLS 1.0 和 1.1 |Microsoft Docs
description: 有關在 IoT 中樞中淘汰 TLS 1.0 和 1.1 與支援加密的指導方針。
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: fcf7620f53c9bfdb51eb62598f2c8b441574eca6
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006075"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>淘汰 IoT 中樞中的 TLS 1.0 和1.1

為了提供最佳的加密能力，IoT 中樞即將轉換為傳輸層安全性 (TLS) 1.2，以做為可供 IoT 裝置和服務選用的加密機制。 

## <a name="timeline"></a>時間軸

IoT 中樞將繼續支援 TLS 1.0/1.1，直到進一步通知為止。 不過建議所有客戶儘快遷移至 TLS 1.2。

## <a name="deprecating-tls-11-ciphers"></a>淘汰 TLS 1.1 加密

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>淘汰 TLS 1.0 加密

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-cipher-suites"></a>TLS 1.2 加密套件

請參閱 [IoT 中樞 TLS 1.2 加密套件](iot-hub-tls-support.md#cipher-suites)。
 
## <a name="customer-feedback"></a>客戶回函

雖然強制執行 TLS 1.2 就整體業界而言是最佳的加密選擇，且即將依規劃啟用，我們仍希望客戶提出特定部署的相關意見以及採用 TLS 1.2 遇到的問題。 就此目的，歡迎將意見傳送至 [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)。
