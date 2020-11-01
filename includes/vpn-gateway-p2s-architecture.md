---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/30/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3ad8fcb6cd5cf68b02ac522d0e5ef5a18ba8a3c
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "93144985"
---
點對站原生 Azure 憑證驗證連線使用下列專案，您可以在此練習中設定這些專案：

* RouteBased VPN 閘道。
* 已上傳至 Azure 之根憑證的公開金鑰 (.cer 檔案)。 一旦上傳憑證，憑證就會被視為受信任的憑證並且用於驗證。
* 從根憑證產生的用戶端憑證。 此用戶端憑證須安裝在每部將連線至 VNet 的用戶端電腦上。 此憑證使用於用戶端憑證。
* VPN 用戶端設定。 VPN 用戶端是使用 VPN 用戶端設定檔進行設定。 這些檔案包含用戶端連線到 VNet 所需的資訊。 此檔案會設定作業系統原生的現有 VPN 用戶端。 您必須使用組態檔中的設定來設定每個進行連線的用戶端。
