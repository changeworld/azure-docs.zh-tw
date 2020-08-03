---
title: Windows 虛擬桌面的新增功能？ - Azure
description: Windows 虛擬桌面的新功能和產品更新。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 06/15/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 9be1053600ab89a7879a04a7c08a44ddf3bc862a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291236"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Windows 虛擬桌面的新增功能？

Windows 虛擬桌面會定期更新。 本文可讓您了解：

- 最新的更新
- 新功能
- 現有功能的改進
- 錯誤修正

本文每月更新。 務必經常回到這裡查看，以掌握新的更新。

## <a name="june-2020"></a>2020 年 6 月

上個月，我們在預覽版中引進了 Windows 虛擬桌面與 Azure Resource Manager 的整合。 這項更新有許多令人興奮的新功能，我們很樂意告知您相關資訊。 以下是此版 Windows 虛擬桌面的新功能。

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager-preview"></a>Windows 虛擬桌面現在與 Azure Resource Manager 整合 (預覽)

Windows 虛擬桌面現在整合至 Azure Resource Manager。 在最新的更新中，所有 Windows 虛擬桌面物件現在都是 Azure Resource Manager 資源。 這項更新也會與 Azure 角色型存取控制 (RBAC) 整合。 若要深入了解，請參閱[什麼是 Azure Resource Manager？](../azure-resource-manager/management/overview.md)。

以下是此變更為您做的事：

- Windows 虛擬桌面現在與 Azure 入口網站整合。 這表示您可以直接在入口網站中管理一切，而不需要 PowerShell、Web 應用程式或協力廠商工具。 若要開始使用，請參閱教學課程中的[透過 Azure 入口網站建立主機集區](create-host-pools-azure-marketplace.md)。

- 在此更新之前，您只能將 RemoteApp 和桌面發佈給個別使用者。 使用 Azure Resource Manager，您現在可以將資源發佈給 Azure Active Directory 群組。

- 舊版 Windows 虛擬桌面有四個內建的管理員角色，您可以將其指派給租用戶或主機集區。 這些角色目前在[角色型存取控制](../role-based-access-control/overview.md)中。 您可以將這些角色套用到每個 Windows 虛擬桌面 Azure Resource Manager 物件，讓您擁有完整的豐富委派模型。

- 在此更新中，您不再需要重複執行 Azure Marketplace 或 GitHub 範本來展開主機集區。 您只需要展開主機集區，就可以移至 Azure 入口網站中的主機集區，然後選取 [+ 新增] 來部署額外的工作階段主機。

- 主機集區部署現在已與 [Azure 共用映像庫](../virtual-machines/windows/shared-image-galleries.md)完全整合。 共用映像庫是個別的 Azure 服務，可儲存虛擬機器 (VM) 映像定義，包括映像版本控制。 您也可以使用全域複寫，將您的映像複製並傳送到其他 Azure 區域，以進行本機部署。

- 透過 PowerShell 或診斷服務 Web 應用程式完成的監視功能現在已移至 Azure 入口網站中的 Log Analytics。 您現在也有兩個選項可將報告視覺化。 您可以執行 Kusto 查詢，並使用活頁簿來建立視覺效果報告。

- 您不再需要完成 Azure Active Directory (Azure AD) 同意，即可使用 Windows 虛擬桌面。 在此更新中，Azure 訂用帳戶上的 Azure AD 租用戶會驗證您的使用者，並為您的管理員提供 RBAC 控制項。


### <a name="powershell-support"></a>PowerShell 支援

我們已將新的 AzWvd Cmdlet 與此更新加入至 Azure PowerShell AZ 模組。 在 .NET Core 上執行的 PowerShell Core 中支援這個新模組。

若要安裝模組，請遵循[設定適用於 Windows 虛擬桌面的 PowerShell 模組](powershell-module.md)中的指示。

您也可以在 [AzWvd PowerShell 參考](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization)中查看可用命令的清單。

如需有關新功能的詳細資訊，請參閱[我們的部落格文章](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)。 

### <a name="additional-gateways"></a>其他閘道

我們已在南非新增閘道叢集，以減少連線延遲。

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Windows 虛擬桌面 (預覽) 上的 Microsoft 小組

我們已對 Windows 虛擬桌面的 Microsoft 小組做了一些改進。 最重要的是，Windows 虛擬桌面現在支援音訊和視覺重新導向來進行通話。 重新導向會在使用者使用音訊或視訊進行通話時建立直接路徑，藉此改善延遲。 更短的距離表示更少的躍點，讓通話的外觀和聲音更順暢。

若要進一步了解，請參閱[我們的部落格文章](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)。

## <a name="next-steps"></a>後續步驟

在 [Microsoft 365 Windows 虛擬桌面藍圖](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)深入了解未來的計劃。

請參閱這些文章，以了解哪些用戶端更新適用於 Windows 虛擬桌面和遠端桌面服務：

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
