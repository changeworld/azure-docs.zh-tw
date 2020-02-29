---
title: IoT 中樞和裝置布建服務（DPS）中的淘汰 TLS 1.0 和 1.1 |Microsoft Docs
description: 在 IoT 中樞和 DPS 中，有關淘汰 TLS 1.0 和1.1 以及支援的密碼的指導方針。
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 8e2f8fd7f99c359949b02959cc442f2f3d3ebfff
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912149"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>IoT 中樞和裝置布建服務中的 TLS 1.0 和1.1 淘汰

為了提供最優質的加密，IoT 中樞和裝置布建服務（DPS）會移至傳輸層安全性（TLS）1.2，做為 IoT 裝置和服務所選的加密機制。 因此，TLS 1.0 和 TLS 1.1 的舊版支援，以及數個非建議的舊版加密，將于**2020 年7月1日**淘汰。


## <a name="impact"></a>影響
根據客戶的特定情況和設定，淘汰 TLS 1.0 和1.1，而非建議的舊版加密，可能是您的 IoT 裝置和服務（與 IoT 中樞或 DPS 通訊）的影響力變更。 在某些情況下，與這些變更不相容的裝置和服務將無法在前述截止日期之後連線到 IoT 中樞或 DPS。


## <a name="supported-ciphers"></a>支援的密碼

用於 TLS 交握之各種加密的可用性時程表如下所示：

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 （目前支援）
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 （將于2020年下半年支援）
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 （將于2020年下半年支援）
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 （將于2020年下半年支援）


## <a name="customer-feedback"></a>客戶回函

雖然 TLS 1.2 強制是業界頂尖的最佳加密選擇，並會依計畫啟用，但我們仍希望收到客戶有關其特定部署和採用 TLS 1.2 的問題。 基於此目的，您可以將您的意見傳送至[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)。
