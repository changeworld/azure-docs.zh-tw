---
title: Azure 實驗室服務中控制 Windows 關閉行為的指南 |微軟文件
description: 自動關閉空閒 Windows 虛擬機器並刪除 Windows 關機命令的步驟。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 39ff4f42457451dfa4aae90b281d6b163c56b4cd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522234"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>控制 Windows 關機行為指南

Azure 實驗室服務提供了多個成本控制,以確保 Windows 虛擬機器 (VM) 不會意外執行:
 - [設定計劃](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [設定使用者的配額](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [在中斷連線時啟用自動關機](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

即使使用這些成本控制,在某些情況下,Windows VM 也可能意外繼續運行;因此,從學生的配額中扣除:

- **RDP 視窗處於開啟狀態**
  
    當學生使用 RDP 連接到其 VM 時,他們可能會無意中使 RDP 視窗保持打開狀態。  只要 RDP 視窗保持打開狀態,**斷開連接設置上的自動關機**將永遠不會生效,因為它僅在斷開 RDP 會話後觸發。

- **Windows 關機命令用於關閉 VM**
  
    學生可以使用 Windows 關機指令或 Windows 中提供的其他關閉機制來關閉 VM,而不是使用[Azure 實驗室服務的停止按鈕](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)。  發生這種情況時,從 Azure 實驗室服務的角度來看,VM 仍在使用中。
    
為説明您防止發生這些情況,本指南提供了自動關閉空閒 Windows VM 並從 **「開始」** 選單中刪除 Windows 關機命令的步驟。  

> [!NOTE]
> 當學生啟動 VM 時,VM 也可能意外地從配額中扣除,但從未使用 RDP 實際連接到該 VM。  本指南當前*未*解決此方案。  相反,應提醒學生在啟動 RDP 後立即連接到他們的 VM;或者,他們應該停止 VM。

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>閒置 VM 的自動 RDP 斷線連線與關機

Windows 提供**本地組策略**設置,可用於設置時間限制,以便在 RDP 會話空閒時自動斷開連接。  當*沒有任何*滑鼠+鍵盤輸入時,會話被確定為空閒。  任何不涉及滑鼠鍵盤輸入的長運行活動都會導致 VM 處於空閒狀態。  這包括執行長查詢、流視頻、編譯等。 根據類的需求,您可以選擇設置空閒時間限制,以便其足夠長的時間來處理這些類型的活動。  例如,如果需要,可以將空閑時間限制設置為 1 小時或更長時間。

以下是學生在將**空閒會話限制**與[**斷開連接設定時自動關機**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)結合使用時的經驗:
 1. 學生使用 RDP 連接到其 Windows VM。
 2. 當學生離開他們的 RDP 視窗開啟,並且 VM 處於您指定的**空閒作業階段限制**的空閒狀態(例如 5 分鐘),學生將看到以下對話框:

    ![閒置時間限制過期對話框](../media/how-to-windows-shutdown/idle-time-expired.png)

1. 如果學生*不*按下 **「確定**」,則 2 分鐘后,他們的 RDP 會話將自動斷開連接。
2. RDP 工作階段斷線連線後,一旦達到**斷開連接設定時自動關閉**的指定時間範圍,Azure 實驗室服務將自動關閉 VM。

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>在樣本 VM 上設定 RDP 閒置作業階段時間限制

要設置 RDP 工作階段空閒時間限制,可以連接到範本 VM 並執行以下 PowerShell 文本。

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 90

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
或者,您可以選擇使用範本 VM 執行以下手動步驟:

1. 按 Windows 鍵,鍵入**gpedit,** 然後選擇 **「編輯組策略」(控制面板)。**

1. 跳到**電腦設定>管理樣本> Windows 元件>遠端桌面服務>遠端桌面工作階段主機>會作業階段時間限制**。  

    ![本機群組原則編輯器](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. 右鍵按下**設定活動但空閒遠端桌面服務會話的時間限制**,然後單擊「**編輯**」。

1. 輸入以下設定,然後按下 **「確定**」 :
   1. 選取 [已啟用]****。
   1. 在**選項**選項下,指定**空閒工作階段限制**。

    ![閒置工作階段限制](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. 最後,要將此行為與**斷開連線設定上的自動關機**相結合,應按照操作方法文章中的步驟:[在斷開連接時啟用 VM 的自動關閉](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)。

## <a name="remove-windows-shutdown-command-from-start-menu"></a>從「開始」選單中移除 Windows 關機命令

Windows**本地群組原則**設定還允許您從 **「開始」** 選單中刪除關機命令。

要刪除關機命令,可以連接到範本 VM 並執行以下 PowerShell 文稿。

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

或者,您可以選擇使用範本 VM 執行以下手動步驟:

1. 按 Windows 鍵,鍵入**gpedit,** 然後選擇 **「編輯組策略」(控制面板)。**

1. 跳到**電腦設定>"開始選單' 與「工作列」 >管理模板**。  

    ![本機群組原則編輯器](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. 右鍵按**下 「刪除並阻止存取關閉」、「重新啟動」、「睡眠」和「休眠」命令**,然後按一下「**編輯**」。

1. 選擇 **「已啟用」** 設定,然後按下「**確定**」
 
   ![關機設定](../media/how-to-windows-shutdown/edit-shutdown.png)

1. 請注意,關閉命令不再顯示在 Windows 開始功能表下;因此,關閉命令不再顯示在**Windows"開始「** 功能表下。只顯示 **「斷開連接」** 命令。

    ![關機命令](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>後續步驟
請參考有關如何準備 Windows 樣本 VM 的文章:[在 Azure 實驗室服務中設定 Windows 樣本電腦的指南](how-to-prepare-windows-template.md)