---
title: 在 IoT 中心中棄用 TLS 1.0 和 1.1 |微軟文件
description: 有關 TLS 1.0 和 1.1 的棄用以及 IoT 中心中支援的密碼的準則。
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381303"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>在 IoT 中棄用 TLS 1.0 和 1.1

為了提供一流的加密,IoT 中心正在轉向傳輸層安全 (TLS) 1.2,作為 IoT 設備和服務的首選加密機制。 

## <a name="timeline"></a>時間軸

IoT 中心將繼續支援 TLS 1.0/1.1,直到另行通知。 但是,我們建議所有客戶儘快遷移到 TLS 1.2。

## <a name="supported-ciphers"></a>支援的密碼

TLS 握手中使用的各種密碼的可用性時程表如下:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256(目前支援)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384(將在2020年下半年得到支援)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256(2020年下半年將予以支援)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384(2020年下半年將予以支援)

## <a name="customer-feedback"></a>客戶回函

雖然 TLS 1.2 實施是全行業範圍內的同類最佳加密選擇,並將按計劃啟用,但我們仍希望聽取客戶關於其特定部署和採用 TLS 1.2 的困難的意見。 此選項, 您可以將註解[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)到 。
