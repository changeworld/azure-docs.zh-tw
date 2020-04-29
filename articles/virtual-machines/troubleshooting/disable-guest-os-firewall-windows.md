---
title: 停用 Azure VM 中的客體 OS 防火牆 | Microsoft Docs
description: 瞭解解決方法，以疑難排解客體作業系統防火牆篩選 VM 的部分或完整流量的情況。
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: e4cd1595d963330bd5decb366310bf5e97f59bc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422371"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>停用 Azure VM 中的客體 OS 防火牆

若您懷疑客體作業系統防火牆有篩選虛擬機器 (VM) 部分或完整流量的情況，則本文提供這種情況的參考資訊。 如果刻意變更防火牆，就可能會發生這種情況，並導致 RDP 連線失敗。

## <a name="solution"></a>解決方法

本文所述程序是作為因應措施使用，讓您專注於修正真正的問題，也就是如何正確設定防火牆規則。 Microsoft 的最佳作法是啟用 Windows 防火牆元件。 設定防火牆規則的方式取決於所需的 VM 存取層級。

### <a name="online-solutions"></a>線上解決方案 

如果 VM 為連線狀態，並可透過相同虛擬網路上的其他 VM 來存取，您即可使用另一部 VM 來進行這些緩解措施。

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>緩解措施 1：自訂指令碼延伸模組或執行命令功能

如果您具備使用中的 Azure 代理程式，即可使用[自訂指令碼延伸模組](../extensions/custom-script-windows.md)或[執行命令](../windows/run-command.md)功能 (僅限 Resource Manager VM)，從遠端執行下列指令碼。

> [!Note]
> * 如果防火牆是於本機設定，請執行下列指令碼：
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * 如果防火牆是透過 Active Directory 原則設定，您可以執行下列指令碼以取得暫時存取權。 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   不過，只要套用此原則，您就會被移出遠端工作階段。 這個問題的永久解決之道是修改套用至此電腦的原則。

#### <a name="mitigation-2-remote-powershell"></a>緩解措施 2：遠端 PowerShell

1.  針對您無法使用 RDP 連線來連接的 VM，連線到與此 VM 相同虛擬網路的某部 VM。

2.  開啟 PowerShell 主控台視窗。

3.  執行下列命令：

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> 如果防火牆是透過群組原則物件設定，此方法可能無效，因為這個命令只會變更本機登錄項目。 如果已備有原則，該原則會覆寫這項變更。 

#### <a name="mitigation-3-pstools-commands"></a>緩解措施 3：PSTools 命令

1.  在要對其進行疑難排解的 VM 上，下載 [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)。

2.  開啟 CMD 執行個體，然後透過其 DIP 存取 VM。

3.  執行下列命令：

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>緩解措施 4：遠端登錄 

請遵循下列步驟使用[遠端登錄](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry)。

1.  在疑難排解 VM 上，啟動 [登錄編輯程式]，然後移**至** > **[檔案] [連線] [網路登入]**。

2.  開啟*目的電腦*\SYSTEM 分支，然後指定下列值：

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  重新啟動服務。 因為您無法使用遠端登入來執行此動作，所以您必須使用遠端服務主控台。

4.  開啟**services.msc**的實例。

5.  按一下 [服務 (本機)]****。

6.  選取 [連線到另一部電腦]****。

7.  輸入問題 VM 的**私人 IP 位址（DIP）** 。

8.  重新啟動本機防火牆原則。

9.  再次嘗試從本機電腦透過 RDP 連線至 VM。

### <a name="offline-solutions"></a>離線解決方案 

如果任何方法皆無法存取 VM，則自訂指令碼延伸模組會失敗，且您必須直接透過系統磁碟，在 [離線] 模式下工作。 若要這樣做，請遵循下列步驟：

1.  [將系統磁碟連結至復原 VM](troubleshoot-recovery-disks-portal-windows.md)。

2.  啟動復原 VM 的遠端桌面連線。

3.  確定該磁碟在磁碟管理主控台中標示為 [線上]。 記下指派給已連結系統磁碟的磁碟機代號。

4.  進行任何變更之前，請建立 \windows\system32\config 資料夾的複本，以便在需要回復變更時使用。

5.  在要對其進行疑難排解的 VM 上，啟動登錄編輯程式 (regedit.exe)。 

6.  針對這個疑難排解程序，我們會將登錄區掛接為 BROKENSYSTEM 和 BROKENSOFTWARE。

7.  醒目提示 HKEY_LOCAL_MACHINE 機碼，然後從功能表中選取 [檔案] > [載入登錄區]。

8.  找出已連結系統磁碟上的 \windows\system32\config\SYSTEM 檔案。

9.  開啟提升權限的 PowerShell 執行個體，然後執行下列命令：

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set through AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [中斷連結系統磁碟並重新建立 VM](troubleshoot-recovery-disks-portal-windows.md)。

11. 檢查問題是否已解決。
