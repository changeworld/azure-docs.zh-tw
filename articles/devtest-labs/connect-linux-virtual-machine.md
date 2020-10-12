---
title: 在 Azure DevTest Labs 中連接到您的 Linux 虛擬機器
description: '瞭解如何在實驗室中連接到您的 Linux 虛擬機器 (Azure DevTest Labs) '
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86531269"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>在您的實驗室中連接至 Linux VM (Azure DevTest Labs) 
本文說明如何在您的實驗室中連接到 Linux VM。 

## <a name="connect-to-a-linux-vm"></a>連線至 Linux VM
1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在搜尋列中，搜尋並選取 **DevTest Labs**。 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="搜尋並選取 DevTest Labs":::    
1. 從實驗室清單中，選取您的 **實驗室**。

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="搜尋並選取 DevTest Labs":::            
1. 在實驗室的首頁上，從 [ **我的虛擬機器** ] 清單中選取您的 Linux VM。 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="搜尋並選取 DevTest Labs":::        
5. 在 [ **總覽** ] 頁面上，您可以看到 VM 的 (FQDN) 或 IP 位址的完整功能變數名稱。 您也可以看到埠，如下圖所示。

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="搜尋並選取 DevTest Labs":::    

    請注意，即使 VM 已啟動， **[連線]** 按鈕仍會呈現灰色。 這是設計的。
6.  使用 SSH 連接到您的 Linux VM。 下列範例會連接到具有 FQDN 的 VM `mydtl07172452621450000.eastus.cloudapp.azure.com` ，並使用的使用者名稱 `vmuser` 和埠 `51637` 。 輸入使用者用來連線到 VM 的密碼。 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    您可以使用 [Putty](https://www.putty.org/) 之類的工具或任何其他 SSH 用戶端來連線至 VM。 

    使用 SSH 連線之後，您可以安裝和設定桌面環境 ([xfce](https://www.xfce.org)) 與遠端桌面 ([xrdp](http://xrdp.org)) 。  如需詳細資訊，請參閱 [安裝和設定遠端桌面以連接到 Azure 中的 LINUX VM](../virtual-machines/linux/use-remote-desktop.md)。 

## <a name="next-steps"></a>後續步驟
[如何：連接到 Windows VM](connect-windows-virtual-machine.md)
