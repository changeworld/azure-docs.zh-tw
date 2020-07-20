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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81681043"
---
本節說明如何針對直接對等互連執行下列修改作業。

### <a name="add-direct-peering-connections"></a>新增直接對等互連連線
1. 選取 [ **+ 新增連接**] 按鈕，並設定新的對等互連連線。
    > [!div class="mx-imgBorder"]
    > ![對等互連資源檢視](../media/setup-direct-modify-addconnection.png)

1. 填寫 [**直接對等互連] 連接**表單，然後選取 [**儲存**]。 如需設定對等互連連線的說明，請參閱「建立和布建直接對等互連」一節中的步驟。
    > [!div class="mx-imgBorder"]
    > ![直接對等互連連接表單](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>移除直接對等互連連線

Azure 入口網站目前不支援移除連接。 如需詳細資訊，請聯絡[Microsoft 對等互連](mailto:peeringexperience@microsoft.com)。

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>升級或降級作用中連接的頻寬
1. 選取您想要修改的對等互連連接，然後選取 [ **...**  >  ]**編輯連接**。
    > [!div class="mx-imgBorder"]
    > ![編輯連線](../media/setup-direct-modify-editconnection.png)

1. 移動滑杆來修改頻寬，然後選取 [**儲存**]。
    > [!div class="mx-imgBorder"]
    > ![修改頻寬](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>在使用中連接上新增 IPv4 或 IPv6 會話資訊
1. 選取您想要修改的對等互連連接，然後選取 [ **...**  >  ]如步驟1所示，**編輯連接**。
1. 輸入**會話 IPv4 首碼**或**會話 IPv6 首碼**資訊，然後選取 [**儲存**]。

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>移除作用中連線的 IPv4 或 IPv6 會話資訊
入口網站目前不支援移除**會話 IPv4 首碼**或**會話 IPv6 首碼**資訊。 如需詳細資訊，請聯絡[Microsoft 對等互連](mailto:peeringexperience@microsoft.com)。
