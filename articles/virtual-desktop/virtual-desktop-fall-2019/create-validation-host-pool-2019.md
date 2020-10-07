---
title: Windows 虛擬桌面 (傳統) 主機集區服務更新 - Azure
description: 了解將更新推展到生產環境之前，如何在 Windows 虛擬桌面 (傳統版) 中建立驗證主機集區以監視服務更新。
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 291f1e8b8870257c233dc32894ff49b26c0a3501
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323524"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-in-windows-virtual-desktop-classic"></a>教學課程：建立主機集區來驗證 Windows 虛擬桌面 (傳統) 中的服務更新

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../create-validation-host-pool.md)。

主機集區是 Windows 虛擬桌面租用戶環境中一或多個相同虛擬機器的集合。 我們建議您建立驗證主機集區，服務更新會先在其中套用。 這可讓您在服務將服務更新套用至您的標準或非驗證環境之前，先進行監視。 若沒有驗證主機集區，您可能無法發現引入錯誤的變更，這可能會導致您生產環境中的使用者停機。

為了確保您的應用程式可使用最新更新，驗證主機集區應盡可能類似於您非驗證環境中的主機集區。 使用者應該經常連線到驗證主機集區，就如同連線到標準主機集區一樣。 如果您已在主機集區上進行自動化測試，則應該在驗證主機集區上包含自動化測試。

您可以使用[診斷功能](diagnostics-role-service-2019.md)或 [Windows 虛擬桌面疑難排解文章](troubleshoot-set-up-overview-2019.md)，在驗證主機集區中進行問題偵錯。

>[!NOTE]
> 建議您將驗證主機集區保持原狀，以測試所有未來的更新。

開始之前，[下載並匯入 Windows 虛擬桌面 PowerShell 模組](/powershell/windows-virtual-desktop/overview/) (如果您還沒這麼做的話)。 之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>建立您的主機集區

您可以依照下列文章中的指示來建立主機集區：
- [教學課程：透過 Azure Marketplace 建立主機集區](create-host-pools-azure-marketplace-2019.md)
- [使用 Azure Resource Manager 範本建立主機集區](create-host-pools-arm-template.md)
- [使用 PowerShell 建立主機集區](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>定義您的主機集區作為驗證主機集區

執行下列 PowerShell Cmdlet 來定義新的主機集區作為驗證主機集區。 以您工作階段相關的值取代引號中的值：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

執行下列 PowerShell Cmdlet 來確認已設定驗證屬性。 以您工作階段相關的值取代引號中的值。

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

Cmdlet 的結果應該類似以下輸出：

```
    TenantName          : contoso
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>更新排程

每個月都會進行服務更新。 如有重大問題，則會更頻繁地提供重大更新。

## <a name="next-steps"></a>後續步驟

現在您已建立驗證主機集區，接下來可以了解如何使用 Azure 服務健康狀態來監視您的 Windows 虛擬桌面部署。

> [!div class="nextstepaction"]
> [設定服務警示](set-up-service-alerts-2019.md)
