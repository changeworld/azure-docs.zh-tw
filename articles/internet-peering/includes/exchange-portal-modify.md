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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680954"
---
本節介紹如何為 Direct 對等互連執行以下修改操作。

### <a name="add-exchange-peering-connections"></a>新增 Exchange 對等互連連線

1. 選擇 **「 新增連線**」按鈕,然後配置新的對等互連連接。
    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-exchange-modify-addconnection.png)
1. 填寫**Exchange 對等連接**窗體,然後選擇 **"儲存**"。 有關配置對等互連連接的説明,請查看"創建和預配直接對等互連"部分中的步驟。
    > [!div class="mx-imgBorder"]
    > ![交換對等連接窗型](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>移除 Exchange 對等互連連線

1. 選擇要刪除的對等互連連線,然後選擇 **...** > **刪除連線**。
    > [!div class="mx-imgBorder"]
    > ![移除連線按鈕](../media/setup-exchange-modify-deleteconnection.png)
1. 在 **「確認刪除**」框中輸入資源 ID,然後選擇 **「刪除**」 。
    > [!div class="mx-imgBorder"]
    > ![刪除確認](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>新增 IPv4 或 IPv6 工作階段

1. 選擇要修改的對等互連連線,然後選擇 **...** > **編輯連線**。
    > [!div class="mx-imgBorder"]
    > ![編輯連結按鈕](../media/setup-exchange-modify-editconnection.png)
1. 新增**IPv4 位址**或**IPv6 位址**資訊,然後選擇 **"儲存**"。
    > [!div class="mx-imgBorder"]
    > ![對等互連連線修改](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>在作用中移除 IPv4 或 IPv6 工作階段

門戶當前不支援從現有連接中刪除IPv4或IPv6會話。 有關詳細資訊,請與[微軟對等互連](mailto:peeringexperience@microsoft.com)。