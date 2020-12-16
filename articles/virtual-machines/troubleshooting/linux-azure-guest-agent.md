---
title: 針對 Azure Linux 代理程式進行疑難排解
description: 解決 Azure Linux 代理程式的問題。
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 247324c30bbe0edaef78c0b0d5e6a6d593e8cac9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586392"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>針對 Azure Linux 代理程式進行疑難排解

[Azure Linux 代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)可讓虛擬機器 (VM) 與 (在 IP 位址168.63.129.16 上裝載) VM 的基礎實體伺服器通訊的網狀架構控制器通訊。

>[!NOTE]
>此 IP 位址是虛擬公用 IP 位址，可促進通訊且不應封鎖。 如需詳細資訊，請參閱 [什麼是 IP 位址168.63.129.16？](../../virtual-network/what-is-ip-address-168-63-129-16.md)。

## <a name="before-you-begin"></a>開始之前

檢查代理程式狀態和版本，以確定它仍然受支援。 請參閱 [Azure 中虛擬機器代理程式的最低版本支援](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version) 以檢查版本支援，或參閱 [WALinuxAgent 常見問題](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) 以取得尋找狀態和版本的步驟。

## <a name="troubleshoot-a-not-ready-status"></a>針對未就緒狀態進行疑難排解

1. 檢查 Azure Linux 代理程式的服務狀態，以確定它正在執行。 服務名稱可能是 **walinuxagent** 或 **waagent**。

   ```
   root@nam-u18:/home/nam# service walinuxagent status
   ● walinuxagent.service - Azure Linux Agent
      Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
      Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
    Main PID: 1036 (python3)
       Tasks: 4 (limit: 4915)
      CGroup: /system.slice/walinuxagent.service
              ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
              └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers
   Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
   root@nam-u18:/home/nam#
   ```

   如果服務正在執行，請將它重新開機以解決問題。 如果服務已停止，請加以啟動、等候幾分鐘，然後再次檢查狀態。

1. 請確定已啟用自動更新。 檢查 **/etc/waagent.conf** 中的自動更新設定。

   ```
   AutoUpdate.Enabled=y
   ```

   如需有關如何更新 Azure Linux 代理程式的詳細資訊，請參閱 [如何更新 VM 上的 Azure Linux 代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent)。

1. 請確定 VM 可以連線到網狀架構控制器。 使用捲曲的工具來測試 VM 是否可以連線到埠80、443和32526上的168.63.129.16。 如果 VM 未如預期般連線，請檢查是否已在 VM 的本機防火牆中開啟埠80、443和32526的輸出通訊。 如果此 IP 位址遭到封鎖，VM 代理程式可能會顯示非預期的行為。

## <a name="advanced-troubleshooting"></a>進階疑難排解

針對 Azure Linux 代理程式進行疑難排解的事件會記錄在 **/var/log/waagent.log** 檔案中。

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>無法連接到 WireServer IP (主機 IP) 

當 VM 無法連線到主機伺服器上的 WireServer IP 時， **/var/log/waagent.log** 檔案中會出現下列錯誤。

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

若要解決此問題：

* 使用 SSH 連線到 VM，然後嘗試從捲曲的下列 URL 存取： http://168.63.129.16/?comp=versions 。
* 檢查可能由防火牆、proxy 或可能封鎖存取 IP 位址168.63.129.16 的其他來源所造成的任何問題。
* 檢查 Linux IPTables 或協力廠商防火牆是否封鎖埠80、443和32526的存取。

## <a name="next-steps"></a>後續步驟

若要進一步針對 Azure Linux 代理程式問題進行疑難排解，請 [聯絡 Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
