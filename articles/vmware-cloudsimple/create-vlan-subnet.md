---
title: 創建 VLAN/子網 - Azure VMware 解決方案（按雲簡單）
description: Azure VMware 解決方案（按雲簡單 - 描述如何為私有雲創建和管理 VLAN/子網，然後應用防火牆規則）。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565990"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>為您的私有雲創建和管理 VLAN/子網

打開"網路"頁上的 VLAN/子網選項卡，以創建和管理私有雲的 VLAN/子網。 創建 VLAN/子網後，可以應用防火牆規則。

## <a name="create-a-vlansubnet"></a>創建 VLAN/子網

1. [訪問雲簡單門戶](access-cloudsimple-portal.md)，並在側功能表上選擇 **"網路**"。
2. 選擇**VLAN/子網**。
3. 按一下 **"創建 VLAN/子網**"。

    ![VLAN/子網頁面](media/vlan-subnet-page.png)

4. 為新的 VLAN/子網選擇私有雲。
5. 輸入 VLAN ID。
6. 輸入子網名稱。
7. 要在 VLAN（子網）上啟用路由，請指定子網 CIDR 範圍。 確保 CIDR 範圍不會與任何本地子網、Azure 子網或閘道子網重疊。
8. 按一下 [提交]****。

    ![創建 VLAN/子網](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> 每個私有雲的配額為 30 個 VLAN。 這些限制可以通過[聯繫支援部門](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)來增加。

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>使用 VLAN 資訊在 vSphere 中設置分散式埠組

要在 vSphere 中創建分散式埠組，請按照 vMware 主題"添加分散式埠組"中的說明<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">操作。</a> 設置分散式埠組時，請從 CloudSimple 配置中提供 VLAN 資訊。

![分散式埠組](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>選擇防火牆表

防火牆表和相關規則在 **"網路>防火牆表**頁上定義。 要選擇要應用於私有雲的 VLAN/子網的防火牆表，請選擇 VLAN/子網按一下**VLAN/子網上的 VLAN/子網，按一下 VLAN/子網**頁上的**防火牆表附件**。 有關設置防火牆表和定義規則的說明，請參閱[防火牆表](firewall.md)。

![防火牆錶鏈接](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> 子網可以與一個防火牆表關聯。 防火牆表可以與多個子閘道聯。

## <a name="edit-a-vlansubnet"></a>編輯 VLAN/子網

要編輯 VLAN/子網的設置，請在**VLAN/子網**頁面上選擇它，然後按一下 **"編輯"** 圖示。 進行更改，然後按一下 **"子服務**"。

## <a name="delete-a-vlansubnet"></a>刪除 VLAN/子網

要刪除 VLAN/子網，請在**VLAN/子網**頁面上選擇它，然後按一下 **"刪除**"圖示。 按一下 **"刪除"** 以確認。
