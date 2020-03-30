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
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775338"
---
本節介紹如何為 Direct 對等互連執行以下修改操作：

### <a name="add-direct-peering-connections"></a>添加直接對等互連連接
1. 按一下頂部的 **" 添加連接**"按鈕並配置新的對等互連連接。
    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-direct-modify-addconnection.png)
1. 填寫**直接對等連接**表單，然後按一下"**保存**"。 有關配置對等互連連接的説明，請查看上面"創建和預配直接對等互連"部分下的步驟。
    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>刪除直接對等互連連接

門戶當前不支援刪除連接。 聯繫[微軟對等互連](mailto:peeringexperience@microsoft.com)。

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>在活動連接上升級或降級頻寬
1. 按一下要修改的對等互連連接，然後按一下 **...** > **編輯連接**按鈕。
    > [!div class="mx-imgBorder"]
    > ![對等連接編輯](../media/setup-direct-modify-editconnection.png)
1. 修改頻寬，如下所示，然後按一下"**保存**"。
    > [!div class="mx-imgBorder"]
    > ![對等連接修改頻寬](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>在活動連接上添加 IPv4/IPv6 會話。
1. 按一下要修改的對等互連連接，然後按一下 **...** > **編輯連接**按鈕，如上所示。
1. 添加**會話 IPv4 首碼**或**會話 IPv6 首碼**資訊，然後按一下"**保存**"。

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>在活動連接上刪除 IPv4/IPv6 會話。
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
