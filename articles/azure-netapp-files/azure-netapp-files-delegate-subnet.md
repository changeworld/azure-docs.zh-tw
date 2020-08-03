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
ms.date: 07/28/2020
ms.author: b-juche
ms.openlocfilehash: 9bb3e93b99ce8d5a61501d417a71e5e38753f5ff
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513021"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>將子網路委派至 Azure NetApp Files 

您必須將子網路委派至 Azure NetApp Files。   在建立磁碟區時，您必須指定已委派的子網路。

## <a name="considerations"></a>考量
* 新建子網路的精靈會預設為 /24 網路遮罩，供 251 個可用的 IP 位址使用。 使用/28 網路遮罩（提供11個可用的 IP 位址）就足以應付服務。
* 在每個 Azure 虛擬網路 (VNet) 中，都只有一個子網路可委派給 Azure NetApp Files。   
   Azure 可讓您在 VNet 中建立多個委派的子網。  不過，如果您使用一個以上的委派子網，嘗試建立新的磁片區將會失敗。  
   VNet 中只能有一個委派的子網。 NetApp 帳戶可以將磁片區部署到多個 Vnet，每個都有自己的委派子網。  
* 您無法在已委派的子網路中指定網路安全性群組或服務端點。 這樣做會導致子網路委派失敗。
* 目前不支援從全域對等互連的虛擬網路存取磁片區。
* 不支援在已委派給 Azure NetApp Files 的子網中，于具有位址首碼（目的地）的 VM 子網上建立[使用者定義的自訂路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)。 這麼做會影響 VM 連線能力。

## <a name="steps"></a>步驟 
1.  從 Azure 入口網站移至 [虛擬網路]**** 刀鋒視窗，並選取要用於 Azure NetApp Files 的虛擬網路。    

1. 在 [虛擬網路] 刀鋒視窗中選取 [子網路]****，然後按一下 [+ 子網路]**** 按鈕。 

1. 完成 [新增子網路] 頁面中的下列必要欄位，以建立要用於 Azure NetApp Files 的新子網路：
    * **名稱**：指定子網名稱。
    * **位址範圍**：指定 IP 位址範圍。
    * **子網委派**：選取 [ **Microsoft. NetApp/磁片**區]。 

      ![子網路委派](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
您也可以在[建立 Azure NetApp Files 的磁碟區](azure-netapp-files-create-volumes.md)時建立及委派子網路。 

## <a name="next-steps"></a>後續步驟  
* [建立適用於 Azure NetApp Files 的磁碟區](azure-netapp-files-create-volumes.md)
* [了解 Azure 服務的虛擬網路整合](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


