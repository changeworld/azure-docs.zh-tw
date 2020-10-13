---
title: Windows 虛擬桌面的新增功能？ - Azure
description: Windows 虛擬桌面的新功能和產品更新。
author: Heidilohr
ms.topic: overview
ms.date: 10/01/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: 0191d6ad74a9b6349f5d1724f9483607dce2d926
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91630008"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Windows 虛擬桌面的新增功能？

Windows 虛擬桌面會定期更新。 本文可讓您了解：

- 最新的更新
- 新功能
- 現有功能的改進
- 錯誤修正

本文每月更新。 務必經常回到這裡查看，以掌握新的更新。

## <a name="september-2020"></a>2020 年 9 月

以下是 2020 年 9 月的變更：

- 我們已藉由減少下列 Azure 地理位置中的連線延遲來最佳化效能：
    - 德國
    - 南非 (僅適用於驗證環境)

您現在可以使用[體驗估算器](https://azure.microsoft.com/services/virtual-desktop/assessment/)，來估算這些區域中的使用者體驗品質。

- 我們已發行 Windows 虛擬桌面的 Windows 桌面用戶端版本 1.2.1364。 在此更新中，我們進行了下列變更：
    - 已修正單一登入 (SSO) 在 Windows 7 上無法使用的問題。
    - 已修正下列問題：當已啟用小組媒體最佳化的使用者嘗試呼叫或加入小組會議，而另一個應用程式的音訊串流以獨佔模式開啟時，導致用戶端中斷連線。
    - 已修正在啟用小組的媒體最佳化時，小組未列舉音訊或影片裝置的問題。
    - 已新增「需要設定的協助？」 桌面設定頁面的連結。
    - 已修正使用高對比深色佈景主題時，所發生的 [訂閱] 按鈕問題。
    
- 感謝使用者的大力協助，我們已修正 Microsoft Store 遠端桌面用戶端的兩個嚴重問題。 將用戶端的階段式發行版本擴大到全球更多使用者時，我們會繼續檢閱意見反應並修正問題。
    
- 我們新增了一項新功能，可讓您變更 VM 位置、映像、資源群組、首碼名稱、做為工作流程一部分的網路組態，用於在 Azure 入口網站中將 VM 新增至您的部署。

- IT 專業人員現在可以使用 Microsoft Endpoint Manager 管理已加入混合式 Azure Active Directory 的 Windows 10 企業版 VM。 若要進一步了解，請參閱[我們的部落格文章](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048)。

## <a name="august-2020"></a>2020 年 8 月

以下是 2020 年 8 月的變更：

- 我們已改善效能，以減少下列 Azure 區域中的連線延遲： 

    - 英國
    - 法國
    - 挪威
    - 南韓

   您可以使用[體驗估算器](https://azure.microsoft.com/services/virtual-desktop/assessment/)，大致了解這些變更對您的使用者有何影響。

- Microsoft Store 遠端桌面用戶端 (v10.2.1522+) 現已正式推出！ 這個版本的 Microsoft Store 遠端桌面用戶端與 Windows 虛擬桌面相容。 我們也引進了重新整理的 UI 流程，以提升使用者體驗。 此更新包括流暢的設計、淺色與深色模式，以及許多其他令人興奮的改變。 我們也將用戶端重寫為使用與 iOS、macOS 和 Android 用戶端相同的基礎遠端桌面通訊協定 (RDP) 引擎。 這可讓我們以更快的速度在所有平台上提供新功能。 [下載用戶端](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab)並試試看！

- 我們已修正 Teams 桌面用戶端 (版本 1.3.00.21759) 的問題：用戶端僅顯示聊天、頻道和行事曆中的 UTC 時區。 更新的用戶端現在改為顯示遠端工作階段的時區。

- Azure Advisor 現在屬於 Windows 虛擬桌面的一部分。 當您透過 Azure 入口網站存取 Windows 虛擬桌面時，您可以看到最佳化 Windows 虛擬桌面環境的建議。 深入了解 [Azure Advisor](azure-advisor.md)。

- Azure CLI 現在支援 Windows 虛擬桌面 (`az desktopvirtualization`)，協助您將 Windows 虛擬桌面部署自動化。 如需擴充命令的清單，請參閱 [desktopvirtualization](/cli/azure/ext/desktopvirtualization/?view=azure-cli-latest)。

- 我們已更新部署範本，使其與 Windows 虛擬桌面 Azure Resource Manager 介面完全相容。 您可以在 [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) 上找到範本。

- Windows 虛擬桌面 US Gov 入口網站現為公開預覽版。 若要進一步了解，請參閱[我們的公告](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/)。

## <a name="july-2020"></a>2020 年 7 月  

7 月時就已正式推出與 Azure 資源管理整合的 Windows 虛擬桌面。

以下是這個新版本的變更內容： 

- 「2019 年秋季版本」現在稱為「Windows 虛擬桌面 (傳統)」，而「2020 年春季版本」現在則只是「Windows 虛擬桌面。」 如需詳細資訊，請參閱[此部落格文章](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/)。 

若要深入了解新功能，請參閱[此部落格文章](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)。 

### <a name="autoscaling-tool-update"></a>自動調整工具更新

原本處於預覽狀態的最新版自動調整工具現已正式推出。 此工具會使用 Azure 自動化帳戶和 Azure 邏輯應用程式來自動關閉並重新啟動主機集區內的工作階段主機虛擬機器 (VM)，以降低基礎結構成本。 若要深入了解，請參閱[使用 Azure 自動化調整工作階段主機](set-up-scaling-script.md)。

### <a name="azure-portal"></a>Azure 入口網站

您現在可以使用 Windows 虛擬桌面中的 Azure 入口網站來執行下列操作： 

- 直接將使用者指派給個人桌面工作階段主機  
- 變更主機集區的驗證環境設定 

### <a name="diagnostics"></a>診斷

我們已針對 Log Analytics 工作區發行一些新的預建查詢。 若要存取這些查詢，請移至 [記錄]，然後在 [類別] 底下選取 [Windows 虛擬桌面]。 若要深入了解，請參閱[使用 Log Analytics 來執行診斷功能](diagnostics-log-analytics.md)。

### <a name="update-for-remote-desktop-client-for-android"></a>Android 版遠端桌面用戶端的更新

[Android 版遠端桌面用戶端](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)現在支援 Windows 虛擬桌面連線。 從 10.0.7 版開始，Android 用戶端便提供新的 UI 來改善使用者體驗。 用戶端也會與 Android 裝置上的 Microsoft Authenticator 整合，以在訂閱 Windows 虛擬桌面工作區時啟用條件式存取。  

先前的遠端桌面用戶端版本現在稱為「遠端桌面 8」。 您在舊版用戶端中擁有的任何現有連線將會順暢地轉移到新用戶端。 新用戶端已經重新編寫至與 iOS 和 macOS 用戶端相同的基礎 RDP 核心引擎，可讓新功能更快地發行到所有平台上。 

### <a name="teams-update"></a>Teams 更新

我們已對 Windows 虛擬桌面的 Microsoft Teams 做了一些改進。 最重要的是，Windows 虛擬桌面現在可為 Windows 桌面用戶端支援音訊和視訊最佳化。 重新導向會在使用者使用音訊或視訊進行通話和會議時在使用者之間建立直接路徑，藉此改善延遲。 更短的距離表示更少的躍點，讓通話的外觀和聲音更順暢。 若要深入了解，請參閱[在 Windows 虛擬桌面上使用 Teams](teams-on-wvd.md)。

## <a name="june-2020"></a>2020 年 6 月

上個月，我們在預覽版中引進了 Windows 虛擬桌面與 Azure Resource Manager 的整合。 這項更新有許多令人興奮的新功能，我們很樂意告知您相關資訊。 以下是此版 Windows 虛擬桌面的新功能。

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows 虛擬桌面現在與 Azure Resource Manager 整合

Windows 虛擬桌面現在整合至 Azure Resource Manager。 在最新的更新中，所有 Windows 虛擬桌面物件現在都是 Azure Resource Manager 資源。 這項更新也會與 Azure 角色型存取控制 (Azure RBAC) 整合。 若要深入了解，請參閱[什麼是 Azure Resource Manager？](../azure-resource-manager/management/overview.md)。

以下是此變更為您做的事：

- Windows 虛擬桌面現在與 Azure 入口網站整合。 這表示您可以直接在入口網站中管理一切，而不需要 PowerShell、Web 應用程式或協力廠商工具。 若要開始使用，請參閱教學課程中的[透過 Azure 入口網站建立主機集區](create-host-pools-azure-marketplace.md)。

- 在此更新之前，您只能將 RemoteApp 和桌面發佈給個別使用者。 使用 Azure Resource Manager，您現在可以將資源發佈給 Azure Active Directory 群組。

- 舊版 Windows 虛擬桌面有四個內建的管理員角色，您可以將其指派給租用戶或主機集區。 這些角色目前在 [Azure 角色型存取控制 (Azure RBAC)](../role-based-access-control/overview.md) 中。 您可以將這些角色套用到每個 Windows 虛擬桌面 Azure Resource Manager 物件，讓您擁有完整的豐富委派模型。

- 在此更新中，您不再需要重複執行 Azure Marketplace 或 GitHub 範本來展開主機集區。 您只需要展開主機集區，就可以移至 Azure 入口網站中的主機集區，然後選取 [+ 新增] 來部署額外的工作階段主機。

- 主機集區部署現在已與 [Azure 共用映像庫](../virtual-machines/windows/shared-image-galleries.md)完全整合。 共用映像庫是個別的 Azure 服務，可儲存虛擬機器 (VM) 映像定義，包括映像版本控制。 您也可以使用全域複寫，將您的映像複製並傳送到其他 Azure 區域，以進行本機部署。

- 透過 PowerShell 或診斷服務 Web 應用程式完成的監視功能現在已移至 Azure 入口網站中的 Log Analytics。 您現在也有兩個選項可將報告視覺化。 您可以執行 Kusto 查詢，並使用活頁簿來建立視覺效果報告。

- 您不再需要完成 Azure Active Directory (Azure AD) 同意，即可使用 Windows 虛擬桌面。 在此更新中，Azure 訂用帳戶上的 Azure AD 租用戶會驗證您的使用者，並為您的管理員提供 Azure RBAC 控制項。


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

## <a name="client-updates"></a>用戶端更新

請參閱這些文章，以了解哪些用戶端更新適用於 Windows 虛擬桌面和遠端桌面服務：

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="next-steps"></a>後續步驟

在 [Microsoft 365 Windows 虛擬桌面藍圖](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)深入了解未來的計劃。
