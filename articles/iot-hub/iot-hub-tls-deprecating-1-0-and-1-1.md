---
title: 在 IoT 中樞中淘汰 TLS 1.0 和 1.1 |Microsoft Docs
description: 有關在 IoT 中樞中淘汰 TLS 1.0 和1.1 以及支援的密碼的指導方針。
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381303"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>IoT 中樞中的 TLS 1.0 和1.1 淘汰

為了提供最佳的加密，IoT 中樞會移至傳輸層安全性（TLS）1.2 做為 IoT 裝置和服務的選擇加密機制。 

## <a name="timeline"></a>時間軸

IoT 中樞將繼續支援 TLS 1.0/1.1，直到進一步通知為止。 不過，我們建議所有客戶儘快遷移至 TLS 1.2。

## <a name="supported-ciphers"></a>支援的密碼

用於 TLS 交握之各種加密的可用性時程表如下所示：

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 （目前支援）
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 （將于2020年下半年支援）
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 （將于2020年下半年支援）
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 （將于2020年下半年支援）

## <a name="customer-feedback"></a>客戶意見反應

雖然 TLS 1.2 強制是業界頂尖的最佳加密選擇，並會依計畫啟用，但我們仍希望收到客戶有關其特定部署和採用 TLS 1.2 的問題。 基於此目的，您可以將您的意見[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)傳送至。
