---
title: 透過瀏覽器連接到您的虛擬機器-Azure |Microsoft Docs
description: 瞭解如何透過瀏覽器連接到您的虛擬機器。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 352fc5fd3ff53a00d9f62966ecf21417ad898706
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288052"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>透過瀏覽器連接到您的虛擬機器 

DevTest Labs 與[Azure](../bastion/index.yml)防禦整合，可讓您透過瀏覽器連接到您的虛擬機器。 如需如何在 DevTest Labs 中啟用這項功能的相關資訊，請參閱[在實驗室虛擬機器上啟用瀏覽器](enable-browser-connection-lab-virtual-machines.md)連線。

啟用*瀏覽器*連線之後，實驗室使用者就可以透過瀏覽器存取虛擬機器。  

## <a name="create-a-lab-virtual-machine"></a>建立實驗室虛擬機器

首先，您必須在已設定防禦的虛擬網路內建立實驗室虛擬機器。 選取您建立的第二**個子網**，而不是 AzureBastionSubnet。 您可以在建立虛擬機器時選取虛擬網路，方法是前往 [ **Advanced settings** ] 索引標籤。

![建立虛擬機器](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>在瀏覽器中啟動虛擬機器

建立虛擬機器之後，您可以在瀏覽器中按一下 [*瀏覽器]* [連線] 按鈕，然後輸入您電腦的使用者名稱和密碼，以將它啟動。  

![在瀏覽器中啟動](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>後續步驟

[在 Azure DevTest Labs 中對實驗室新增 VM](devtest-lab-add-vm.md)
