---
title: 在 Azure DevTest Labs 虛擬機器上啟用瀏覽器連接 |Microsoft Docs
description: DevTest Labs 現在與 Azure 防禦整合，身為實驗室的擁有者，您可以啟用透過瀏覽器存取所有實驗室虛擬機器的程式。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9b901d36907c8d2bbae866c38bbf1c2554e0f1da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482933"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>在實驗室虛擬機器上啟用瀏覽器連接 

DevTest Labs 與[Azure](https://docs.microsoft.com/azure/bastion/)防禦整合，可讓您透過瀏覽器連接到您的虛擬機器。 您必須先在實驗室虛擬機器上啟用瀏覽器連線。

身為實驗室的擁有者，您可以啟用透過瀏覽器存取所有實驗室虛擬機器的程式。 不需要額外用戶端、代理程式或軟體。 Azure 防禦可透過 TLS 直接在 Azure 入口網站中，為您的虛擬機器提供安全且順暢的 RDP/SSH 連線能力。 當您透過 Azure 防禦進行連接時，您的虛擬機器不需要公用 IP 位址。 如需詳細資訊，請參閱[什麼是 Azure 防禦？](../bastion/bastion-overview.md)

> [!NOTE]
> 在實驗室虛擬機器上啟用瀏覽器連線處於預覽狀態。

本文說明如何在實驗室虛擬機器上啟用瀏覽器連線。

## <a name="prerequisites"></a>必要條件 
在現有實驗室的虛擬網路中部署防禦主機 **（或）** 將您的實驗室與防禦設定的 VNet 連接。 

若要瞭解如何在 VNet 中部署防禦主機，請參閱[建立 Azure 防禦主機（預覽）](../bastion/bastion-create-host-portal.md)。 建立防禦主機時，請選取實驗室的虛擬網路。 

若要瞭解如何將您的實驗室與已設定的 VNet 連線，請參閱[在 Azure DevTest Labs 中設定虛擬網路](devtest-lab-configure-vnet.md)。 選取已部署防禦主機的 VNet，並在其中**AzureBastionSubnet** 。 詳細的步驟如下： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左側導覽功能表上的 [**所有服務**]。 
1. 從清單中選取 [ **DevTest Labs** ]。 
1. 從實驗室清單中，選取*您的實驗室*。 

    > [!NOTE]
    > Azure 防禦功能目前為預覽狀態。 其僅限於下欄區域：美國西部、美國東部、西歐、美國中南部、澳大利亞東部和日本東部。 因此，如果您的實驗室不在其中一個區域中，請在其中一個區域中建立實驗室。 
1. 在左側功能表的 [設定] 區段中，選取 [**設定****和原則**]。 
1. 選取 [**虛擬網路**]。
1. 從工具列中選取 [**新增**]。 
1. 選取已部署防禦主機的**VNet** 。 
1. 選取子網： **AzureBastionSubnet**。 

    ![子網路](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. 選取 [**在虛擬機器建立時使用**] 選項。 
1. 在工具列上選取 [儲存]。 
1. 如果您有實驗室的舊 VNet，請選取 [**...* ]，將它移除。 和**移除**。 

## <a name="enable-browser-connection"></a>啟用瀏覽器連接 

一旦您在實驗室中設定了防禦的 VNet，身為實驗室擁有者，您就可以在實驗室虛擬機器上啟用瀏覽器連線。

若要在實驗室虛擬機器上啟用瀏覽器連接，請遵循下列步驟：

1. 在 [Azure 入口網站中，流覽至*您的實驗室*。
1. 選取 [組態和原則]****。
1. 在 [**設定**] 中，選取 **[瀏覽器連線（預覽）]**。

![啟用瀏覽器連接](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>後續步驟
請參閱下列文章，以瞭解如何使用瀏覽器連線到您的 Vm：[透過瀏覽器連線到您的虛擬機器](connect-virtual-machine-through-browser.md)