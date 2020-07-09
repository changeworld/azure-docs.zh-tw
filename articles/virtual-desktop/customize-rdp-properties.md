---
title: 使用 PowerShell 自訂 RDP 屬性-Azure
description: 如何使用 PowerShell Cmdlet 自訂 Windows 虛擬桌面的 RDP 屬性。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4cbfe03c48c6d971c140dc332c38a47bc9234173
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135118"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>自訂主機集區的遠端桌面通訊協定屬性

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

自訂主機集區的遠端桌面通訊協定（RDP）內容，例如多監視器體驗和音訊重新導向，可讓您根據自己的需求為使用者提供最佳的體驗。 您可以使用 Azure 入口網站或在**AzWvdHostPool** Cmdlet 中使用 *-CustomRdpProperty*參數，在 WINDOWS 虛擬桌面中自訂 RDP 屬性。

如需支援的屬性及其預設值的完整清單，請參閱[支援的 RDP 檔案設定](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)。

## <a name="prerequisites"></a>Prerequisites

開始之前，請依照[設定 Windows 虛擬桌面 PowerShell 模組](powershell-module.md)中的指示來設定您的 PowerShell 模組，並登入 Azure。

## <a name="default-rdp-properties"></a>預設 RDP 屬性

根據預設，已發佈的 RDP 檔案包含下列屬性：

|RDP 屬性 | 桌上型電腦 | RemoteApps |
|---|---| --- |
| 多監視器模式 | 已啟用 | N/A |
| 磁片磁碟機重新導向已啟用 | 磁片磁碟機、剪貼簿、印表機、COM 埠、USB 裝置和智慧卡| 磁片磁碟機、剪貼簿和印表機 |
| 遠端音訊模式 | 在本機播放 | 在本機播放 |

您為主機集區定義的任何自訂屬性都會覆寫這些預設值。

## <a name="configure-rdp-properties-in-the-azure-portal"></a>在 Azure 入口網站中設定 RDP 屬性

若要在 Azure 入口網站中設定 RDP 屬性：

1. 在 <https://portal.azure.com> 登入 Azure。
2. 在搜尋列中輸入**windows 虛擬桌面**。
3. 在 [服務] 下，選取 [ **Windows 虛擬桌面**]。
4. 在 [Windows 虛擬桌面] 頁面上，選取畫面左側功能表中的 [**主機**集區]。
5. 選取您想要更新的**主機集區名稱**。
6. 選取畫面左側功能表中的 [**屬性**]。
7. **在 [內容**] 索引標籤上，移至 [ **rdp 設定**] 以開始編輯 rdp 屬性。 屬性應以分號分隔的格式，例如 PowerShell 範例。
8. 當您完成時，請選取 [**儲存**] 以儲存變更。

下一節將告訴您如何在 PowerShell 中手動編輯自訂 RDP 屬性。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>新增或編輯單一自訂 RDP 屬性

若要新增或編輯單一自訂 RDP 屬性，請執行下列 PowerShell Cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

若要檢查您剛執行的 Cmdlet 是否已更新屬性，請執行此 Cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

例如，如果您在名為0301HP 的主機集區上檢查 "audiocapturemode" 屬性，請輸入下列 Cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>新增或編輯多個自訂 RDP 屬性

若要新增或編輯多個自訂 RDP 屬性，請將自訂 RDP 屬性提供為以分號分隔的字串，以執行下列 PowerShell Cmdlet：

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

您可以藉由執行下列 Cmdlet，檢查並確定已新增 RDP 屬性：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

根據先前的 Cmdlet 範例，如果您在0301HP 主機集區上設定多個 RDP 屬性，您的 Cmdlet 會如下所示：

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>重設所有自訂 RDP 屬性

您可以遵循[新增或編輯單一自訂 rdp](#add-or-edit-a-single-custom-rdp-property)內容中的指示，將個別的自訂 rdp 屬性重設為預設值，或者，您可以執行下列 PowerShell Cmdlet 來重設主機集區的所有自訂 rdp 屬性：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

若要確定您已成功移除設定，請輸入下列 Cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>後續步驟

既然您已自訂指定主機集區的 RDP 內容，您可以登入 Windows 虛擬桌面用戶端，在使用者會話中測試這些屬性。 這些後續的操作指南將告訴您如何使用您選擇的用戶端來連線到會話：

- [與 Windows 桌面用戶端連線](connect-windows-7-and-10.md)
- [與 Web 用戶端連線](connect-web.md)
- [與 Android 用戶端連線](connect-android.md)
- [與 macOS 用戶端連線](connect-macos.md)
- [與 iOS 用戶端連線](connect-ios.md)
