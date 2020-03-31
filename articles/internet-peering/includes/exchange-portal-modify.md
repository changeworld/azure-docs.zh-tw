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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774558"
---
本節介紹如何為 Direct 對等互連執行以下修改操作：

### <a name="add-exchange-peering-connections"></a>添加 Exchange 對等互連連接

1. 按一下頂部的 **" 添加連接**"按鈕並配置新的對等互連連接。
    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-exchange-modify-addconnection.png)
1. 填寫**Exchange 對等連接**表單，然後按一下"**保存**"。 有關配置對等互連連接的説明，請查看上面"創建和預配直接對等互連"部分下的步驟。
    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>刪除 Exchange 對等互連連接

1. 按一下要刪除的對等互連連接，然後按一下 **...** > **刪除連接**按鈕。
    > [!div class="mx-imgBorder"]
    > ![對等連接刪除](../media/setup-exchange-modify-deleteconnection.png)
1. 在突出顯示的框中的 **"確認刪除**"框中輸入資源識別碼，然後按一下"**刪除**"。
    > [!div class="mx-imgBorder"]
    > ![對等連接刪除確認](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>在活動連接上添加 IPv4/IPv6 會話

1. 按一下要修改的對等互連連接，然後按一下 **...** > **編輯連接**按鈕。
    > [!div class="mx-imgBorder"]
    > ![對等連接編輯](../media/setup-exchange-modify-editconnection.png)
1. 添加**IPv4 位址**或**IPv6 位址**資訊，然後按一下"**保存**"。
    > [!div class="mx-imgBorder"]
    > ![對等連接修改](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>在活動連接上刪除 IPv4/IPv6 會話

門戶當前不支援從現有連接中刪除 IPv4/IPv6 會話。 聯繫[微軟對等互連](mailto:peeringexperience@microsoft.com)。