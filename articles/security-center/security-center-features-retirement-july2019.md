---
title: 淘汰資訊安全中心功能（2019年7月） |Microsoft Docs
description: 本文說明2019年7月31日淘汰的資訊安全中心功能。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 4a760fe1e5df7cf614a68f8fa8a05926326d3edf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80583227"
---
# <a name="retirement-of-security-center-features-july-2019"></a>淘汰資訊安全中心功能（2019年7月）

> [!NOTE]
> 本檔詳述從2019年7月31日 Azure 資訊安全中心淘汰的功能清單。
>
>

我們在2019年7月前六個月對 Azure 資訊安全中心進行了幾項[改進](https://azure.microsoft.com/updates/?product=security-center)。
有了這些改良的功能，我們從2019年7月31日資訊安全中心移除一些多餘的功能和相關的 Api。

這些已淘汰的功能大部分都可以取代為 Azure 資訊安全中心或 Azure Log Analytics 中的其他功能。 您可以使用[Azure Sentinel （預覽）](https://azure.microsoft.com/services/azure-sentinel/)來執行其他功能。

已淘汰的資訊安全中心功能包括：

- [事件儀表板](#menu_events)
- [搜尋功能表項目](#menu_search)
- [在身分識別和存取上查看傳統身分識別 & 存取連結（預覽）](#menu_classicidentity)
- [安全性警示地圖上的安全性事件對應按鈕（預覽）](#menu_securityeventsmap)
- [自訂警示規則（預覽）](#menu_customalerts)
- [[威脅防護] 安全性警示中的 [調查] 按鈕](#menu_investigate)
- [安全性解決方案的子集](#menu_solutions)
- [編輯安全性原則的安全性設定](#menu_securityconfigurations)
- [適用于 Log Analytics 工作區的安全性和審核儀表板（原本用於 OMS 入口網站）](#menu_securityomsdashboard)

本文提供每個已淘汰功能的詳細資訊，以及您可以採取來執行取代功能的步驟。

## <a name="events-dashboard"></a>事件儀表板<a name="menu_events"></a>

資訊安全中心會使用 Log Analytics 代理程式，從您的電腦收集各種安全性相關設定和事件。 它會將這些事件儲存在您的工作區中。 [[事件] 儀表板](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard)可讓您查看這項資料，並為您提供 Log Analytics 的進入點。

我們已淘汰當您選取工作區時所出現的 [事件] 儀表板：

![事件儀表板][2]

### <a name="events-dashboard---the-new-experience"></a>事件儀表板-新體驗

我們鼓勵您使用 Azure Log Analytics 的原生功能來查看您工作區上的值得注意事件。

如果您已在資訊安全中心中建立自訂值得注意的事件，則可加以存取。 在 Log Analytics 中，移至 [**選取工作區** > ] [**已儲存的搜尋**]。 您的資料不會遺失或修改。 您也可以從 Log Analytics 中的相同畫面取得原生值得注意的事件。

![工作區儲存的搜尋][3]

## <a name="search-menu-entry"></a>搜尋功能表項目<a name="menu_search"></a>

Azure 資訊安全中心目前使用 Azure 監視器記錄搜尋來抓取和分析您的安全性資料。 此畫面可作為 Log Analytics 搜尋頁面的視窗，並可讓使用者在其選取的工作區上執行搜尋查詢。 如需詳細資訊，請參閱[Azure 資訊安全中心搜尋](https://docs.microsoft.com/azure/security-center/security-center-search)。 我們已淘汰此搜尋視窗：

![Search page][4]

### <a name="search-menu-entry---the-new-experience"></a>搜尋功能表項目-新體驗

我們鼓勵您使用 Azure Log Analytics 原生功能，在您的工作區上執行搜尋查詢。 移至 Azure Log Analytics，然後選取 [**記錄**]。

![Log Analytics 記錄頁面][5]

## <a name="classic-identity--access-preview"></a>傳統身分識別 & 存取（預覽）<a name="menu_classicidentity"></a>

資訊安全中心中的傳統身分識別 & 存取體驗目前顯示 Log Analytics 中身分識別和存取訊號的儀表板。 若要查看此儀表板：

1. 選取 [ **View 傳統 Identity & 存取**]。

   ![身分識別頁面][6]

1. 查看 **& 存取儀表板**的身分識別。

    ![識別頁-工作區選取][7]

1. 選取工作區以在 Log Analytics 中開啟身分**識別 & 存取**儀表板，以查看您工作區上的身分識別和存取訊號。

   ![標識頁-儀表板][8]

我們已淘汰上述步驟中所示的三個畫面。 您的資料仍可在 Log Analytics 安全性解決方案中使用，且未修改或移除。

### <a name="classic-identity--access-preview---the-new-experience"></a>傳統身分識別 & 存取（預覽）-新體驗

Log Analytics 儀表板已在單一工作區上顯示深入解析。 不過，原生資訊安全中心功能可讓您看到所有訂用帳戶和所有與其相關聯的工作區。 您可以存取便於使用的觀點，讓您專注于根據其安全分數排序的建議事項。

在 Log Analytics 中的身分**識別 & 存取**儀表板的所有功能，都可以藉由選取資訊安全中心內的 **[身分識別 & 存取（預覽）** ] 來達成。

![識別頁-傳統體驗淘汰][9]

## <a name="security-events-map"></a>安全性事件對應<a name="menu_securityeventsmap"></a>

資訊安全中心會提供安全性警示對應，協助您識別安全性威脅。 該對應中的 [**移至安全性事件對應**] 按鈕會開啟儀表板，讓您在選取的工作區上查看原始安全性事件。

我們已移除 [**移至安全性事件] 地圖**按鈕和 [每個工作區] 儀表板。

![安全性警示對應-按鈕][10]

當您選取 [**前往安全性事件對應**] 按鈕時，它會開啟 [（現已淘汰）] 威脅情報儀表板。

![威脅情報儀表板][11]

當您選擇工作區來查看其威脅情報儀表板時，您會在 Log Analytics 中開啟 [（現在已淘汰）安全性警示對應（預覽）] 畫面。

![Log Analytics 中的安全性警示對應][12]

您現有的資料仍可在 Log Analytics 安全性解決方案中使用，且未修改或移除。

### <a name="security-events-map---the-new-experience"></a>安全性事件對應-新體驗

我們鼓勵您使用內建在資訊安全中心：**安全性警示對應（預覽）** 中的警示對應功能。 此功能可提供優化的體驗，並在所有訂用帳戶和相關聯的工作區中運作。 它可讓您在整個環境中擁有高階觀點，而不是專注于單一工作區。

## <a name="custom-alert-rules-preview"></a>自訂警示規則（預覽）<a name="menu_customalerts"></a>

我們已在2019年6月30日[淘汰自訂警示體驗](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)，因為其基礎結構已淘汰。 停用日期之後，就不會再產生自訂安全性警示。
我們建議您啟用[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) ，並在該處重新建立自訂警示。 或者，您可以使用 Azure 監視器記錄警示來建立警示。

若要使用 Azure Sentinel 建立自訂警示：

1. [開啟 Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) ，並選取您的自訂警示儲存所在的工作區
1. 從功能表選取 [**分析**]
1. 依照下列[教學](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats)課程中的指示，瞭解如何在 Azure Sentinel 中建立自訂警示

如果您不想使用 Azure Sentinel，您可以使用 Azure 監視器記錄警示來建立警示。 如需指示，請參閱[在 Azure 監視器中](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)使用 Azure 監視器和記錄警示 [來建立、查看和記錄管理警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)。

![自訂警示][13]

如需有關自訂警示淘汰的詳細資訊，請參閱[Azure 資訊安全中心（預覽）中的自訂警示規則](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)。

## <a name="security-alerts-investigation"></a>安全性警示調查<a name="menu_investigate"></a>

資訊安全中心中的調查功能可協助您將潛在的安全性事件分級。 此功能可讓您瞭解事件的範圍，並追蹤其根本原因。 我們已從資訊安全中心中移除這項功能，因為已將其取代為[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)中的改善體驗。

![安全性事件][14]

當您從 [**安全性事件**] 畫面選取 [**調查**] 按鈕時，您會在 Log Analytics 中開啟調查儀表板（預覽）。 我們已淘汰調查儀表板。

您現有的資料仍可在 Log Analytics 安全性解決方案中使用，且未修改或移除。

![Log Analytics 中的調查儀表板][15]

### <a name="investigation---the-new-experience"></a>調查-新體驗

我們鼓勵您轉換到[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) ，以取得豐富的調查體驗。 Azure Sentinel 提供強大的搜尋和查詢工具，以尋找整個組織資料來源的安全性威脅。

## <a name="subset-of-security-solutions"></a>安全性解決方案的子集<a name="menu_solutions"></a>

資訊安全中心可以[在 Azure 中啟用整合式安全性解決方案](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)。 我們已淘汰資訊安全中心的下列合作夥伴解決方案。 這些解決方案會在[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)中啟用，以及一些額外的資料來源。

- [新一代防火牆和 web 應用程式防火牆解決方案](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [整合支援通用事件格式（CEF）的安全性解決方案](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft 進階威脅分析](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

淘汰之後，您就無法從 UI 或 API 新增或修改前述清單中提及的任何解決方案類型。 Azure 資訊安全中心將不會再探索這些合作夥伴解決方案的任何新實例。

如果您有現有的已連線解決方案，我們鼓勵您移至 Azure Sentinel。

![安全性中心解決方案][16]

## <a name="edit-security-configurations-for-security-policies"></a>編輯安全性原則的安全性設定<a name="menu_securityconfigurations"></a>

Azure 資訊安全中心會套用一組[具有超過 150 個建議規則的集合](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) (英文)，以此方式監視安全性設定以強化作業系統。 這些規則與防火牆、審核、密碼原則等相關。 如果發現電腦設定有弱點，資訊安全中心會產生安全性建議。 [[編輯安全性](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config)設定] 畫面可讓客戶自訂資訊安全中心中的預設 OS 安全性設定。

我們已淘汰此預覽功能。 若要在停用日期之後將安全性設定重設回其預設值，請使用[下列指示](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/Reset%20security%20configurations%20customization)，透過 API 或 Powershell 來執行這項操作。

![編輯安全性設定][17]

### <a name="edit-security-configurations---the-new-experience"></a>編輯安全性設定-新體驗

我們想要啟用資訊安全中心以支援[來賓設定代理程式](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)。 這類更新可讓您擁有更豐富的功能集，包括支援更多作業系統，以及與來賓設定的 Azure 來賓原則整合。 啟用這些變更之後，您也能夠控制大規模的設定，並自動將它們套用至新的資源。

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>Log Analytics 工作區的安全性和審核儀表板<a name="menu_securityomsdashboard"></a>

[安全性和審核] 儀表板原本是在 OMS 入口網站中使用。 在 Log Analytics 中，儀表板會提供值得注意的安全性事件和威脅、威脅情報對應，以及儲存在工作區中的安全性事件的身分識別和存取評估的每個工作區。 我們已移除儀表板。 如同我們在儀表板 UI 中的建議，我們建議您轉換到 Azure 資訊安全中心。

![Log Analytics 安全性儀表板][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>安全性與審核儀表板-新體驗

我們建議您切換到 Azure 資訊安全中心。 它在多個訂用帳戶及其相關聯的工作區之間提供相同的安全性總覽，再加上更豐富的功能集。

您可以取得原始的 Log Analytics 查詢，以填入[GitHub 存放庫](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards)中資訊安全中心的 [安全性和 audit] 儀表板。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/)。
- 深入瞭解[Azure Sentinel](https://docs.microsoft.com/azure/sentinel)。

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
