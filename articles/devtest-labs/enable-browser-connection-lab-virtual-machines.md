---
title: 在 Azure DevTest Labs 虛擬機器上啟用瀏覽器連線
description: DevTest Labs 現在與 Azure 防禦整合，作為實驗室的擁有者，您可以透過瀏覽器啟用存取所有實驗室虛擬機器。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6d9d631c79c22f1f713cfc4ee7cdd766a4ad8f06
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341167"
---
# <a name="enable-browser-connection-on-azure-devtest-labs-virtual-machines"></a>在 Azure DevTest Labs 虛擬機器上啟用瀏覽器連線 
DevTest Labs 與 [Azure](../bastion/index.yml)防禦整合，可讓您透過瀏覽器連接到您的虛擬機器。 您需要先在實驗室虛擬機器上啟用瀏覽器連線。

作為實驗室的擁有者，您可以透過瀏覽器啟用存取所有實驗室虛擬機器。 不需要額外用戶端、代理程式或軟體。 Azure 防禦可直接在 TLS 的 Azure 入口網站中，為您的虛擬機器提供安全且順暢的 RDP/SSH 連線能力。 當您透過 Azure 防禦連線時，您的虛擬機器不需要公用 IP 位址。 如需詳細資訊，請參閱 [什麼是 Azure 防禦？](../bastion/bastion-overview.md)


本文說明如何在實驗室虛擬機器上啟用瀏覽器連線。

## <a name="prerequisites"></a>必要條件 
- 在現有實驗室的虛擬網路中部署堡壘主機 **(或)** 將您的實驗室與已設定的虛擬網路連線。
若要瞭解如何在虛擬網路中部署堡壘主機，請參閱  [建立 Azure 堡壘主機](../bastion/tutorial-create-host-portal.md)。 建立堡壘主機時，請選取實驗室的虛擬網路。 
- 實驗室使用者必須在防禦主機和已設定防禦的虛擬網路上具有「 **讀取** 者」角色。 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>在防禦虛擬網路中建立第二個子網
首先，您必須在防禦虛擬網路中建立第二個子網，因為 AzureBastionSubnet 不允許在其中建立非防禦資源。 在防禦虛擬網路內建立另一個子網，如下圖所示：

![Azure 防禦虛擬網路中的第二個子網](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>在子網中啟用 VM 建立
現在，請依照下列步驟，在此子網中啟用 Vm 的建立： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左側導覽功能表上的 [ **所有服務** ]。 
1. 從清單中選取 **DevTest Labs** 。 
1. 從實驗室清單中，選取 *您的實驗室*。 

    > [!NOTE]
    > Azure 防禦現已在下欄區域正式推出：美國西部、美國東部、西歐、美國中南部、澳大利亞東部和日本東部。 因此，如果您的實驗室不在其中一個區域中，請在其中一個區域中建立實驗室。 
    
1. 在左側功能表的 [設定] 區段中，選取 [**設定****與原則**]。 
1. 選取 [ **虛擬網路**]。
1. 從工具列中選取 [ **加入** ]。 
1. 選取已部署堡壘主機的 **虛擬網路** 。 
1. 選取 Vm 的子網，而不是您稍早建立的 **AzureBastionSubnet**。 如果您在底部的清單中看不到子網，請關閉頁面，然後重新開啟它。 

    ![在子網中啟用 VM 建立](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. 選取 [ **在虛擬機器建立時使用** ] 選項。 
1. 在工具列上選取 [儲存]。 
1. 如果您的實驗室有舊的虛擬網路，請選取 [*...*  ]，將它移除。 並 **移除**。 

## <a name="enable-browser-connection"></a>啟用瀏覽器連接 

當您在實驗室內有一個防禦設定的虛擬網路之後，您可以在實驗室虛擬機器上啟用瀏覽器連線，作為實驗室擁有者。

若要在實驗室虛擬機器上啟用瀏覽器連接，請遵循下列步驟：

1. 在 Azure 入口網站中，流覽至 *您的實驗室*。
1. 選取 [組態和原則]。
1. 在 [ **設定**] 中，選取 **[瀏覽器連線]**。 如果您沒有看到此選項，請關閉 [設定 **原則** ] 頁面，然後重新開啟它。 

    ![啟用瀏覽器連接](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>後續步驟
請參閱下列文章，以瞭解如何使用瀏覽器連接到您的 Vm： [透過瀏覽器連線到您的虛擬機器](connect-virtual-machine-through-browser.md)