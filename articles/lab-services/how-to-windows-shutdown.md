---
title: 在 Azure 實驗室服務中控制 Windows 關機行為的指南 |Microsoft Docs
description: 自動關閉閒置的 Windows 虛擬機器並移除 Windows 關機命令的步驟。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e17f6e79c3d18d82dd206954dcfb0e06b02b4d53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445163"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>控制 Windows 關機行為的指南

Azure 實驗室服務提供數個成本控制，以確保 Windows 虛擬機器（Vm）不會意外執行：
 - [設定排程](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [設定使用者的配額](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [在中斷連線時啟用 VM 的自動關機](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

即使有這些成本控制，在某些情況下，Windows VM 可能會意外繼續執行;因此，從學生的配額中扣除：

- **RDP 視窗保持開啟狀態**
  
    當學生使用 RDP 連接到其 VM 時，可能會不小心讓 RDP 視窗保持開啟狀態。  只要 RDP 視窗保持開啟狀態，[在中斷連線時**自動關機**] 設定就不會生效，因為只有在 rdp 會話中斷連線後才會觸發。

- **Windows 關機命令可用來關閉 VM**
  
    學生可以使用 Windows 關機命令，或 Windows 中提供的其他關閉機制來關閉 VM，而不是使用[Azure 實驗室服務](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)的 [停止] 按鈕。  發生這種情況時，從 Azure 實驗室服務的觀點來看，VM 仍在使用中。
    
為了協助您避免這些情況發生，本指南提供自動關閉閒置的 Windows VM，並從 [**開始**] 功能表移除 Windows 關機命令的步驟。  

> [!NOTE]
> 當學生啟動 VM 時，VM 可能也會非預期地從配額中扣除，但實際上並不會使用 RDP 來連接到它。  本指南目前*並未*解決這種情況。  相反地，學生應該會在啟動後立即使用 RDP 連接到其 VM;或者，它們應該會停止 VM。

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>閒置 VM 的自動 RDP 中斷連線和關閉

Windows 提供**本機群組原則**設定，可讓您用來設定時間限制，以在 RDP 會話閒置時自動將其中斷連線。  當沒有任何 mouse\keyboard 輸入時 *，會決定*會話為閒置狀態。  任何不牽涉到 mouse\keyboard 輸入的長時間執行活動，都會導致 VM 處於閒置狀態。  這包括執行長時間查詢、串流影片、編譯等等。 視類別的需求而定，您可以選擇設定閒置時間限制，使其夠長，足以處理這些類型的活動。  例如，您可以視需要將閒置時間限制設定為1或更多小時。

以下是當您設定**閒置會話限制**與 [中斷連線時[**自動關機]**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)設定組合時的學生體驗：
 1. 學生使用 RDP 連線到其 Windows VM。
 2. 當學生讓 RDP 視窗保持開啟狀態，且 VM 閒置達您指定的**閒置會話限制**時（例如5分鐘），學生會看到下列對話方塊：

    ![[閒置時間限制已過期] 對話方塊](./media/how-to-windows-shutdown/idle-time-expired.png)

1. 如果學生*未*按一下 **[確定]**，其 RDP 會話會在2分鐘後自動中斷連線。
2. 在 RDP 會話中斷連線之後，一旦達到 [在中斷連線時**自動關機]** 設定的指定時間範圍，Azure 實驗室服務就會自動關閉 VM。

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>設定範本 VM 上的 RDP 閒置會話時間限制

若要設定 RDP 會話閒置時間限制，您可以連接到範本 VM，並執行下列 PowerShell 腳本。

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
或者，您可以選擇使用範本 VM 來遵循下列手動步驟：

1. 按 Windows 鍵，輸入**gpedit.msc**，然後選取 **[編輯群組原則] （控制台）**。

1. 前往 [**電腦設定] > 系統管理範本 > Windows 元件 > 遠端桌面服務 > 遠端桌面工作階段主機 > 會話時間限制**]。  

    ![本機群組原則編輯器](./media/how-to-windows-shutdown/group-policy-idle.png)
   
1. 以滑鼠右鍵按一下 [**設定有效但閒置遠端桌面服務會話的時間限制**]，然後按一下 [**編輯**]。

1. 輸入下列設定，然後按一下 **[確定]**：
   1. 選取 [啟用]。
   1. 在 [**選項**] 下，指定**閒置會話限制**。

    ![閒置工作階段限制](./media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. 最後，若要將此行為與 [**在中斷連線時自動關機]** 設定結合，您應遵循操作說明文章中的步驟：[在中斷連線時啟用 vm 的自動關機](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)。

> [!WARNING]
> 當您使用 PowerShell 直接修改登錄設定，或使用 [群組原則編輯器] 來進行這項設定之後，您必須先重新開機 VM，設定才會生效。  此外，如果您使用登錄進行設定，群組原則編輯器不一定會重新整理以反映登錄設定的變更;不過，登錄設定仍會如預期般生效，而且當您指定的時間長度閒置時，您會看到 RDP 會話中斷連線。

## <a name="remove-windows-shutdown-command-from-start-menu"></a>從 [開始] 功能表移除 Windows 關機命令

Windows**本機群組原則**設定也可讓您從 [**開始**] 功能表中移除 [關機] 命令。

若要移除 shutdown 命令，您可以連接到範本 VM，並執行下列 PowerShell 腳本。

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

或者，您可以選擇使用範本 VM 來遵循下列手動步驟：

1. 按 Windows 鍵，輸入**gpedit.msc**，然後選取 **[編輯群組原則] （控制台）**。

1. 移至 [電腦設定] **> 系統管理範本 > [開始] 功能表和工作列**]。  

    ![本機群組原則編輯器](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. 以滑鼠右鍵按一下 **[移除]，並防止存取 [關機]、[重新開機]、[睡眠] 和 [休眠] 命令**，然後按一下 [**編輯**]。

1. 選取 [**已啟用**] 設定，然後按一下 **[確定]**：
 
   ![關機設定](./media/how-to-windows-shutdown/edit-shutdown.png)

1. 請注意，[關機] 命令不會再出現在 Windows [**開始**] 功能表底下;只會顯示 [**中斷連線]** 命令。

    ![Shutdown 命令](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>後續步驟
請參閱有關如何準備 Windows 範本 VM 的文章：在[Azure 實驗室服務中設定 windows 範本電腦的指南](how-to-prepare-windows-template.md)