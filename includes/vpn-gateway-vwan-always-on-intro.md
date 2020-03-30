---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117073"
---
Windows 10 VPN 用戶端的一項新功能"始終打開"是維護 VPN 連接的能力。 使用"始終打開"時，活動 VPN 設定檔可以根據觸發器（如使用者登錄、網路狀態更改或設備螢幕處於活動狀態）自動連接並保持連接。

您可以將 Windows 10 始終打開的閘道建立到 Azure 的持久使用者隧道和設備隧道。 本文可説明您配置始終位於 VPN 的使用者隧道。

始終在 VPN 連接包括兩種類型的隧道之一：

* **設備隧道**：在使用者登錄到設備之前連接到指定的 VPN 伺服器。 預登錄連接方案和裝置管理使用設備隧道。

* **使用者隧道**：僅在使用者登錄到設備後連接。 通過使用使用者隧道，您可以通過 VPN 伺服器訪問組織資源。

設備隧道和使用者隧道獨立于其 VPN 設定檔運行。 它們可以同時連接，並且可以根據需要使用不同的身份驗證方法和其他 VPN 配置設置。
