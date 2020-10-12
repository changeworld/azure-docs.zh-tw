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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81680954"
---
本節說明如何針對直接對等互連執行下列修改作業。

### <a name="add-exchange-peering-connections"></a>新增 Exchange 對等互連連接

1. 選取 [ **+ 新增連接** ] 按鈕，並設定新的對等互連連線。
    > [!div class="mx-imgBorder"]
    > ![對等互連資源查看](../media/setup-exchange-modify-addconnection.png)
1. 填寫 Exchange 對 **等互連連接** 表單，然後選取 [ **儲存**]。 如需設定對等互連連線的說明，請參閱「建立和布建直接對等互連」一節中的步驟。
    > [!div class="mx-imgBorder"]
    > ![Exchange 對等互連連接表單](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>移除 Exchange 對等互連連線

1. 選取您要刪除的對等互連連線，然後選取 **...**  > **刪除連接**。
    > [!div class="mx-imgBorder"]
    > ![刪除連接按鈕](../media/setup-exchange-modify-deleteconnection.png)
1. 在 [ **確認刪除** ] 方塊中輸入資源識別碼，然後選取 [ **刪除**]。
    > [!div class="mx-imgBorder"]
    > ![刪除確認](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>在作用中連接上新增 IPv4 或 IPv6 會話

1. 選取您要修改的對等互連連線，然後選取 **...**  > **編輯連接**。
    > [!div class="mx-imgBorder"]
    > ![編輯連接按鈕](../media/setup-exchange-modify-editconnection.png)
1. 新增 **IPv4 位址** 或 **IPv6 位址** 資訊，然後選取 [ **儲存**]。
    > [!div class="mx-imgBorder"]
    > ![對等互連連接修改](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>移除主動連線上的 IPv4 或 IPv6 會話

入口網站目前不支援從現有連線移除 IPv4 或 IPv6 會話。 如需詳細資訊，請洽詢 [Microsoft 對等互連](mailto:peeringexperience@microsoft.com)。