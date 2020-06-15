---
title: 淘汰 IoT 中樞的 TLS 1.0 和 1.1 |Microsoft Docs
description: 有關在 IoT 中樞中淘汰 TLS 1.0 和 1.1 與支援加密的指導方針。
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 5c717a02c2008436617d16f08625a1cecc204340
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849513"
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

## <a name="tls-12-ciphers"></a>TLS 1.2 加密

請參閱 [IoT 中樞 TLS 1.2 加密建議](iot-hub-tls-support.md#recommended-ciphers)。
 
## <a name="customer-feedback"></a>客戶回函

雖然強制執行 TLS 1.2 就整體業界而言是最佳的加密選擇，且即將依規劃啟用，我們仍希望客戶提出特定部署的相關意見以及採用 TLS 1.2 遇到的問題。 就此目的，歡迎將意見傳送至 [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)。
