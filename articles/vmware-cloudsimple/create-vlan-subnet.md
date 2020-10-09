---
title: 建立 Vlan/子網-Azure VMware Solution by CloudSimple
description: Azure VMware 解決方案（依 CloudSimple）-說明如何建立和管理私人雲端的 Vlan/子網，然後套用防火牆規則。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77565990"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>建立及管理私人雲端的 Vlan/子網

開啟 [網路] 頁面上的 [Vlan/子網] 索引標籤，以建立和管理私人雲端的 Vlan/子網。 建立 VLAN/子網之後，您可以套用防火牆規則。

## <a name="create-a-vlansubnet"></a>建立 VLAN/子網

1. [存取 CloudSimple 入口網站](access-cloudsimple-portal.md) ，然後選取側邊功能表上的 [ **網路** ]。
2. 選取 [ **vlan/子網**]。
3. 按一下 [ **建立 VLAN/子網**]。

    ![VLAN/子網頁面](media/vlan-subnet-page.png)

4. 選取新 VLAN/子網的私人雲端。
5. 輸入 VLAN ID。
6. 輸入子網名稱。
7. 若要在 VLAN (子網) 上啟用路由，請指定子網 CIDR 範圍。 請確定 CIDR 範圍未與任何內部部署子網、Azure 子網或閘道子網重迭。
8. 按一下 [提交]  。

    ![建立 VLAN/子網](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> 每個私人雲端有30個 Vlan 的配額。 您可以 [聯繫支援人員](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)來增加這些限制。

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>使用 VLAN 資訊在 vSphere 中設定分散式埠群組

若要在 vSphere 中建立分散式通訊埠群組，請遵循《 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">VSphere 網路指南》</a>中的 VMware 主題「新增分散式埠群組」中的指示。 設定分散式埠群組時，請提供 CloudSimple 設定的 VLAN 資訊。

![分散式埠群組](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>選取防火牆資料表

防火牆資料表和相關聯的規則是在 **網路 > 防火牆資料表** 頁面上定義。 若要選取要套用至私人雲端 VLAN/子網的防火牆資料表，請在 [ **vlan/子網**] 頁面上選取 vlan/子網，然後按一下 [**防火牆資料表附件**]。 如需有關設定防火牆資料表和定義規則的指示，請參閱 [防火牆表格](firewall.md) 。

![防火牆資料表連結](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> 子網可以與一個防火牆資料表相關聯。 防火牆資料表可以與多個子網相關聯。

## <a name="edit-a-vlansubnet"></a>編輯 VLAN/子網

若要編輯 VLAN/子網的設定，請在 [ **vlan/子網** ] 頁面上加以選取，然後按一下 [ **編輯** ] 圖示。 進行變更，然後按一下 [ **Submet**]。

## <a name="delete-a-vlansubnet"></a>刪除 VLAN/子網

若要刪除 VLAN/子網，請在 [ **vlan/子網** ] 頁面上加以選取，然後按一下 [ **刪除** ] 圖示。 按一下 [ **刪除** ] 以確認。
