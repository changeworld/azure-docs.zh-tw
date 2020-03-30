---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75437001"
---
|  | **私人對等互連** | **Microsoft 對等互連** |  **公用對等互連** (已被取代，不適用新線路) |
| --- | --- | --- | --- |
| **每個對等支援的前置詞數目最大值** |預設為 4000，ExpressRoute Premium 中為 10000 |200 |200 |
| **支援的 IP 位址範圍** |您的 WAN 內任何有效的 IP 位址。 |您或您的連線提供者所擁有的公用 IP 位址。 |您或您的連線提供者所擁有的公用 IP 位址。 |
| **AS 編號需求** |私密和公用 AS 編號。 您必須擁有公用 AS 編號，才能選擇使用其中一個編號。 |私密和公用 AS 編號。 不過，您必須證明公用 IP 位址的擁有權。 |私密和公用 AS 編號。 不過，您必須證明公用 IP 位址的擁有權。 |
| **支援的 IP 通訊協定**| IPv4 |  IPv4、IPv6 | IPv4 |
| **路由介面 IP 位址** |RFC1918 和公用 IP 位址 |在路由註冊中向您註冊的公用 IP 位址。 |在路由註冊中向您註冊的公用 IP 位址。 |
| **MD5 雜湊支援** |是 |是 |是 |