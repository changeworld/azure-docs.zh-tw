---
title: 針對 Azure Linux 來賓代理程式進行疑難排解
description: 針對 Azure Linux 來賓代理程式無法運作的問題進行疑難排解
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
ms.openlocfilehash: 9e3b376cfaf5379acaf92713c42509471200d066
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95549888"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>針對 Azure Linux 來賓代理程式進行疑難排解

[Azure Linux 來賓代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 是虛擬機器) 代理程式 (VM。 它可讓 VM (IP 位址168.63.129.16 上裝載 VM) 基礎實體伺服器，與網狀架構控制器通訊。 此 IP 位址是虛擬公用 IP 位址，可協助進行通訊。 如需詳細資訊，請參閱 [什麼是 IP 位址 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)。

## <a name="checking-agent-status-and-version"></a>檢查代理程式狀態和版本

請參閱https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>針對處於未就緒狀態的 VM 代理程式進行疑難排解

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>步驟1檢查 Azure Linux 來賓代理程式服務是否正在執行

根據發行版本而定，服務名稱可以是 walinuxagent 或 waagent：

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


如果您可以看到正在執行的服務，請重新開機服務，以查看問題是否已解決。 如果服務已停止，請將其啟動並等候幾分鐘。 然後檢查 **代理程式狀態** 是否已報告為 [ **就緒**]。 若要進一步疑難排解這些問題，請聯絡 [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="step-2-check-whether-auto-update-is-enabled"></a>步驟2：檢查自動更新是否已啟用

在/etc/waagent.conf 中檢查此設定：

```
AutoUpdate.Enabled=y
```

如需有關如何更新 Azure Linux 代理程式的詳細資訊，請參閱 https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>步驟3檢查 VM 是否可以連線到網狀架構控制器

使用捲曲的工具來測試 VM 是否可以連線到埠80、32526和443上的168.63.129.16。 如果 VM 未如預期般連線，請檢查是否已在 VM 的本機防火牆中開啟埠80、443和32526的輸出通訊。 如果此 IP 位址遭到封鎖，VM 代理程式可能發生各種非預期的行為。

## <a name="advanced-troubleshooting"></a>進階疑難排解

針對 Azure Linux 來賓代理程式進行疑難排解的事件會記錄在下列記錄檔中：

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>無法連接到 WireServer IP (主機 IP)  

您注意到/var/log/waagent.log 中有下列錯誤專案：

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**分析**

VM 無法連線到主機伺服器上的 WireServer IP。

**方案**

1. 因為無法連線到 WireServer IP，請使用 SSH 連接到 VM，然後嘗試從捲曲存取下列 URL： http://168.63.129.16/?comp=versions 
1. 檢查可能由防火牆、proxy 或其他可能封鎖存取 IP 位址168.63.129.16 的來源所造成的任何問題。
1. 檢查 Linux IPTables 或協力廠商防火牆是否封鎖埠80、443和32526的存取。 如需為何無法封鎖此位址的詳細資訊，請參閱 [什麼是 IP 位址 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)。


## <a name="next-steps"></a>後續步驟

若要進一步疑難排解「Windows Azure 來賓代理程式無法運作」問題，請 [洽詢 Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
