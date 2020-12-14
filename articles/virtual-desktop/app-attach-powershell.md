---
title: Windows 虛擬桌面 MSIX 應用程式附加預覽 PowerShell-Azure
description: 如何使用 PowerShell 設定適用于 Windows 虛擬桌面的 MSIX 應用程式附加。
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8aa6a2168bff6e90d636770804900fa93f081ced
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425788"
---
# <a name="set-up-msix-app-attach-preview-using-powershell"></a>使用 PowerShell 設定 MSIX 應用程式附加 (預覽版) 

> [!IMPORTANT]
> MSIX 應用程式附加目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

除了 Azure 入口網站之外，您也可以使用 PowerShell 以手動方式設定 MSIX 應用程式附加 (preview) 。 本文將逐步引導您瞭解如何使用 PowerShell 來設定 MSIX 應用程式連接。

## <a name="requirements"></a>規格需求

>[!IMPORTANT]
>開始之前，請務必填寫並提交 [此表單](https://aka.ms/enablemsixappattach) ，以在您的訂用帳戶中啟用 MSIX 應用程式附加。 如果您沒有核准的要求，MSIX app attach 將無法運作。 要求的核准可能需要最多24小時的工作天。 當您的要求已接受且完成時，您會收到一封電子郵件。

以下是設定 MSIX app attach 所需的內容：

- 運作中的 Windows 虛擬桌面部署。 若要瞭解如何將 Windows 虛擬桌面部署 (傳統) ，請參閱 [在 Windows 虛擬桌面中建立租](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)使用者。 若要瞭解如何使用 Azure Resource Manager 整合部署 Windows 虛擬桌面，請參閱 [使用 Azure 入口網站建立主機集](./create-host-pools-azure-marketplace.md)區。
- 至少有一個作用中工作階段主機的 Windows 虛擬桌面主機集區。
- 桌面遠端應用程式群組。
- MSIX 封裝工具。
- MSIX 封裝的應用程式會展開至已上傳至檔案共用的 MSIX 映射中。
- 您的 Windows 虛擬桌面部署中將儲存 MSIX 封裝的檔案共用。
- 您上傳 MSIX 映射所在的檔案共用必須也可供主機集區中)  (Vm 的所有虛擬機器存取。 使用者將需要唯讀許可權才能存取該映射。
- 下載並安裝 PowerShell Core。
- 下載 Azure PowerShell 課程模組的公開預覽版本，並將其展開至本機資料夾。
- 執行下列 Cmdlet 以安裝 Azure 模組：

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>登入 Azure 並匯入模組

當您準備好所有需求之後，請在提升許可權的命令提示字元中開啟 PowerShell core，然後執行此 Cmdlet：

```powershell
Connect-AzAccount
```

執行之後，請使用您的認證來驗證您的帳戶。 在此情況下，系統可能會要求您提供裝置 URL 或權杖。

## <a name="import-the-azwindowsvirtualdesktop-module"></a>匯入 Az. WindowsVirtualDesktop 模組

您將需要 DesktopVirtualization 模組，才能遵循本文中的指示。

>[!NOTE]
>在公開預覽中，我們會將模組提供為個別的 ZIP 檔案，您必須手動匯入。

開始之前，您可以執行下列 Cmdlet，以查看您的會話或 VM 上是否已安裝 Az. DesktopVirtualization 模組：

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

如果您要卸載現有的模組複本並重新開始，請執行此 Cmdlet：

```powershell
Uninstall-Module Az.DesktopVirtualization
```

如果模組在您的 VM 上遭到封鎖，請執行此 Cmdlet 將其解除封鎖：

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

有了這種清除程式之後，就可以開始匯入模組。

1. 執行下列 Cmdlet，然後在系統提示您同意執行自訂程式碼時按 **R** 鍵。

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. 執行匯入 Cmdlet 之後，請執行下列 Cmdlet 來查看是否有 MSIX 的 Cmdlet：

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   如果 Cmdlet 存在，輸出應該看起來像這樣：

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   如果您沒有看到此輸出，請關閉所有 PowerShell 並 PowerShell Core 會話，然後再試一次。

## <a name="set-up-helper-variables"></a>設定 helper 變數

匯入模組之後，您必須設定 helper 變數。 下列範例將示範如何執行每一個作業。

若要取得您的訂用帳戶識別碼：

```powershell
Get-AzContext -ListAvailable | fl
```

若要使用下列名稱來選取 Azure 租使用者與訂用帳戶的內容：

```powershell
$obj = Select-AzContext -Name "<Name>"
```

若要設定訂用帳戶變數：

```powershell
$subId = $obj.Subscription.Id
```

若要設定工作區名稱：

```powershell
$ws = "<WorksSpaceName>"
```

若要設定主機集區名稱：

```powershell
$hp = "<HostPoolName>"
```

若要設定已設定工作階段主機 Vm 的資源群組：

```powershell
$rg = "<ResourceGroupName>"
```

最後，確認您已正確設定所有變數：

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>將 MSIX 套件新增至主機集區

設定好所有專案之後，就可以將 MSIX 套件新增至主機集區。 若要這樣做，您必須先取得 MSIX 映射的 UNC 路徑。

使用 UNC 路徑，執行此 Cmdlet 以展開 MSIX 映射：

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

執行這個指令程式可將 MSIX 套件新增至您想要的主機集區：

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

完成之後，請確認已使用此 Cmdlet 建立套件：

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>移除主機集區中的 MSIX 套件

若要從主機集區移除套件：

使用此 Cmdlet 取得與主機集區相關聯的所有套件清單，然後在輸出中尋找您想要移除的套件名稱：

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

或者，您也可以使用此 Cmdlet 根據其顯示名稱取得特定套件：

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

若要移除套件，請執行此 Cmdlet：

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>將 MSIX 應用程式發佈至應用程式群組

如果您已完成上述各節中的指示，則只能遵循本節中的指示。 如果您的主機集區具有作用中的工作階段主機、至少一個傳統型應用程式群組，而且已將 MSIX 套件新增至主機集區，您就可以開始使用。

若要從 MSIX 套件將應用程式發佈至應用程式群組，您必須尋找其名稱，然後在發佈 Cmdlet 中使用該名稱。

若要發佈應用程式：

執行此 Cmdlet 以列出所有可用的應用程式群組：

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

當您找到想要發佈應用程式之應用程式群組的名稱時，請在此 Cmdlet 中使用其名稱：

```powershell
$grName = "<AppGroupName>"
```

最後，您必須發佈應用程式。

- 若要將 MSIX 應用程式發佈至桌面應用程式群組，請執行下列 Cmdlet：

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- 若要將應用程式發佈至遠端應用程式群組，請改為執行下列 Cmdlet：

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>如果將使用者同時指派給相同主機集區中的遠端應用程式群組和桌面應用程式群組，則當使用者連線到遠端桌面時，他們會看到兩個群組中的 MSIX 應用程式。

## <a name="next-steps"></a>後續步驟

在 [Windows 虛擬桌面 >techcommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)詢問有關這項功能的問題。

您也可以在 [Windows 虛擬桌面意見反應中樞](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)留下 Windows 虛擬桌面的意見反應。

以下是您可能會覺得有用的一些其他文章：

- [MSIX 應用程式附加詞彙](app-attach-glossary.md)
- [MSIX 應用程式附加常見問題](app-attach-faq.md)