---
title: 在 IoT 中心和設備佈建服務 （DPS） 中棄用 TLS 1.0 和 1.1 |微軟文檔
description: 有關 TLS 1.0 和 1.1 的棄用以及 IoT 中心和 DPS 中支援的密碼的準則。
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402800"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>在 IoT 中心和設備佈建服務中棄用 TLS 1.0 和 1.1

為了提供一流的加密，IoT 中心和設備佈建服務 （DPS） 正在遷移到傳輸層安全 （TLS） 1.2，作為 IoT 設備和服務的首選加密機制。 

## <a name="supported-ciphers"></a>支援的密碼

TLS 握手中使用的各種密碼的可用性時程表如下：

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256（當前支援）
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384（將在2020年下半年得到支援）
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256（2020年下半年將予以支援）
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384（2020年下半年將予以支援）


## <a name="customer-feedback"></a>客戶回函

雖然 TLS 1.2 實施是全行業範圍內的同類最佳加密選擇，並將按計劃啟用，但我們仍希望聽取客戶關於其特定部署和採用 TLS 1.2 的困難的意見。 為此，您可以將注釋發送到[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)。
