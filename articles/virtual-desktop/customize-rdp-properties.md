---
title: 使用 PowerShell 自訂 RDP 屬性-Azure
description: 如何使用 PowerShell Cmdlet 自訂適用于 Windows 虛擬桌面的 RDP 屬性。
author: Heidilohr
ms.topic: how-to
ms.date: 09/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7c4bda1ecf28e964db6ba672157790114affe650
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462219"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>自訂主機集區的遠端桌面通訊協定 (RDP) 屬性

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md)。

自訂主機集區的遠端桌面通訊協定 (RDP) 屬性（例如多重監視器體驗和音訊重新導向），可讓您根據使用者的需求為您的使用者提供最佳體驗。 您可以使用 Azure 入口網站或在**AzWvdHostPool**指令程式中使用 *-CustomRdpProperty*參數，在 Windows 虛擬桌面中自訂 RDP 屬性。

如需支援的屬性及其預設值的完整清單，請參閱 [支援的 RDP 檔設定](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) 。

## <a name="default-rdp-file-properties"></a>預設 RDP 檔案屬性

RDP 檔案預設具有下列屬性：

|RDP 屬性|在桌面上|作為 RemoteApp|
|---|---|---|
|多重監視器模式|啟用|N/A|
|磁片磁碟機重新導向已啟用|磁片磁碟機、剪貼簿、印表機、COM 埠、USB 裝置和智慧卡|磁片磁碟機、剪貼簿和印表機|
|遠端音訊模式|在本機上播放|在本機上播放|

## <a name="prerequisites"></a>Prerequisites

開始之前，請依照[設定 Windows 虛擬桌面 PowerShell 模組](powershell-module.md)中的指示來設定您的 PowerShell 模組，並登入 Azure。

## <a name="configure-rdp-properties-in-the-azure-portal"></a>在 Azure 入口網站中設定 RDP 屬性

若要在 Azure 入口網站中設定 RDP 屬性：

1. 在 <https://portal.azure.com> 登入 Azure。
2. 在搜尋列中輸入 **windows 虛擬桌面** 。
3. 在 [服務] 下，選取 [ **Windows 虛擬桌面**]。
4. 在 [Windows 虛擬桌面] 頁面上，選取畫面左側功能表中的 [ **主機** 集區]。
5. 選取您要更新 **之主機集** 區的名稱。
6. 選取畫面左側功能表中的 [ **屬性** ]。
7. 在 [ **內容** ] 索引標籤上，移至 [ **rdp 設定** ] 開始編輯 rdp 屬性。 屬性應該採用分號分隔格式，例如 PowerShell 範例。
8. 當您完成時，請選取 [ **儲存** ] 以儲存您的變更。

接下來的章節將告訴您如何在 PowerShell 中手動編輯自訂 RDP 屬性。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>新增或編輯單一自訂 RDP 屬性

若要新增或編輯單一自訂 RDP 屬性，請執行下列 PowerShell Cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

若要檢查您剛剛執行的 Cmdlet 是否已更新屬性，請執行下列 Cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

例如，如果您正在檢查名為0301HP 的主機集區上的 "audiocapturemode" 屬性，則會輸入此 Cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>新增或編輯多個自訂 RDP 屬性

若要新增或編輯多個自訂 RDP 屬性，請將自訂 RDP 屬性提供為分號分隔的字串，以執行下列 PowerShell Cmdlet：

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

您可以執行下列 Cmdlet 來檢查以確定已新增 RDP 屬性：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

根據我們先前的 Cmdlet 範例，如果您在0301HP 主機集區上設定了多個 RDP 屬性，您的 Cmdlet 看起來會像這樣：

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>重設所有自訂 RDP 屬性

您可以依照 [新增或編輯單一自訂 rdp 屬性](#add-or-edit-a-single-custom-rdp-property)中的指示，將個別的自訂 rdp 屬性重設為預設值，也可以執行下列 PowerShell Cmdlet 來重設主機集區的所有自訂 rdp 屬性：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

若要確定您已成功移除設定，請輸入下列 Cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>接下來的步驟

既然您已針對指定的主機集區自訂 RDP 屬性，您就可以登入 Windows 虛擬桌面用戶端，在使用者會話中進行測試。 接下來的操作指南將告訴您如何使用您選擇的用戶端連接到會話：

- [與 Windows 桌面用戶端連線](connect-windows-7-10.md)
- [與 Web 用戶端連線](connect-web.md)
- [與 Android 用戶端連線](connect-android.md)
- [與 macOS 用戶端連線](connect-macos.md)
- [與 iOS 用戶端連線](connect-ios.md)
