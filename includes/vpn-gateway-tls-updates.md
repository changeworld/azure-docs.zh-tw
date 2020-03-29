---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67173803"
---
>[!NOTE]
>從 2018 年 7 月 1 日起，對 TLS 1.0 和 1.1 的支援將會從 Azure VPN 閘道移除。 VPN 閘道僅支援 TLS 1.2。 若要保有支援，請參閱[用以啟用 TLS 1.2 支援的更新](#tls1)。

此外，下列舊版演算法也會在 2018 年 7 月 1 日針對 TLS 取代：

* RC4 (Rivest Cipher 4)
* DES (資料加密演算法)
* 3DES (三重資料加密演算法)
* MD5 (訊息摘要 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-7-and-windows-81"></a><a name="tls1"></a>如何在 Windows 7 和 Windows 8.1 中啟用 TLS 1.2 支援？

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
