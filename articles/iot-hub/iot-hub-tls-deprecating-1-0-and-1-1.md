---
title: IoT 中樞和裝置布建服務（DPS）中的淘汰 TLS 1.0 和 1.1 |Microsoft Docs
description: 在 IoT 中樞和 DPS 中，有關淘汰 TLS 1.0 和1.1 以及支援的密碼的指導方針。
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 95733f579740f2928cda917eec0023bf00d53076
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722781"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>IoT 中樞和裝置布建服務中的 TLS 1.0 和1.1 淘汰

為了提供最優質的加密，IoT 中樞和裝置布建服務（DPS）會移至傳輸層安全性（TLS）1.2，做為 IoT 裝置和服務所選的加密機制。 因此，TLS 1.0 和 TLS 1.1 的舊版支援，以及數個非建議的舊版加密，將于**2020 年7月1日**淘汰。


## <a name="impact"></a>影響
根據客戶的特定情況和設定，淘汰 TLS 1.0 和1.1，而非建議的舊版加密，可能是您的 IoT 裝置和服務（與 IoT 中樞或 DPS 通訊）的影響力變更。 在某些情況下，與這些變更不相容的裝置和服務將無法在前述截止日期之後連線到 IoT 中樞或 DPS。


## <a name="supported-ciphers"></a>支援的密碼

在 TLS 信號交換期間，只允許下列加密：

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>客戶回函

儘管實施 TLS 1.2 是業界最佳的加密選項，也會依計畫推行，我們仍希望了解客戶在採用 TLS 1.2 時的具體部署方式和困難之處。 基於此目的，您可以將您的意見傳送至[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)。