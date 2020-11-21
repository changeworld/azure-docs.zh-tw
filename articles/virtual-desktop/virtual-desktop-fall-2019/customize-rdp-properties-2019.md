---
title: 使用 PowerShell Windows 虛擬桌面 (傳統) 自訂 RDP 屬性-Azure
description: 如何使用 PowerShell Cmdlet 自訂 Windows 虛擬桌面 (傳統) 的 RDP 屬性。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5110e97e52939ea2115bb839768cc7ab96802961
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020703"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>自訂 Windows 虛擬桌面 (傳統) 主機集區的遠端桌面通訊協定屬性

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../customize-rdp-properties.md)。

自訂主機集區的遠端桌面通訊協定 (RDP) 屬性（例如多重監視器體驗和音訊重新導向），可讓您根據使用者的需求為您的使用者提供最佳體驗。 您可以使用 **RdsHostPool** Cmdlet 中的 **-CustomRdpProperty** 參數，在 Windows 虛擬桌面中自訂 RDP 屬性。

如需支援的屬性及其預設值的完整清單，請參閱 [支援的 RDP 檔設定](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) 。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。 之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>預設 RDP 屬性

根據預設，已發佈的 RDP 檔案包含下列屬性：

|RDP 屬性 | 桌上型電腦 | RemoteApps |
|---|---| --- |
| 多重監視器模式 | 啟用 | N/A |
| 磁片磁碟機重新導向已啟用 | 磁片磁碟機、剪貼簿、印表機、COM 埠、USB 裝置和智慧卡| 磁片磁碟機、剪貼簿和印表機 |
| 遠端音訊模式 | 在本機上播放 | 在本機上播放 |

您為主機集區定義的任何自訂屬性都會覆寫這些預設值。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>新增或編輯單一自訂 RDP 屬性

若要新增或編輯單一自訂 RDP 屬性，請執行下列 PowerShell Cmdlet：

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![PowerShell Cmdlet Get-RDSRemoteApp 的螢幕擷取畫面，其中醒目提示 [名稱] 和 [FriendlyName] 以編輯自訂的 R D P 屬性。](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>新增或編輯多個自訂 RDP 屬性

若要新增或編輯多個自訂 RDP 屬性，請將自訂 RDP 屬性提供為分號分隔的字串，以執行下列 PowerShell Cmdlet：

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![PowerShell Cmdlet Set-RDSRemoteApp 的螢幕擷取畫面，其中醒目提示 [名稱] 和 [FriendlyName] 以編輯自訂的 R D P 屬性。](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>重設所有自訂 RDP 屬性

您可以依照 [新增或編輯單一自訂 rdp 屬性](#add-or-edit-a-single-custom-rdp-property)中的指示，將個別的自訂 rdp 屬性重設為預設值，也可以執行下列 PowerShell Cmdlet 來重設主機集區的所有自訂 rdp 屬性：

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![PowerShell Cmdlet Get-RDSRemoteApp 的螢幕擷取畫面，其中已反白顯示名稱和 FriendlyName。](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>下一步

既然您已針對指定的主機集區自訂 RDP 屬性，您就可以登入 Windows 虛擬桌面用戶端，在使用者會話中進行測試。 接下來的兩個操作說明將會說明如何使用您所選擇的用戶端，連線至工作階段：

- [與 Windows 桌面用戶端連線](connect-windows-7-10-2019.md)
- [與 Web 用戶端連線](connect-web-2019.md)