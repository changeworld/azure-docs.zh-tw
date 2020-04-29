---
title: 將子網路委派至 Azure NetApp Files | Microsoft Docs
description: 說明如何將子網路委派至 Azure NetApp Files。
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
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: b-juche
ms.openlocfilehash: b83f530549ffa43789963fd0c95b4982f5289356
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80054470"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>將子網路委派至 Azure NetApp Files 

您必須將子網路委派至 Azure NetApp Files。   在建立磁碟區時，您必須指定已委派的子網路。

## <a name="considerations"></a>考量
* 新建子網路的精靈會預設為 /24 網路遮罩，供 251 個可用的 IP 位址使用。 使用 /28 網路遮罩供 16 個可用的 IP 位址使用，對服務而言就已足夠。
* 在每個 Azure 虛擬網路 (VNet) 中，都只有一個子網路可委派給 Azure NetApp Files。   
   Azure 可讓您在 VNet 中建立多個委派的子網。  不過，如果您使用一個以上的委派子網，嘗試建立新的磁片區將會失敗。
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
* [瞭解 Azure 服務的虛擬網路整合](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


