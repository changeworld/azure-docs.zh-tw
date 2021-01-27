---
title: Windows 虛擬桌面的新增功能？ - Azure
description: Windows 虛擬桌面的新功能和產品更新。
author: Heidilohr
ms.topic: overview
ms.date: 01/06/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: 2f49ec0fef5aa79c602e561746eb0f6dba16cb33
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98876592"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Windows 虛擬桌面的新增功能？

Windows 虛擬桌面會定期更新。 本文可讓您了解：

- 最新的更新
- 新功能
- 現有功能的改進
- 錯誤修正

本文每月更新。 務必經常回到這裡查看，以掌握新的更新。

## <a name="december-2020"></a>2020 年 12 月

以下是 2020 年 12 月的變更： 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>適用於 Windows 虛擬桌面的 Azure 監視器

現已推出適用於 Windows 虛擬桌面的 Azure 監視器公開預覽。 這項新功能包含以 Azure 監視器活頁簿為基礎的健全儀表板，可協助 IT 專業人員了解其 Windows 虛擬桌面環境。 如需詳細資訊，請參閱[我們部落格上的公告](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587)。 

### <a name="azure-resource-manager-template-change"></a>Azure Resource Manager 範本變更 

在最新的更新中，我們移除了 Azure Resource Manager 範本中可供建立和佈建主機集區的所有公用 IP 位址參數。 我們強烈建議您避免將公用 IP 使用於 Windows 虛擬桌面，以確保您的部署安全。 如果您的部署依賴公用 IP，則必須將其重新設定為使用私人 IP，否則您的部署將無法正常運作。

### <a name="msix-app-attach-public-preview"></a>MSIX 應用程式連結公開預覽 

MSIX 應用程式連結是另一項在本月開始公開預覽的服務。 MSIX 應用程式連結是一項服務，可將 MSIX 應用程式動態呈現給 Windows 虛擬桌面工作階段主機 VM。 如需詳細資訊，請參閱[我們部落格上的公告](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231)。 

### <a name="screen-capture-protection"></a>螢幕擷取保護 

