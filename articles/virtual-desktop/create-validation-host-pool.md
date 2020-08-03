---
title: Windows 虛擬桌面主機集區服務更新 - Azure
description: 將更新推展到生產環境之前，如何建立驗證主機集區來監視服務更新。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 177763e7939de082faa0c83d2ab661292f0758b2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292647"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>教學課程：建立主機集區以驗證服務更新

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md)。

主機集區是 Windows 虛擬桌面租用戶環境中一或多個相同虛擬機器的集合。 將主機集區部署到生產環境之前，強烈建議您建立驗證主機集區。 更新會先套用到驗證主機集區，讓您先監視服務更新，再將其推展到生產環境。 若沒有驗證主機集區，您可能無法發現引入錯誤的變更，這可能會導致您生產環境中的使用者停機。

為了確保您的應用程式可使用最新更新，驗證主機集區應盡可能類似於您生產環境中的主機集區。 使用者應該經常連線到驗證主機集區，就如同連線到生產主機集區一樣。 如果您已在主機集區上進行自動化測試，則應該在驗證主機集區上包含自動化測試。

您可以使用[診斷功能](diagnostics-role-service.md)或 [Windows 虛擬桌面疑難排解文章](troubleshoot-set-up-overview.md)，在驗證主機集區中進行問題偵錯。

>[!NOTE]
> 建議您將驗證主機集區保持原狀，以測試所有未來的更新。

>[!IMPORTANT]
>與 Azure 資源管理整合的 Windows 虛擬桌面目前無法啟用和停用驗證環境。 我們會在解決此問題後更新本文。

## <a name="prerequisites"></a>Prerequisites

開始之前，請依照[設定 Windows 虛擬桌面 PowerShell 模組](powershell-module.md)中的指示來設定您的 PowerShell 模組，並登入 Azure。

## <a name="create-your-host-pool"></a>建立您的主機集區

您可以依照下列文章中的指示來建立主機集區：
- [教學課程：透過 Azure Marketplace 建立主機集區](create-host-pools-azure-marketplace.md)
- [使用 PowerShell 建立主機集區](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>定義您的主機集區作為驗證主機集區

執行下列 PowerShell Cmdlet 來定義新的主機集區作為驗證主機集區。 以您工作階段相關的值取代括弧中的值：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

執行下列 PowerShell Cmdlet 來確認已設定驗證屬性。 以您工作階段相關的值取代括弧中的值。

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

Cmdlet 的結果應該類似以下輸出：

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>更新排程

每個月都會進行服務更新。 如有重大問題，則會更頻繁地提供重大更新。

如果有任何服務更新，請確定您至少有一小組使用者會每天登入，以驗證環境。 我們建議您定期瀏覽我們的 [TechCommunity 網站](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true)，並關注 WVDUPdate 的任何文章，以隨時掌握服務更新的相關資訊。

## <a name="next-steps"></a>後續步驟

現在您已建立驗證主機集區，接下來可以了解如何使用 Azure 服務健康狀態來監視您的 Windows 虛擬桌面部署。 

> [!div class="nextstepaction"]
> [設定服務警示](./set-up-service-alerts.md)
