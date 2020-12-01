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
ms.openlocfilehash: c3212acd5015edbb639b8904b885c718d654e5b4
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356256"
---
本節說明如何針對直接對等互連執行下列修改作業。

### <a name="add-direct-peering-connections"></a>新增直接對等互連連接
1. 選取 [ **+ 新增連接** ] 按鈕，並設定新的對等互連連線。
    > [!div class="mx-imgBorder"]
    > ![[AshburnPeering-連接] 頁面會列出連接。 [+ 新增連接] 按鈕會反白顯示。](../media/setup-direct-modify-addconnection.png)

1. 填寫直接對 **等互連連接** 表單，然後選取 [ **儲存**]。 如需設定對等互連連線的說明，請參閱「建立和布建直接對等互連」一節中的步驟。
    > [!div class="mx-imgBorder"]
    > ![直接對等互連連接表單](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>移除直接對等互連連接

Azure 入口網站目前不支援移除連接。 如需詳細資訊，請洽詢 [Microsoft 對等互連](mailto:peeringexperience@microsoft.com)。

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>升級或降級作用中連接的頻寬
1. 選取您要修改的對等互連連線，然後選取 **...**  > **編輯連接**。
    > [!div class="mx-imgBorder"]
    > ![編輯連線](../media/setup-direct-modify-editconnection.png)

1. 移動滑杆來修改頻寬，然後選取 [ **儲存**]。
    > [!div class="mx-imgBorder"]
    > ![修改頻寬](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>新增使用中連線的 IPv4 或 IPv6 會話資訊
1. 選取您要修改的對等互連連線，然後選取 **...**  > **編輯連接**，如步驟1所示。
1. 輸入 **會話 IPv4 首碼** 或 **會話 IPv6 首碼** 資訊，然後選取 [ **儲存**]。

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>移除作用中連線的 IPv4 或 IPv6 會話資訊
入口網站目前不支援移除 **會話 IPv4 首碼** 或 **會話 IPv6 首碼** 資訊。 如需詳細資訊，請洽詢 [Microsoft 對等互連](mailto:peeringexperience@microsoft.com)。