這個月也開始提供螢幕擷取保護的公開預覽。 您可使用這項功能來防止在用戶端端點上擷取敏感性資訊。 請前往[此頁面](https://aka.ms/WVDScreenCaptureProtection)來試用螢幕擷取保護。  

### <a name="built-in-roles"></a>內建角色

我們已針對系統管理員權限新增 Windows 虛擬桌面的新內建角色。 如需詳細資訊，請參閱 [Azure 資源的內建角色](rbac.md)。 

### <a name="application-group-limit-increase"></a>應用程式群組限制增加

我們已將每個 Azure Active Directory 租用戶的預設應用程式群組限制增加到 200 個群組。

### <a name="client-updates-for-december-2020"></a>2020 年 12 月的用戶端更新

我們發行了下列新版的用戶端： 

- Android
- macOS
- Windows

如需用戶端更新的詳細資訊，請參閱[用戶端更新](whats-new.md#client-updates)。

## <a name="november-2020"></a>2020 年 11 月

### <a name="azure-portal-experience"></a>Azure 入口網站體驗

我們已修正 Azure 入口網站使用者體驗中的兩個錯誤 (bug)：

- 「新增 VM」工作流程不會再覆寫桌面應用程式的易記名稱。
- 如果工作階段主機是擴展集的一部分，則現在會載入工作階段主機索引標籤。

### <a name="fslogix-client-version-2009"></a>FSLogix用戶端，版本 2009 

我們發行了新版本的 FSLogix 用戶端，其中包含許多修正項目和改良功能。 若要深入了解，請參閱[我們的部落格文章](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix)。

### <a name="rdp-shortpath-public-preview"></a>RDP Shortpath 公開預覽

RDP Shortpath 使用點對站和站對站 VPN 和 ExpressRoute，為您的 Windows 虛擬桌面工作階段主機導入直接連接功能， 同時也引進了 URCP 傳輸通訊協定。 RDP Shortpath 的設計是為了減少延遲和網路躍點，以改善使用者體驗。 若要深入了解，請參閱 [Windows 虛擬桌面 RDP Shortpath](shortpath.md)。

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization，版本 2.0.1

我們已發行 Windows 虛擬桌面 Cmdlet 的 2.0.1 版本。 此更新包含可讓您管理 MSIX 應用程式附加的 Cmdlet。 您可以在 [PowerShell 資源庫](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1)下載新版本。

### <a name="azure-advisor-updates"></a>Azure Advisor 更新

Azure Advisor 現在提供新的 Windows 虛擬桌面鄰近性指導建議，且在深度優先負載平衡主機集區中的效能最佳化方面也提供了新的建議。 若要深入了解，請參閱 [Azure 網站](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/)。

## <a name="october-2020"></a>2020 年 10 月

以下是 2020 年 10 月的變更：

### <a name="improved-performance"></a>提升效能

- 我們已藉由減少下列 Azure 地理位置中的連線延遲來最佳化效能：
    - 瑞士
    - 加拿大

您現在可以使用[體驗估算器](https://azure.microsoft.com/services/virtual-desktop/assessment/)，來估算這些區域中的使用者體驗品質。

### <a name="azure-government-cloud-availability"></a>Azure Government 雲端可用性

Azure Government 雲端現已正式推出。 若要深入了解，請參閱[我們的部落格文章](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/)。

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Windows 虛擬桌面 Azure 入口網站更新

我們對 Windows 虛擬桌面 Azure 入口網站進行了一些更新：

- 已修正導致使用者無法開啟 [工作階段] 索引標籤的 resourceID 錯誤。
- 已簡化 [工作階段主機] 索引標籤上的 UI。
- 已修正 RDP 屬性底下的 [預設值]、[可用性] 和 [還原預設值] 設定。
- 使所有索引標籤上的「移除」和「刪除」功能趨於一致。
- 現在，入口網站會驗證「新增應用程式」工作流程中的應用程式名稱。
- 已修正工作階段主機匯出資料在資料行中未對齊的問題。
- 已修正入口網站無法擷取使用者工作階段的問題。
- 已修正在不同資源群組中建立虛擬機器時發生的工作階段主機擷取問題。
- 已更新 [工作階段主機] 索引標籤，使其同時列出作用中和已中斷連線的工作階段。
- [應用程式] 索引標籤現已有頁面。
- 已修正在 [應用程式清單] 索引標籤中未正確顯示「需要命令列」文字的問題。
- 已修正入口網站在使用德文版的共用映像庫時無法部署主機集區或虛擬機器的問題。

### <a name="client-updates-for-october-2020"></a>2020 年 10 月的用戶端更新

我們發行了新版本的用戶端。 若要深入了解，請參閱下列文章：

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

如需其他用戶端的詳細資訊，請參閱[用戶端更新](#client-updates)。

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

- Azure CLI 現在支援 Windows 虛擬桌面 (`az desktopvirtualization`)，協助您將 Windows 虛擬桌面部署自動化。 如需擴充命令的清單，請參閱 [desktopvirtualization](/cli/azure/ext/desktopvirtualization/?view=azure-cli-latest&preserve-view=true)。

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

- 主機集區部署現在已與 [Azure 共用映像庫](../virtual-machines/shared-image-galleries.md)完全整合。 共用映像庫是個別的 Azure 服務，可儲存虛擬機器 (VM) 映像定義，包括映像版本控制。 您也可以使用全域複寫，將您的映像複製並傳送到其他 Azure 區域，以進行本機部署。

- 透過 PowerShell 或診斷服務 Web 應用程式完成的監視功能現在已移至 Azure 入口網站中的 Log Analytics。 您現在也有兩個選項可將報告視覺化。 您可以執行 Kusto 查詢，並使用活頁簿來建立視覺效果報告。

- 您不再需要完成 Azure Active Directory (Azure AD) 同意，即可使用 Windows 虛擬桌面。 在此更新中，Azure 訂用帳戶上的 Azure AD 租用戶會驗證您的使用者，並為您的管理員提供 Azure RBAC 控制項。

### <a name="powershell-support"></a>PowerShell 支援

我們已將新的 AzWvd Cmdlet 與此更新加入至 Azure PowerShell AZ 模組。 在 .NET Core 上執行的 PowerShell Core 中支援這個新模組。

若要安裝模組，請遵循[設定適用於 Windows 虛擬桌面的 PowerShell 模組](powershell-module.md)中的指示。

您也可以在 [AzWvd PowerShell 參考](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization&preserve-view=true)中查看可用命令的清單。

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