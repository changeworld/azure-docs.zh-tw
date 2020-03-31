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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 2ddc56c60c547bd4ce48d620a83fb79246762bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "69642483"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>在實驗室虛擬機器上啟用瀏覽器連接 

DevTest 實驗室與[Azure 堡壘](https://docs.microsoft.com/azure/bastion/)集成 ，使您能夠通過瀏覽器連接到虛擬機器。 您首先需要在實驗室虛擬機器上啟用瀏覽器連接。

作為實驗室的擁有者，您可以通過瀏覽器啟用訪問所有實驗室虛擬機器。 不需要額外用戶端、代理程式或軟體。 Azure Bastion 通過 SSL 直接在 Azure 門戶中為虛擬機器提供安全、無縫的 RDP/SSH 連接。 通過 Azure 堡壘進行連接時，虛擬機器不需要公共 IP 位址。 有關詳細資訊，請參閱什麼是[Azure 堡壘？](../bastion/bastion-overview.md)

> [!NOTE]
> 在實驗室虛擬機器上啟用瀏覽器連接處於預覽狀態。

本文演示如何在實驗室虛擬機器上啟用瀏覽器連接。

## <a name="prerequisites"></a>Prerequisites 
在現有實驗室的虛擬網路 **（OR）** 中部署堡壘主機，將實驗室與已配置的 VNet 進行連接。 

要瞭解如何在 VNet 中部署堡壘主機，請參閱[創建 Azure 堡壘主機（預覽）。](../bastion/bastion-create-host-portal.md) 創建堡壘主機時，選擇實驗室的虛擬網路。 

要瞭解如何將實驗室與已配置的 VNet 的堡壘連接，請參閱[在 Azure 開發人員測試實驗室中配置虛擬網路](devtest-lab-configure-vnet.md)。 選擇部署了堡壘主機的 VNet 和其中的**Azure Bastion Subnet。** 詳細的步驟如下： 

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 選擇左側導航功能表上**的所有服務**。 
1. 從清單中選擇**開發人員測試實驗室**。 
1. 從實驗室清單中，選擇*您的實驗室*。 

    > [!NOTE]
    > Azure 堡壘當前處於預覽狀態。 它僅限於以下區域：美國西部、美國東部、西歐、美國中南部、澳大利亞東部和日本東部。 因此，如果您的實驗室不在其中一個區域中，請在這些區域之一創建一個實驗室。 
1. 在左側功能表的 **"設置"** 部分中選擇 **"配置"和"策略**"。 
1. 選擇**虛擬網路**。
1. 從工具列中選擇 **"添加**"。 
1. 選擇已部署堡壘主機的**VNet。** 
1. 選擇子網 **：AzureBastion 子網**。 

    ![子網路](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. 選擇 **"虛擬機器創建中的使用**"選項。 
1. 在工具列上選取 [儲存]****。 
1. 如果您有實驗室的舊 VNet，請通過選擇 "*...* 並**刪除**。 

## <a name="enable-browser-connection"></a>啟用瀏覽器連接 

在實驗室內配置了堡壘 VNet 後，作為實驗室擁有者，您可以在實驗室虛擬機器上啟用瀏覽器連接。

要在實驗室虛擬機器上啟用瀏覽器連接，請按照以下步驟操作：

1. 在 Azure 門戶中，導航到*實驗室*。
1. 選取 [組態和原則]****。
1. 在 **"設置"** 中，選擇**瀏覽器連接（預覽）。**

![啟用瀏覽器連接](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>後續步驟
請參閱以下文章，瞭解如何使用瀏覽器連接到 VM：[通過瀏覽器連接到虛擬機器](connect-virtual-machine-through-browser.md)