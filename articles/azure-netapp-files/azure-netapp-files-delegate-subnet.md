---
title: 將子網路委派至 Azure NetApp Files | Microsoft Docs
description: 瞭解如何將子網委派給 Azure NetApp Files。 當您建立磁片區時，請指定委派的子網。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: bb3d1fd49c2623ff6dcbe8a19ae8c8ca3b46425a
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930623"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>將子網路委派至 Azure NetApp Files 

您必須將子網路委派至 Azure NetApp Files。   在建立磁碟區時，您必須指定已委派的子網路。

## <a name="considerations"></a>考量

* 新建子網路的精靈會預設為 /24 網路遮罩，供 251 個可用的 IP 位址使用。 使用/28 網路遮罩（提供11個可用的 IP 位址）對服務已足夠。
* 在每個 Azure 虛擬網路 (VNet) 中，都只有一個子網路可委派給 Azure NetApp Files。   
   Azure 可讓您在 VNet 中建立多個委派的子網。  但是，如果您使用多個委派的子網，任何嘗試建立新磁片區的嘗試都會失敗。  
   VNet 中只能有一個委派的子網。 NetApp 帳戶可將磁片區部署到多個 Vnet，每個都有自己的委派子網。  
* 您無法在已委派的子網路中指定網路安全性群組或服務端點。 這樣做會導致子網路委派失敗。
* 目前不支援從全域對等互連的虛擬網路存取磁片區。
* Azure NetApp Files 的委派子網不支援 (Udr) 和網路安全性群組 (Nsg) 的[使用者定義路由](../virtual-network/virtual-networks-udr-overview.md#custom-routes)。 不過，您可將 UDR 和 NSG 套用至其他子網路，即使在與委派給 Azure NetApp Files 的子網路相同 VNet 中也一樣。  
   Azure NetApp Files 會對委派的子網建立系統路由。 如果您需要路由表以進行疑難排解，則路由會顯示在路由表的 **有效路由** 中。

## <a name="steps"></a>步驟

1.  移至 Azure 入口網站中的 [ **虛擬網路** ] 分頁，然後選取您想要用於 Azure NetApp Files 的虛擬網路。    

1. 在 [虛擬網路] 刀鋒視窗中選取 [子網路]****，然後按一下 [+ 子網路]**** 按鈕。 

1. 完成 [新增子網路] 頁面中的下列必要欄位，以建立要用於 Azure NetApp Files 的新子網路：
    * **名稱**：指定子網名稱。
    * **位址範圍**：指定 IP 位址範圍。
    * **子網委派**：選取 [ **Microsoft. NetApp/磁片**區]。 

      ![子網路委派](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
您也可以在[建立 Azure NetApp Files 的磁碟區](azure-netapp-files-create-volumes.md)時建立及委派子網路。 

## <a name="next-steps"></a>後續步驟

* [建立適用於 Azure NetApp Files 的磁碟區](azure-netapp-files-create-volumes.md)
* [了解 Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)