---
title: 使用電源外殼自訂 RDP 屬性 - Azure
description: 如何使用 PowerShell Cmdlet 自訂 Windows 虛擬桌面的 RDP 屬性。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128073"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>為主機池自訂遠端桌面協定屬性

自訂主機池的遠端桌面協定 （RDP） 屬性（如多監視器體驗和音訊重定向）可讓您根據使用者的需求為使用者提供最佳體驗。 您可以使用**Set-RdsHostPool** Cmdlet 中的 **-CustomRdp屬性**參數自訂 Windows 虛擬桌面中的 RDP 屬性。

有關受支援屬性及其預設值的完整清單，請參閱[支援的 RDP 檔設置](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。 之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>預設 RDP 屬性

預設情況下，已發佈的 RDP 檔包含以下屬性：

|RDP 屬性 | 桌上型電腦 | RemoteApps |
|---|---| --- |
| 多顯示器模式 | 啟用 | N/A |
| 啟用磁碟機重定向 | 磁碟機、剪貼簿、印表機、COM 埠、USB 設備和智慧卡| 磁碟機、剪貼簿和印表機 |
| 遠端音訊模式 | 在本地播放 | 在本地播放 |

為主機池定義的任何自訂屬性都將覆蓋這些預設值。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>添加或編輯單個自訂 RDP 屬性

要添加或編輯單個自訂 RDP 屬性，請運行以下 PowerShell Cmdlet：

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![突出顯示了 PowerShell Cmdlet get-RDSRemoteApp 的螢幕截圖，突出顯示了名稱和易記名稱。](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>添加或編輯多個自訂 RDP 屬性

要添加或編輯多個自訂 RDP 屬性，請通過提供自訂 RDP 屬性作為分號分隔字串運行以下 PowerShell Cmdlet：

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![突出顯示了 PowerShell Cmdlet get-RDSRemoteApp 的螢幕截圖，突出顯示了名稱和易記名稱。](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>重置所有自訂 RDP 屬性

您可以按照["添加"或編輯單個自訂 RDP 屬性](#add-or-edit-a-single-custom-rdp-property)中的說明將單個自訂 RDP 屬性重置為其預設值，也可以通過運行以下 PowerShell Cmdlet 來重置主機池的所有自訂 RDP 屬性：

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![突出顯示了 PowerShell Cmdlet get-RDSRemoteApp 的螢幕截圖，突出顯示了名稱和易記名稱。](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>後續步驟

現在，您已為給定的主機池自訂了 RDP 屬性，可以登錄到 Windows 虛擬桌面用戶端以作為使用者會話的一部分對其進行測試。 接下來的兩個"如何"將告訴您如何使用您選擇的用戶端連接到會話：

- [與 Windows 桌面用戶端連線](connect-windows-7-and-10.md)
- [與 Web 用戶端連線](connect-web.md)
