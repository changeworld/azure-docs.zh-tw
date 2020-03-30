---
title: 排除服務連接 Windows 虛擬桌面 - Azure
description: 在 Windows 虛擬桌面租戶環境中設置用戶端連接時，如何解決問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127455"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows 虛擬桌面服務連接

使用本文可以解決 Windows 虛擬桌面用戶端連接的問題。

## <a name="provide-feedback"></a>提供意見反應

您可以向我們提供回饋，並與[Windows 虛擬桌面技術社區](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)的產品團隊和其他活動社區成員討論 Windows 虛擬桌面服務。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>使用者連接，但未顯示任何內容（無源）

使用者可以啟動遠端桌面用戶端並能夠進行身份驗證，但使用者在 Web 發現源中看不到任何圖示。

確認報告問題的使用者已使用此命令列分配給應用程式組：

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

確認使用者是否使用正確的憑據登錄。

如果使用 Web 用戶端，請確認不存在緩存的憑據問題。

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Windows 10 企業多會話虛擬機器不回應

如果虛擬機器未回應，並且無法通過 RDP 訪問它，則需要通過檢查主機狀態來使用診斷功能進行故障排除。

要檢查主機狀態，請運行此 Cmdlet：

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

如果主機狀態為`NoHeartBeat`，則表示 VM 未回應，並且代理無法與 Windows 虛擬桌面服務通信。

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

您可以執行以下幾項操作來修復 NoHeartBeat 狀態。

### <a name="update-fslogix"></a>更新 FSLogix

如果您的 FSLogix 不是最新的版本，特別是如果它是 frxdrvvt.sys 的版本 2.9.7205.27375，則可能會導致鎖死。 請務必將[FSLogix 更新到最新版本](https://go.microsoft.com/fwlink/?linkid=2084562)。

### <a name="disable-bgtaskregistrationmaintenancetask"></a>禁用 BgTask 註冊維護任務

如果更新 FSLogix 不起作用，則問題可能是 BiSrv 元件在每週維護任務期間耗盡了系統資源。 使用以下兩種方法之一禁用 BgTask 註冊維護任務，從而暫時禁用維護任務：

- 轉到"開始"功能表並搜索**任務計畫程式**。 導航到**任務調度庫** > **微軟** > **Windows** > **代理基礎結構**。 查找名為**BgTask 註冊維護任務**的任務。 找到它後，按右鍵它，然後從下拉式功能表中選擇 **"禁用**"。
- 以管理員身份打開命令列功能表並運行以下命令：
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>後續步驟

- 有關 Windows 虛擬桌面和升級跟蹤的故障排除概述，請參閱[故障排除概述、回饋和支援](troubleshoot-set-up-overview.md)。
- 要在 Windows 虛擬桌面環境中創建租戶和主機池時解決問題，請參閱[租戶和主機池創建](troubleshoot-set-up-issues.md)。
- 要在 Windows 虛擬桌面中配置虛擬機器 （VM） 時解決問題，請參閱[工作階段主機虛擬機器配置](troubleshoot-vm-configuration.md)。
- 要解決在 Windows 虛擬桌面中使用 PowerShell 時的問題，請參閱[Windows 虛擬桌面電源外殼](troubleshoot-powershell.md)。
- 要完成疑難排解教程，請參閱[教程：解決資源管理器範本部署的疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
