---
title: 在 Azure 實驗室服務中控制 Windows 關機行為的指南 |Microsoft Docs
description: 自動關機閒置的 Windows 虛擬機器，並移除 Windows 關機命令的步驟。
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: c6021131787dde4fe23ec4caad107bda2e20158a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541555"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>控制 Windows 關機行為的指南

Azure Lab Services 提供數個成本控制，以確保 Windows 虛擬機器 (Vm) 未預期地執行：
 - [設定排程](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [設定使用者的配額](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [在中斷連線時啟用 VM 的自動關機](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

即使有了這些成本控制，在某些情況下，Windows VM 可能會意外地繼續執行;因此，從學生的配額中扣除：

- **RDP 視窗保持開啟**
  
    當學生使用 RDP 連線至其 VM 時，可能會不小心讓 RDP 視窗保持開啟。  只要 RDP 視窗保持開啟，[中斷連線時 **自動關機]** 設定將永遠不會生效，因為只有在 rdp 會話中斷連線後才會觸發。

- **Windows shutdown 命令可用來關閉 VM**
  
    學生可以使用 Windows 關機命令或 Windows 中提供的其他關機機制來關閉 VM，而不是使用 [Azure 實驗室服務](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)的 [停止] 按鈕。  發生這種情況時，從 Azure 實驗室服務的觀點來看，VM 仍在使用中。
    
為了協助您避免發生這些情況，本指南提供了自動關機閒置 Windows VM，並從 [ **開始** ] 功能表移除 windows 關機命令的步驟。  

> [!NOTE]
> 當學生啟動 VM 時，VM 也可能會意外地從配額中扣除，但永遠不會使用 RDP 來連線至 VM。  本指南目前 *無法* 解決這種情況。  相反地，學生應該在啟動後，使用 RDP 立即連線至其 VM;或者，它們應該會停止 VM。

## <a name="remove-windows-shutdown-command-from-start-menu"></a>從 [開始] 功能表移除 Windows 關機命令

Windows **本機群組原則** 設定也可讓您從 [ **開始** ] 功能表中移除關機命令。

若要移除關機命令，您可以連線至範本 VM，然後執行下列 PowerShell 腳本。

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

或者，您可以選擇使用範本 VM 來執行下列手動步驟：

1. 按 Windows 鍵，輸入 **gpedit.msc**，然後選取 [ **編輯群組原則] (控制台]) **。

1. 移至 [ **電腦設定 >] 系統管理範本 > [開始] 功能表和工作列**。  

    ![本機群組原則編輯器](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. 以滑鼠右鍵按一下 **[移除] 並防止存取關機、重新開機、睡眠和休眠命令**，然後按一下 [ **編輯**]。

1. 選取 [ **已啟用** ] 設定，然後按一下 **[確定]**：
 
   ![關機設定](./media/how-to-windows-shutdown/edit-shutdown.png)

1. 請注意，[關機] 命令不會再出現在 Windows [ **開始** ] 功能表下;只會顯示 [ **中斷連線]** 命令。

    ![關機命令](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>後續步驟
請參閱有關如何準備 Windows 範本 VM 的文章： [在 Azure 實驗室服務中設定 windows 範本電腦的指南](how-to-prepare-windows-template.md)
