---
title: 包含檔案
titleSuffix: Azure
description: 包含檔案
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81680954"
---
本節說明如何針對直接對等互連執行下列修改作業。

### <a name="add-exchange-peering-connections"></a>新增 Exchange 對等互連連線

1. 選取 [ **+ 新增連接**] 按鈕，並設定新的對等互連連線。
    > [!div class="mx-imgBorder"]
    > ![對等互連資源檢視](../media/setup-exchange-modify-addconnection.png)
1. 填寫 Exchange 對**等互連連接**表單，然後選取 [**儲存**]。 如需設定對等互連連線的說明，請參閱「建立和布建直接對等互連」一節中的步驟。
    > [!div class="mx-imgBorder"]
    > ![Exchange 對等互連連接表單](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>移除 Exchange 對等互連連線

1. 選取您想要刪除的對等互連連接，然後選取 [ **...**  >  ]**刪除連接**。
    > [!div class="mx-imgBorder"]
    > ![刪除連接按鈕](../media/setup-exchange-modify-deleteconnection.png)
1. 在 [**確認刪除**] 方塊中輸入 [資源識別碼]，然後選取 [**刪除**]。
    > [!div class="mx-imgBorder"]
    > ![刪除確認](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>在使用中連接上新增 IPv4 或 IPv6 會話

1. 選取您想要修改的對等互連連接，然後選取 [ **...**  >  ]**編輯連接**。
    > [!div class="mx-imgBorder"]
    > ![[編輯連接] 按鈕](../media/setup-exchange-modify-editconnection.png)
1. 新增**IPv4 位址**或**IPv6 位址**資訊，然後選取 [**儲存**]。
    > [!div class="mx-imgBorder"]
    > ![對等互連連接的修改](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>移除作用中連線的 IPv4 或 IPv6 會話

目前不支援在入口網站上從現有的連線移除 IPv4 或 IPv6 會話。 如需詳細資訊，請聯絡[Microsoft 對等互連](mailto:peeringexperience@microsoft.com)。