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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681043"
---
本節介紹如何為 Direct 對等互連執行以下修改操作。

### <a name="add-direct-peering-connections"></a>新增直接對等互連連線
1. 選擇 **「 新增連線**」按鈕,然後配置新的對等互連連接。
    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-direct-modify-addconnection.png)

1. 填寫**直接對等連接**窗體,然後選擇 **"保存**"。 有關配置對等互連連接的説明,請查看"創建和預配直接對等互連"部分中的步驟。
    > [!div class="mx-imgBorder"]
    > ![直接對等連接窗體](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>刪除直接對等連結

Azure 入口網站目前不支援刪除連接。 有關詳細資訊,請與[微軟對等互連](mailto:peeringexperience@microsoft.com)。

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>在活動連線上升級或降級頻寬
1. 選擇要修改的對等互連連線,然後選擇 **...** > **編輯連線**。
    > [!div class="mx-imgBorder"]
    > ![編輯連線](../media/setup-direct-modify-editconnection.png)

1. 通過移動滑塊修改頻寬,然後選擇 **"保存**"。
    > [!div class="mx-imgBorder"]
    > ![變更頻寬](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>新增 IPv4 或 IPv6 工作階段資訊
1. 選擇要修改的對等互連連線,然後選擇 **...** > **編輯連接**,如步驟 1 所示。
1. 輸入**工作階段 IPv4 首**碼或**工作階段 IPv6 首**元資訊,然後選擇 **"儲存**"。

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>移除活動連線上的 IPv4 或 IPv6 工作階段資訊
門戶當前不支援刪除**工作階段 IPv4 首**碼 或**會話 IPv6 首**碼的資訊。 有關詳細資訊,請與[微軟對等互連](mailto:peeringexperience@microsoft.com)。
