---
title: 在 Azure 開發人員測試實驗室虛擬機器上啟用瀏覽器連接 |微軟文檔
description: DevTest Labs 現在與 Azure Bastion 集成，作為實驗室的擁有者，您可以通過瀏覽器啟用訪問所有實驗室虛擬機器。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: e2dd642139ae082cc0d0838e61399c549d2d812a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74970755"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>在實驗室虛擬機器上啟用瀏覽器連接 
DevTest 實驗室與[Azure 堡壘](https://docs.microsoft.com/azure/bastion/)集成 ，使您能夠通過瀏覽器連接到虛擬機器。 您首先需要在實驗室虛擬機器上啟用瀏覽器連接。

作為實驗室的擁有者，您可以通過瀏覽器啟用訪問所有實驗室虛擬機器。 不需要額外用戶端、代理程式或軟體。 Azure Bastion 通過 SSL 直接在 Azure 門戶中為虛擬機器提供安全、無縫的 RDP/SSH 連接。 通過 Azure 堡壘進行連接時，虛擬機器不需要公共 IP 位址。 有關詳細資訊，請參閱什麼是[Azure 堡壘？](../bastion/bastion-overview.md)


本文演示如何在實驗室虛擬機器上啟用瀏覽器連接。

## <a name="prerequisites"></a>Prerequisites 
在現有實驗室的虛擬網路 **（OR）** 中部署堡壘主機，將實驗室與堡壘配置的虛擬網路連接起來。 

要瞭解如何在虛擬網路中部署堡壘主機，請參閱創建 Azure[堡壘主機](../bastion/bastion-create-host-portal.md)。 創建堡壘主機時，選擇實驗室的虛擬網路。 

首先，您需要在堡壘虛擬網路中創建第二個子網，因為 Azure Bastion Subnet 不允許在其中創建非堡壘資源。 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>在堡壘虛擬網路中創建第二個子網路
不能在 Azure 堡壘子網中創建實驗室 VM。 在堡壘虛擬網路中創建另一個子網，如下圖所示：

![Azure 堡壘虛擬網路中的第二個子網](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>在子網中啟用 VM 創建
現在，通過按照以下步驟啟用在此子網中創建 VM： 

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 選擇左側導航功能表上**的所有服務**。 
1. 從清單中選擇**開發人員測試實驗室**。 
1. 從實驗室清單中，選擇*您的實驗室*。 

    > [!NOTE]
    > Azure 堡壘現在通常在以下區域可用：美國西部、美國東部、西歐、美國中南部、澳大利亞東部和日本東部。 因此，如果您的實驗室不在其中一個區域中，請在這些區域之一創建一個實驗室。 
    
1. 在左側功能表的 **"設置"** 部分中選擇 **"配置"和"策略**"。 
1. 選擇**虛擬網路**。
1. 從工具列中選擇 **"添加**"。 
1. 選擇部署了堡壘主機的**虛擬網路**。 
1. 為 VM 選擇子網，而不是**AzureBastionSubnet，** 這是您之前創建的另一個子網。 如果底部看不到清單中的子網，請關閉該頁面並重新打開它。 

    ![在子網中啟用 VM 創建](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. 選擇 **"虛擬機器創建中的使用**"選項。 
1. 在工具列上選取 [儲存]****。 
1. 如果您有一個舊的虛擬網路實驗室，請通過選擇 **...* 並**刪除**。 

## <a name="enable-browser-connection"></a>啟用瀏覽器連接 

在實驗室內部配置了堡壘虛擬網路後，作為實驗室擁有者，您可以在實驗室虛擬機器上啟用瀏覽器連接。

要在實驗室虛擬機器上啟用瀏覽器連接，請按照以下步驟操作：

1. 在 Azure 門戶中，導航到*實驗室*。
1. 選取 [組態和原則]****。
1. 在 **"設置"** 中，選擇 **"瀏覽器連接**"。 如果看不到此選項，請關閉 **"配置策略"** 頁，然後重新打開它。 

    ![啟用瀏覽器連接](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>後續步驟
請參閱以下文章，瞭解如何使用瀏覽器連接到 VM：[通過瀏覽器連接到虛擬機器](connect-virtual-machine-through-browser.md)
