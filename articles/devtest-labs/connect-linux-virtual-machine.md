---
title: 連接到 Azure DevTest Labs 中的 Linux 虛擬機器
description: 瞭解如何在實驗室中連接至 Linux 虛擬機器（Azure DevTest Labs）
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531269"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>連接到您實驗室中的 Linux VM （Azure DevTest Labs）
本文說明如何連線到您實驗室中的 Linux VM。 

## <a name="connect-to-a-linux-vm"></a>連線至 Linux VM (英文)
1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在搜尋列中，搜尋並選取 [ **DevTest Labs**]。 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="搜尋並選取 DevTest Labs":::    
1. 從實驗室清單中，選取您的**實驗室**。

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="選取您的實驗室":::            
1. 在您實驗室的首頁上，從 [**我的虛擬機器**] 清單中選取您的 Linux VM。 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="選取您的 Linux VM":::        
5. 在 [**總覽**] 頁面上，您可以看到 VM 的完整功能變數名稱（FQDN）或 IP 位址。 您也可以看到埠，如下圖所示。

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="VM 的完整功能變數名稱":::    

    請注意，即使 VM 已啟動， **[連接]** 按鈕還是會呈現灰色。 這是設計的。
6.  使用 SSH 連線到您的 Linux VM。 下列範例會連線至具有 FQDN 的 VM `mydtl07172452621450000.eastus.cloudapp.azure.com` ，其使用者名稱為 `vmuser` 和埠 `51637` 。 輸入使用者用來連線到 VM 的密碼。 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    您可以使用工具（例如[Putty](https://www.putty.org/) ）或任何其他 SSH 用戶端來連線至 VM。 

    使用 SSH 進行連線之後，您可以安裝和設定桌面環境（[xfce](https://www.xfce.org)）和遠端桌面（[xrdp](http://xrdp.org)）。  如需詳細資訊，請參閱[安裝和設定遠端桌面以連接至 Azure 中的 LINUX VM](../virtual-machines/linux/use-remote-desktop.md)。 

## <a name="next-steps"></a>後續步驟
[如何：連接到 Windows VM](connect-windows-virtual-machine.md)
