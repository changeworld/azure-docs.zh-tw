---
title: IoT 中樞和裝置布建服務（DPS）中的淘汰 TLS 1.0 和 1.1 |Microsoft Docs
description: 在 IoT 中樞和 DPS 中，有關淘汰 TLS 1.0 和1.1 以及支援的密碼的指導方針。
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402800"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>IoT 中樞和裝置布建服務中的 TLS 1.0 和1.1 淘汰

為了提供最優質的加密，IoT 中樞和裝置布建服務（DPS）會移至傳輸層安全性（TLS）1.2，做為 IoT 裝置和服務所選的加密機制。 

## <a name="supported-ciphers"></a>支援的密碼

用於 TLS 交握之各種加密的可用性時程表如下所示：

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 （目前支援）
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 （將于2020年下半年支援）
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 （將于2020年下半年支援）
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 （將于2020年下半年支援）


## <a name="customer-feedback"></a>客戶回函

雖然 TLS 1.2 強制是業界頂尖的最佳加密選擇，並會依計畫啟用，但我們仍希望收到客戶有關其特定部署和採用 TLS 1.2 的問題。 基於此目的，您可以將您的意見傳送至[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)。
