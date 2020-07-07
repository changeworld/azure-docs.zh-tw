---
title: Azure VM 客體 OS 防火牆封鎖輸入流量 | Microsoft Docs
description: 瞭解如何修正客體作業系統防火牆封鎖輸入流量的遠端桌面入口網站（RDP）連線問題。
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
ms.openlocfilehash: 1b80fc997a4b3d2b472717b1ec2f379a4e958d8c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80422547"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Azure VM 客體 OS 防火牆封鎖輸入流量

本文說明如何解決客體作業系統防火牆封鎖輸入流量時發生的遠端桌面入口網站 (RDP) 問題。

## <a name="symptoms"></a>徵狀

您無法使用 RDP 連線來連線到 Azure 虛擬機器 (VM)。 從 [開機診斷] -> [螢幕擷取畫面] 中，顯示作業系統已在 [歡迎] 畫面 (Ctrl+Alt+Del) 完全載入。

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

未將 RDP 規則設定為允許 RDP 流量。

### <a name="cause-2"></a>原因 2

客體系統的防火牆設定檔設定為封鎖所有輸入連線，包括 RDP 流量。

![防火牆設定](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>解決方案

在按照下列步驟進行之前，請先擷取受影響虛擬機器系統磁碟的快照集作為備份。 如需詳細資訊，請參閱 [擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。

若要修正此問題，請使用[如何使用遠端工具對 Azure VM 問題進行疑難排解](remote-tools-troubleshoot-azure-vm-issues.md)的其中一種方法來遠端連線至 VM，然後編輯客體作業系統的防火牆規則，以**允許** RDP 流量。

### <a name="online-troubleshooting"></a>線上疑難排解

連線至[序列主控台，然後開啟 PowerShell 執行個體](serial-console-windows.md#use-cmd-or-powershell-in-serial-console)。 如果 VM 上未啟用序列主控台，請移至[修復離線的 VM](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)。

#### <a name="mitigation-1"></a>緩解措施 1

1.  如果 Azure 代理程式已安裝且在 vm 上正常運作，您可以使用 [vm] 功能表上 [支援與**疑難排解**] [  >  **重設密碼**] 底下的 [僅重設設定] 選項。

2.  執行此復原選項時會執行下列動作：

    *   啟用 RDP 元件 (如果已停用)。

    *   啟用 Windows 防火牆設定檔。

    *   請確定 Windows 防火牆已開啟 RDP 規則。

    *   如果上述步驟無效，請以手動方式重設防火牆規則。 若要這樣做，請執行下列命令以查詢包含「遠端桌面」名稱的所有規則：

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        如果 RDP 連接埠是設為 3389 以外的任何其他連接埠，則您必須找出任何可能已為此連接埠建立並設定的自訂規則。 若要查詢所有含自訂連接埠的輸入規則，請執行下列命令：

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  如果您看到規則已停用，請加以啟用。 若要開啟整個群組 (例如內建的遠端桌面群組)，請執行下列命令：

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    反之，若要開啟特定遠端桌面 (TCP-In) 規則，請執行下列命令：

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  如需進行疑難排解，您可以關閉防火牆設定檔：

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    在完成疑難排解並正確設定防火牆後，請重新啟用防火牆。

    > [!Note]
    > 您不需要重新啟動 VM 以套用這些變更。

5.  嘗試建立 RDP 連線以存取 VM。

#### <a name="mitigation-2"></a>緩解措施 2

1.  查詢防火牆設定檔，以判斷輸入防火牆原則是否設定為*BlockInboundAlways*：

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > 下列指導方針適用於相關防火牆原則，並依其設定方式而異：
    >    * *BlockInbound*：除非您具備允許輸入流量的有效規則，否則會封鎖所有輸入流量。
    >    * *BlockInboundAlways*：會忽略所有防火牆規則，並封鎖所有流量。

2.  編輯*DefaultInboundAction*以設定這些設定檔以**允許**流量。 若要這樣做，請執行下列命令：

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  再次查詢設定檔，以確認您的變更已成功生效。 若要這樣做，請執行下列命令：

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > 您不需要重新啟動 VM 以套用變更。

4.  再次嘗試透過 RDP 存取您的 VM。

### <a name="offline-mitigations"></a>離線風險降低措施

1.  [將系統磁碟連結至復原 VM](troubleshoot-recovery-disks-portal-windows.md)。

2.  啟動復原 VM 的遠端桌面連線。

3.  請確定磁片在 [磁片管理] 主控台中標示為 [**線上**]。 記下指派給已連結系統磁碟的磁碟機代號。

#### <a name="mitigation-1"></a>緩解措施 1

請參閱 [如何啟用/停用客體 OS 上的防火牆規則](enable-disable-firewall-rule-guest-os.md)。

#### <a name="mitigation-2"></a>緩解措施 2

1.  [將系統磁碟連結至復原 VM](troubleshoot-recovery-disks-portal-windows.md)。

2.  啟動復原 VM 的遠端桌面連線。

3.  將系統磁片連結至復原 VM 之後，請確定該磁片在磁片管理主控台中標示為 [**線上**]。 記下指派給已連結 OS 磁碟的磁碟機代號。

4.  開啟提升權限的 CMD 執行個體，然後執行下列指令碼：

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [中斷連結系統磁碟並重新建立 VM](troubleshoot-recovery-disks-portal-windows.md)。

6.  檢查問題是否已解決。
