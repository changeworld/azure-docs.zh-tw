---
title: 在 Azure 中針對 Windows VM 上的 RDP 一般錯誤進行疑難排解 | Microsoft Docs
description: 了解如何在 Azure 中針對 Windows VM 上的 RDP 一般錯誤進行疑難排解 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f2a1a5f3eaf79a345b0d33f43d260fe6aa15236b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87439245"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>在 Azure VM 中對 RDP 一般錯誤進行疑難排解

本文將說明當您對 Azure 中的 Windows 虛擬機器 (VM) 建立遠端桌面通訊協定 (RDP) 連線時，可能會遇到的一般錯誤。

## <a name="symptom"></a>徵狀

當您對 Azure 中的 Windows VM 進行 RDP 連線時，您可能會收到下列的一般錯誤訊息：

**因為下列其中一個原因，遠端桌面無法連線到遠端電腦：**

1. **未啟用伺服器的遠端存取**

2. **遠端電腦已關閉**

3. **遠端電腦無法在網路上使用**

**請確定遠端電腦已開啟並連線到網路，而且已啟用遠端存取。**

## <a name="cause"></a>原因

發生此問題的可能原因如下：

### <a name="cause-1"></a>原因 1

下列 RDP 元件已停用：

- 在元件層級上
- 在接聽程式層級上
- 在終端伺服器上
- 在遠端桌面工作階段主機角色上

### <a name="cause-2"></a>原因 2

遠端桌面服務 (TermService) 並未執行。

### <a name="cause-3"></a>原因 3

RDP 接聽程式的設定不正確。

## <a name="solution"></a>解決方法

在遵循下列步驟之前，請擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 若要解決此問題，請使用序列控制或修復離線的 VM。

### <a name="serial-console"></a>序列主控台

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>步驟 1：在序列主控台中開啟 CMD 執行個體

1. 選取 [支援與疑難排解]**** > [序列主控台 (預覽)]**** 來存取[序列主控台](serial-console-windows.md)。 如果已在 VM 上啟用此功能，您就能成功連線該 VM。

2. 針對 CMD 執行個體建立新通道。 輸入 **CMD** 以啟動通道來取得通道名稱。

3. 切換至執行 CMD 執行個體的通道，在此案例中，它應該是通道 1。

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>步驟 2：檢查 RDP 登錄機碼的值：

1. 檢查是否已由群組原則停用 RDP。

    ```
    REM Get the group policy setting
    reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
    ```
    如果群組原則指出 RDP 已停用 (fDenyTSConnections 值為 0x1) ，請執行下列命令以啟用 TermService 服務。 如果找不到登錄機碼，則沒有設定為停用 RDP 的群組原則。 您可以移至下一個步驟。

    ```
    REM update the fDenyTSConnections value to enable TermService service
    reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f
    ```
    > [!NOTE]
    > 此步驟會暫時啟用 TermService 服務。 更新群組原則設定時，將會重設變更。 若要解決此問題，您必須檢查本機群組原則或網域群組原則是否已停用 TermService 服務，然後再更新原則設定。
    
2. 檢查目前的遠端連線設定。
    ```
    REM Get the local remote connection setting
    reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections
    ```
    如果命令傳回0x1，則 VM 不允許遠端連線。 然後，使用下列命令來允許遠端連線：
     ```
     reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
     ```
    
1. 檢查終端伺服器的目前組態。

    ```
    REM Get the local remote connection setting
    reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
    ```

      如果命令傳回 0，表示終端伺服器已停用。 那麼請啟用終端伺服器，如下所示：

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. 如果伺服器在終端伺服器陣列 (RDS 或 Citrix) 上，終端伺服器模組會設為清空模式。 檢查終端伺服器模組的目前模式。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      如果命令傳回 1，表示終端伺服器模組設為清空模式。 那麼請將模組設為工作模式，如下所示：

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. 檢查是否可以連線到終端伺服器。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      如果命令會傳回 1，表示您無法連線到終端伺服器。 那麼請啟用連線，如下所示：

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. 檢查 RDP 接聽程式的目前組態。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      如果命令傳回 0，表示 RDP 接聽程式已停用。 那麼請啟用接聽程式，如下所示：

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. 檢查是否可以連線到 RDP 接聽程式。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   如果命令會傳回 1，表示您無法連線到 RDP 接聽程式。 那麼請啟用連線，如下所示：

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. 重新啟動 VM。

8. 若要從 CMD 執行個體離開，請輸入 `exit`，然後按兩次 **Enter**。

9. 輸入 `restart` 來重新啟動 VM，然後連線至該 VM。

如果仍然發生問題，請移至步驟 2。

#### <a name="step-2-enable-remote-desktop-services"></a>步驟 2：啟用遠端桌面服務

如需詳細資訊，請參閱[無法在 Azure VM 上啟動遠端桌面服務](troubleshoot-remote-desktop-services-issues.md)。

#### <a name="step-3-reset-rdp-listener"></a>步驟 3：重設 RDP 接聽程式

如需詳細資訊，請參閱 [Azure VM 中的遠端桌面時常斷線](troubleshoot-rdp-intermittent-connectivity.md)。

### <a name="offline-repair"></a>離線修復

#### <a name="step-1-turn-on-remote-desktop"></a>步驟 1：開啟遠端桌面

1. [將 OS 磁片連結至復原 VM](./troubleshoot-recovery-disks-portal-windows.md)。
2. 啟動復原 VM 的遠端桌面連線。
3. 確定磁片在磁片管理主控台中標示為 [ **線上** ]。 記下指派給已連結 OS 磁碟的磁碟機代號。
4. 啟動復原 VM 的遠端桌面連線。
5. 開啟提升許可權的命令提示字元會話 **， (以系統管理員身分執行**) 。 執行下列指令碼。 在此指令碼中，我們假設指派給已連結 OS 磁碟的磁碟機代號是 F。請將此磁碟機代號取代為 VM 的適當值。

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. 如果 VM 已加入網域，請檢查下列登錄機碼，以查看是否有會停用 RDP 的群組原則。 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      如果機碼鍵值設為 1，表示原則已停用 RDP。 若要透過 GPO 原則啟用遠端桌面，請從網域控制站變更下列原則：

   
      **Computer Configuration\Policies\Administrative Templates:**

      Policy definitions\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections\Allow users to connect remotely by using Remote Desktop Services
  
1. 將磁碟從救援 VM 中斷連結。
1. [從磁碟建立新的 VM](../windows/create-vm-specialized.md)。

如果仍然發生問題，請移至步驟 2。

#### <a name="step-2-enable-remote-desktop-services"></a>步驟 2：啟用遠端桌面服務

如需詳細資訊，請參閱[無法在 Azure VM 上啟動遠端桌面服務](troubleshoot-remote-desktop-services-issues.md)。

#### <a name="step-3-reset-rdp-listener"></a>步驟 3：重設 RDP 接聽程式

如需詳細資訊，請參閱 [Azure VM 中的遠端桌面時常斷線](troubleshoot-rdp-intermittent-connectivity.md)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
