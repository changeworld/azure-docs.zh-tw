---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e40f421c0fa45d772cd333dac51fe2bdf2779f48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91829068"
---
Always On Windows 10 VPN 用戶端的新功能，就是維護 VPN 連線的能力。 使用 Always On，使用中的 VPN 設定檔可以自動連線，並根據觸發程式（例如使用者登入、網路狀態變更或裝置螢幕使用中）保持線上狀態。

您可以使用閘道搭配 Windows 10 Always On，為 Azure 建立持續性的使用者通道和裝置通道。

Always On VPN 連線包含兩種通道類型的其中一種：

* **裝置**通道：在使用者登入裝置之前，連接到指定的 VPN 伺服器。 預先登入連線案例和裝置管理會使用裝置通道。

* **使用者**通道：只有在使用者登入裝置之後才會連線。 藉由使用使用者通道，您可以透過 VPN 伺服器存取組織資源。

裝置通道和使用者通道的運作與它們的 VPN 設定檔無關。 它們可以同時連線，而且可以適當地使用不同的驗證方法和其他 VPN 設定。
