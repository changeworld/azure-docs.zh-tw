---
title: Azure VMware 解決方案（按雲簡單 ） - 設置防火牆表和規則
description: 介紹如何設置私有雲防火牆表和規則以限制子網和 VLAN 上的流量。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 986f4b0da7254ebac3725a704f32af785c72fbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244663"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>為私有雲設置防火牆表和規則

防火牆表和相關規則允許您指定適用于特定子網和 VLAN 的流量限制。

* 子網可以與一個防火牆表關聯。
* 防火牆表可以與多個子閘道聯。

## <a name="add-a-new-firewall-table"></a>添加新的防火牆表

1. [訪問雲簡單門戶](access-cloudsimple-portal.md)，並在側功能表上選擇 **"網路**"。
2. 選擇**防火牆表**。
3. 選擇 **"創建防火牆表**"。

    ![VLAN/子網頁面](media/firewall-tables-page.png)

4. 輸入表的名稱。
5. 列出了表的預設規則。 按一下 **"創建新規則**"以創建其他規則。 有關詳細資訊，請參閱以下過程。
6. 按一下"**完成"** 以保存防火牆表。

> [!IMPORTANT]
> 每個私有雲最多可以創建兩個防火牆表。

## <a name="firewall-rules"></a>防火牆規則

防火牆規則確定防火牆如何處理特定類型的流量。 所選防火牆表**的"規則**"選項卡列出了所有關聯的規則。

![防火牆規則表](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>建立防火牆規則

1. 以以下任一方式顯示創建防火牆規則的設置：
    * 創建防火牆表時按一下"**添加規則**"。
    * 在 **"網路>防火牆表**"頁上選擇特定的防火牆表，然後按一下"**創建新的防火牆規則**"。
2. 設置規則如下：
    * [名稱]****。 為規則指定名稱。
    * **優先順序**。 為規則分配優先順序。 首先執行編號較低的規則。
    * **流量類型**。 選擇規則是專用雲、Internet 還是 VPN 流量（無狀態）還是公共 IP 位址（有狀態）。
    * **通訊協定**。 選擇規則（TCP、UDP 或任何協定）所涵蓋的協定。
    * **方向**. 選擇規則是針對入站流量還是出站流量。 您必須為入站和出站流量定義單獨的規則。
    * **行動**。 選擇規則匹配（允許或拒絕）時要執行的操作。
    * **源**. 指定規則涵蓋的源（CIDR 塊、內部源或任何源）。
    * **源埠範圍**. 指定受規則約束的埠範圍。
    * **方向**. 選擇入站或出站。
    * **目的地**. 指定規則覆蓋的目標（CIDR 塊、內部或任何源）。
    * **源埠範圍**. 指定受規則約束的埠範圍。

    ![防火牆表添加規則](media/firewall-rule-create.png)

3. 按一下 **"完成"** 以保存規則並將其添加到防火牆表的規則清單中。

> [!IMPORTANT]
> 每個防火牆表最多可以有 10 個入站規則和 20 個出站規則。 這些限制可以通過[聯繫支援部門](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)來增加。

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>附加 VLAN/子網

定義防火牆表後，可以指定受表中規則約束的子網。

1. 在 **"網路** > **防火牆表"** 頁上，選擇防火牆表。
2. 打開**附加的 VLAN/子網**選項卡。
3. 按一下 **"附加到 VLAN/子網**"。
4. 選擇私有雲和 VLAN。 將顯示關聯的子網名稱和 CIDR 塊。
5. 按一下 [提交]****。
