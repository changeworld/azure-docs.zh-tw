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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80117073"
---
Windows 10 VPN 用戶端 Always On 的新功能，就是維護 VPN 連線的能力。 使用 Always On 時，作用中的 VPN 設定檔可以根據觸發程式自動連線並保持連接狀態，例如使用者登入、網路狀態變更或裝置螢幕作用中。

您可以使用具有 Windows 10 Always On 的閘道，為 Azure 建立持續的使用者通道和裝置通道。 本文可協助您設定 Always On VPN 使用者通道。

Always On VPN 連線包含兩種通道類型之一：

* **裝置**通道：在使用者登入裝置之前，會連線到指定的 VPN 伺服器。 登入前的連線案例和裝置管理使用裝置通道。

* **使用者**通道：只有在使用者登入裝置之後，才會連線。 藉由使用使用者通道，您可以透過 VPN 伺服器存取組織資源。

裝置通道和使用者通道的運作與它們的 VPN 設定檔無關。 它們可以同時連接，而且可以適當地使用不同的驗證方法和其他 VPN 設定。
