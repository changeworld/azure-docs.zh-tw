---
title: 使用 PowerShell 自訂 RDP 屬性-Azure
description: 如何使用 PowerShell Cmdlet 自訂 Windows 虛擬桌面的 RDP 屬性。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dc89b0f6ed745b3de829c0fff145adc79c5af642
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615548"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>自訂主機集區的遠端桌面通訊協定屬性

>[!IMPORTANT]
>此內容適用于不支援 Azure Resource Manager Windows 虛擬桌面物件的秋季2019版。 如果您嘗試管理春季2020更新中引進的 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../customize-rdp-properties.md)。

自訂主機集區的遠端桌面通訊協定（RDP）內容，例如多監視器體驗和音訊重新導向，可讓您根據自己的需求為使用者提供最佳的體驗。 您可以使用**RdsHostPool** Cmdlet 中的 **-CustomRdpProperty**參數，在 WINDOWS 虛擬桌面中自訂 RDP 屬性。

如需支援的屬性及其預設值的完整清單，請參閱[支援的 RDP 檔案設定](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。 之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>預設 RDP 屬性

根據預設，已發佈的 RDP 檔案包含下列屬性：

|RDP 屬性 | 桌上型電腦 | RemoteApps |
|---|---| --- |
| 多監視器模式 | 啟用 | N/A |
| 磁片磁碟機重新導向已啟用 | 磁片磁碟機、剪貼簿、印表機、COM 埠、USB 裝置和智慧卡| 磁片磁碟機、剪貼簿和印表機 |
| 遠端音訊模式 | 在本機播放 | 在本機播放 |

您為主機集區定義的任何自訂屬性都會覆寫這些預設值。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>新增或編輯單一自訂 RDP 屬性

若要新增或編輯單一自訂 RDP 屬性，請執行下列 PowerShell Cmdlet：

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![已反白顯示 [名稱] 和 [FriendlyName] 的 PowerShell Cmdlet RDSRemoteApp 螢幕擷取畫面。](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>新增或編輯多個自訂 RDP 屬性

若要新增或編輯多個自訂 RDP 屬性，請將自訂 RDP 屬性提供為以分號分隔的字串，以執行下列 PowerShell Cmdlet：

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![已反白顯示 [名稱] 和 [FriendlyName] 的 PowerShell Cmdlet RDSRemoteApp 螢幕擷取畫面。](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>重設所有自訂 RDP 屬性

您可以遵循[新增或編輯單一自訂 rdp](#add-or-edit-a-single-custom-rdp-property)內容中的指示，將個別的自訂 rdp 屬性重設為預設值，或者，您可以執行下列 PowerShell Cmdlet 來重設主機集區的所有自訂 rdp 屬性：

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![已反白顯示 [名稱] 和 [FriendlyName] 的 PowerShell Cmdlet RDSRemoteApp 螢幕擷取畫面。](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>後續步驟

既然您已自訂指定主機集區的 RDP 內容，您可以登入 Windows 虛擬桌面用戶端，在使用者會話中測試這些屬性。 接下來的兩個操作說明會告訴您如何使用您選擇的用戶端連接到會話：

- [與 Windows 桌面用戶端連線](../connect-windows-7-and-10.md)
- [與 Web 用戶端連線](connect-web-2019.md)
