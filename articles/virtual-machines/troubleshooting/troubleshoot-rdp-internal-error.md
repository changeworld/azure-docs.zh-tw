---
title: 對 Azure 虛擬機器進行 RDP 連線時發生內部錯誤 | Microsoft Docs
description: 了解如何針對 Microsoft Azure 中的 RDP 內部錯誤進行疑難排解。| Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 8046e4f42db50db15c840a13b95ae1f3620a8c7f
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918252"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>嘗試透過遠端桌面連線至 Azure VM 時發生內部錯誤

本文說明當您嘗試連線至 Microsoft Azure 中的虛擬機器 (VM) 時所可能遇到的錯誤。


## <a name="symptoms"></a>徵狀

您無法使用遠端桌面通訊協定 (RDP) 來連線至 Azure VM。 連線會停滯在「正在設定遠端」區段，或者您會收到下列錯誤訊息：

- RDP 內部錯誤
- 發生內部錯誤
- 這部電腦無法連線到遠端電腦。 請嘗試重新連線。 如果問題持續發生，請連絡遠端電腦的擁有者或網路系統管理員


## <a name="cause"></a>原因

此錯誤可能是由於下列原因而發生：

- 無法存取本機的 RSA 加密金鑰。
- TLS 通訊協定已停用。
- 憑證損毀或過期。

## <a name="solution"></a>解決方法

在遵循下列步驟之前，請擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。

若要針對此問題進行疑難排解，請使用「序列主控台」，或藉由將 VM 的 OS 磁碟連結至復原 VM 來[修復離線的 VM](#repair-the-vm-offline)。


### <a name="use-serial-control"></a>使用序列主控台

連線至[序列主控台並開啟 PowerShell 執行個體](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
)。 如果 VM 上未啟用序列主控台，請移至[修復離線的 VM](#repair-the-vm-offline) 一節。

#### <a name="step-1-check-the-rdp-port"></a>步驟 1：檢查 RDP 連接埠

1. 在 PowerShell 執行個體中，使用 [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) 來檢查是否有其他應用程式正在使用連接埠 8080：

        Netstat -anob |more
2. 如果 Termservice.exe 正在使用 8080 連接埠，請移至步驟 2。 如果 Termservice.exe 以外的另一個服務或應用程式正在使用 8080 連接埠，則請遵循下列步驟：

    1. 針對正在使用 3389 服務的應用程式停止服務：

            Stop-Service -Name <ServiceName> -Force

    2. 啟動終端機服務：

            Start-Service -Name Termservice

2. 如果無法停止應用程式，或是這個方法不適合您，則請變更 RDP 的連接埠：

    1. 變更連接埠：

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. 為新的連接埠設定防火牆：

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. 在 Azure 入口網站的 RDP 連接埠中，[為新的連接埠更新網路安全性群組](../../virtual-network/security-overview.md)。

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>步驟 2：在 RDP 自我簽署憑證上設定正確的權限

1.  在 PowerShell 執行個體中，逐一執行下列命令來更新 RDP 自我簽署憑證：

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. 如果您無法使用此方法來更新憑證，請嘗試從遠端更新 RDP 自我簽署憑證：

    1. 從可以連線到問題發生所在 VM 的作用中 VM，於 [執行] 方塊中輸入 **mmc**，以開啟 Microsoft Management Console。
    2. 在 [檔案] 功能表上，依序選取 [新增/移除嵌入式管理單元]、[憑證] 和 [新增]。
    3. 選取 [電腦帳戶]，選取 [另一部電腦]，然後新增問題 VM 的 IP 位址。
    4. 移至 **Remote Desktop\Certificates** 資料夾，以滑鼠右鍵按一下憑證，然後選取 [刪除]。
    5. 從序列主控台的 PowerShell 執行個體中，重新啟動遠端桌面組態服務：

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. 重設 MachineKeys 資料夾的權限。

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. 重新啟動 VM，然後嘗試啟動 VM 的遠端桌面連線。 如果仍然發生錯誤，請移至下一個步驟。

#### <a name="step-3-enable-all-supported-tls-versions"></a>步驟 3︰啟用所有支援的 TLS 版本

RDP 用戶端會使用 TLS 1.0 作為預設通訊協定。 不過，您也可以將此設定變更為已成為新標準的 TLS 1.1。 如果 VM 上停用了 TLS 1.1，連線將會失敗。
1.  在 CMD 執行個體中，啟用 TLS 通訊協定：

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  若要避免 AD 原則覆寫變更，請暫時停止群組原則更新：

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  重新啟動 VM 來讓變更生效。 如果問題已解決，請執行下列命令以重新啟用群組原則：

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    如果變更會還原，則表示公司網域中有 Active Directory 原則。 您必須變更該原則，以避免此問題再次發生。

### <a name="repair-the-vm-offline"></a>修復離線的 VM

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>將 OS 磁碟連結至復原 VM

1. [將 OS 磁碟連結至復原 VM](../windows/troubleshoot-recovery-disks-portal.md)。
2. 在 OS 磁碟連結至復原 VM 後，請確定該磁碟在磁碟管理主控台中標示為 [線上]。 記下指派給已連結 OS 磁碟的磁碟機代號。
3. 啟動復原 VM 的遠端桌面連線。

#### <a name="enable-dump-log-and-serial-console"></a>啟用傾印記錄檔和序列主控台

若要啟用傾印記錄檔與序列主控台，請執行下列指令碼。

1. 開啟提升權限的命令提示字元工作階段 (**以系統管理員身分執行**)。
2. 執行下列指令碼：

    在此指令碼中，我們假設指派給已連結 OS 磁碟的磁碟機代號是 F。請將此磁碟機代號取代為 VM 的適當值。

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>重設 MachineKeys 資料夾的權限

1. 開啟提升權限的命令提示字元工作階段 (**以系統管理員身分執行**)。
2. 執行下列指令碼。 在此指令碼中，我們假設指派給已連結 OS 磁碟的磁碟機代號是 F。請將此磁碟機代號取代為 VM 的適當值。

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>啟用所有支援的 TLS 版本

1.  開啟提高權限的命令提示字元工作階段 (**以系統管理員身分執行**)，然後執行下列命令。 下列指令碼假設指派給所連結 OS 磁碟的磁碟機代號是 F。請將此磁碟機代號取代為 VM 的適當值。
2.  檢查所啟用的 TLS：

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  如果金鑰不存在，或者其值為 **0**，則請執行下列指令碼來啟用通訊協定：

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  啟用 NLA：

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [卸離 OS 磁碟並重新建立 VM](../windows/troubleshoot-recovery-disks-portal.md)，然後檢查問題是否已解決。





