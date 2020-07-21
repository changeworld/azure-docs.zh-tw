---
title: 連接到 Azure DevTest Labs 中的 Windows 虛擬機器
description: 瞭解如何在實驗室中連接到您的 Windows 虛擬機器（Azure DevTest Labs）
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e1e786daa396548030976159d1b150caa4b24396
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540653"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>連接到您實驗室中的 Windows VM （Azure DevTest Labs）
本文說明如何連線到您實驗室中的 Windows VM。 

## <a name="connect-to-a-windows-vm"></a>連接到 Windows VM
1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在搜尋列中，搜尋並選取 [ **DevTest Labs**]。 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="搜尋並選取 DevTest Labs":::    
1. 從實驗室清單中，選取您的**實驗室**。

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="選取您的實驗室":::            
1. 在您實驗室的首頁上，從 [**我的虛擬機器**] 清單中選取您的 Windows VM。 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="選取您的 Windows VM":::                
1. 在 VM 的 [**虛擬機器**] 頁面上，選取工具列上的 **[連線]** 。 如果 VM 已停止，請選取 [第一次**啟動**] 來啟動 vm。

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="選取工具列上的 [連接]":::                    
1. 如果您使用的是 Edge 瀏覽器，您會在瀏覽器底部看到**下載的 RDP**檔案的連結。 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="已下載 RDP":::                        
1. 開啟 RDP 檔案，然後輸入您在建立 VM 時所輸入的 VM 認證。 您現在應該已連線到 Windows VM。 

## <a name="next-steps"></a>後續步驟
[如何：連接到 Linux VM](connect-linux-virtual-machine.md)
