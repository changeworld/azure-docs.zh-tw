---
title: 安全中心停用功能 (2019 年 7 月) |微軟文件
description: 本文介紹了安全中心在 2019 年 7 月 31 日停用的功能。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 1f923db5c730f6dec15f4fc211ab7acbd009ac7f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521425"
---
# <a name="retirement-of-security-center-features-july-2019"></a>安全中心停用功能(2019年7月)

> [!NOTE]
> 本文檔詳細介紹了 2019 年 7 月 31 日從 Azure 安全中心停用的功能清單。
>
>

在 2019 年 7 月的六個月中,我們對 Azure 安全中心進行了多項[改進](https://azure.microsoft.com/updates/?product=security-center)。
通過這些改進的功能,我們於 2019 年 7 月 31 日從安全中心刪除了一些冗餘功能和相關 API。

這些停用功能中的大多數都可以替換為 Azure 安全中心或 Azure 日誌分析中的其他功能。 可以使用[Azure Sentinel(預覽)](https://azure.microsoft.com/services/azure-sentinel/)實現其他功能。

已停用的安全中心功能包括:

- [事件儀表板](#menu_events)
- [搜尋選單項目](#menu_search)
- [檢視「身份&存取上的經典身份連結(預覽)](#menu_classicidentity)
- [安全警示地圖上的安全事件對應按鈕(預覽)](#menu_securityeventsmap)
- [自訂警示規則 (預覽)](#menu_customalerts)
- [調查威脅保護安全警報中的按鈕](#menu_investigate)
- [安全解決方案的子集](#menu_solutions)
- [編輯安全原則的安全設定](#menu_securityconfigurations)
- [紀錄分析工作區的安全和審核儀表板(最初用於 OMS 門戶)](#menu_securityomsdashboard)

本文提供了每個停用功能的詳細資訊,以及實現替換功能可以採取的步驟。

## <a name="events-dashboard"></a>事件儀表板<a name="menu_events"></a>

安全中心使用日誌分析代理從您的電腦收集各種與安全相關的配置和事件。 它將這些事件存儲在工作區中。 事件[儀表板](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard)允許您查看此數據,並為您提供日誌分析的入口點。

我們停用了您選擇工作區時顯示的事件儀表板:

![事件儀表板][2]

### <a name="events-dashboard---the-new-experience"></a>事件儀表板 - 新體驗

我們鼓勵您使用 Azure 日誌分析的本機功能來查看工作區上的顯著事件。

如果您在安全中心中創建了自定義值得注意的事件,則可以訪問這些事件。 在紀錄分析中,轉到**選擇工作區** > **儲存的搜尋**。 您的資料不會丟失或修改。 日誌分析中的同一螢幕也提供了本機值得注意的事件。

![工作區儲存的搜尋][3]

## <a name="search-menu-entry"></a>搜尋選單項目<a name="menu_search"></a>

Azure 安全中心當前使用 Azure 監視器日誌搜索來檢索和分析安全數據。 此螢幕用作日誌分析搜索頁的視窗,並使用戶能夠在其選定的工作區上運行搜索查詢。 有關詳細資訊,請參閱[Azure 安全中心搜尋](https://docs.microsoft.com/azure/security-center/security-center-search)。 我們停用了此搜尋視窗:

![Search page][4]

### <a name="search-menu-entry---the-new-experience"></a>搜尋選單項目 - 新體驗

我們鼓勵您使用 Azure 日誌分析本機功能在工作區上執行搜尋查詢。 跳到 Azure 紀錄分析並選擇 **「日誌**」。。

![紀錄分析紀錄頁面][5]

## <a name="classic-identity--access-preview"></a>經典身份&存取(預覽)<a name="menu_classicidentity"></a>

安全中心中的經典標識&訪問體驗當前在日誌分析中顯示標識和訪問資訊的儀錶板。 要檢視此儀表板:

1. 選擇 **「檢視經典身份&」 訪問**。

   ![識別頁][6]

1. 檢視**識別& 存取儀表板**。

    ![識別頁 - 工作區選擇][7]

1. 選擇工作區以在日誌分析中打開 **「標識&訪問**」儀錶板,以查看工作區上的標識和訪問資訊。

   ![識別頁 - 儀表板][8]

我們停用了前面步驟中顯示的所有三個螢幕。 您的數據在日誌分析安全解決方案中仍然可用,並且未修改或刪除。

### <a name="classic-identity--access-preview---the-new-experience"></a>經典身份&存取(預覽) - 新體驗

日誌分析儀錶板在單個工作區上顯示了見解。 但是,本機安全中心功能提供對所有訂閱及其關聯的所有工作區的可見性。 您可以造訪易於使用的檢視,該檢視可讓您關注根據安全分數排列的建議的重要性。

通過選擇安全中心內的**標識&訪問(預覽),** 可以訪問日誌分析中的**標識&訪問**儀錶板的所有功能。

![身份頁面 - 經典體驗退休][9]

## <a name="security-events-map"></a>安全事件對應<a name="menu_securityeventsmap"></a>

安全中心為您提供[安全警報映射](https://docs.microsoft.com/azure/security-center/security-center-threat-intel),以幫助識別安全威脅。 **該地圖中的「轉到安全事件映射」** 按鈕將打開一個儀表板,允許您查看所選工作區上的原始安全事件。

我們刪除了 **「轉到安全事件映射」** 按鈕和每個工作區儀錶板。

![安全警示映射 - 按鈕][10]

當您選擇「**轉到安全事件映射」** 按鈕時,它將打開(現已停用)威脅情報儀錶板。

![威脅情報儀表板][11]

當您選擇工作區以查看其威脅智慧儀錶板時,您將在日誌分析中打開(現已停用)安全警報映射(預覽)螢幕。

![紀錄分析中的安全警報對應][12]

您的現有數據在日誌分析安全解決方案中仍然可用,並且未修改或刪除。

### <a name="security-events-map---the-new-experience"></a>安全事件映射 - 新體驗

我們鼓勵您使用安全中心內置的警報映射功能:**安全警報映射(預覽)。** 此功能提供優化的體驗,適用於所有訂閱和關聯的工作區。 它為您提供整個環境的高級檢視,並且不專注於單個工作區。

## <a name="custom-alert-rules-preview"></a>自訂警示規則 (預覽)<a name="menu_customalerts"></a>

我們於 2019 年 6 月 30 日[停用了自定義警報體驗](https://docs.microsoft.com/azure/security-center/security-center-custom-alert),因為它的基礎基礎結構已停用。 停用日期后,將不再生成自定義安全警報。
我們建議您啟用 Azure [Sentinel](https://azure.microsoft.com/services/azure-sentinel/)並在那裡重新建立自訂警報。 或者,您可以使用 Azure 監視器日誌警報創建警報。

要使用 Azure 哨兵建立自訂警報,請執行以下準備:

1. [開啟 Azure 哨兵](https://portal.azure.com/#create/Microsoft.ASI/preview)並選擇自訂警示的儲存工作區
1. 從選單中選擇**分析**
1. 依以下[教學](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats)中有關如何在 Azure Sentinel 建立自訂警報的說明進行操作

如果您對使用 Azure Sentinel 不感興趣,則可以使用 Azure 監視器日誌警報創建警報。 有關說明,請參閱使用 Azure 監視器 中的 Azure 監視器和[紀錄警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) [建立、檢視和管理紀錄警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)。

![自訂警示][13]

有關自訂警報停用的詳細資訊,請參閱 Azure[安全中心(預覽)中的自訂警報規則](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)。

## <a name="security-alerts-investigation"></a>安全警報調查<a name="menu_investigate"></a>

安全中心[中的"調查"功能](https://docs.microsoft.com/azure/security-center/security-center-investigation)可説明您對潛在的安全事件進行會審。 該功能允許您瞭解事件的範圍並跟蹤其根本原因。 我們從安全中心刪除了此功能,因為它已被[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)中改進的體驗所取代。

![安全性事件][14]

從**安全事件**螢幕中選擇「**調查**」按鈕時,請在日誌分析中打開調查儀錶板(預覽)。 我們停用了調查儀錶板。

您的現有數據在日誌分析安全解決方案中仍然可用,並且未修改或刪除。

![紀錄分析中的調查儀表板][15]

### <a name="investigation---the-new-experience"></a>調查 - 新體驗

我們鼓勵您過渡到 Azure [Sentinel,](https://azure.microsoft.com/services/azure-sentinel/)獲得豐富的調查體驗。 Azure Sentinel 提供了強大的搜索和查詢工具,用於查找組織數據源的安全威脅。

## <a name="subset-of-security-solutions"></a>安全解決方案子集<a name="menu_solutions"></a>

安全中心可以在[Azure 中啟用整合的安全解決方案](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)。 我們從安全中心停用了以下合作夥伴解決方案。 這些解決方案在[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)中以及許多其他數據源中啟用。

- [下一代防火牆和 Web 應用程式防火牆解決方案](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [整合支援通用事件格式 (CEF) 的安全解決方案](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD 識別保護](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

停用後,不能從 UI 或 API 添加或修改前面清單中提及的任何解決方案類型。 Azure 安全中心將不再發現這些合作夥伴解決方案的任何新實例。

如果您有現有的連接解決方案,我們鼓勵您遷移到 Azure Sentinel。

![安全中心解決方案][16]

## <a name="edit-security-configurations-for-security-policies"></a>編輯安全原則的安全設定<a name="menu_securityconfigurations"></a>

Azure 資訊安全中心會套用一組[具有超過 150 個建議規則的集合](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) (英文)，以此方式監視安全性設定以強化作業系統。 這些規則涉及防火牆、審核、密碼策略等。 如果發現電腦設定有弱點，資訊安全中心會產生安全性建議。 [「編輯安全設定」螢幕](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config)允許客戶自定義安全中心的預設作業系統安全配置。

我們停用了此預覽功能。 要在停用日期后將安全配置重置回其預設值,請使用[以下說明](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/Reset%20security%20configurations%20customization)通過 API 或 Powershell 執行此操作。

![編輯安全性設定][17]

### <a name="edit-security-configurations---the-new-experience"></a>編輯安全性設定 - 新體驗

我們打算啟用安全中心來支援[來賓設定代理](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)。 此類更新將允許更豐富的功能集,包括支援更多操作系統和集成來賓配置的 Azure 來賓策略。 啟用這些更改後,您還可以大規模控制配置並自動將其應用於新資源。

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>紀錄分析工作區的安全和審核儀表板<a name="menu_securityomsdashboard"></a>

安全和審核儀錶板最初用於OMS門戶。 在日誌分析中,儀錶板提供每個工作區顯著安全事件和威脅的概述、威脅智慧映射以及保存在工作區中的安全事件的標識和訪問評估。 我們刪除了儀錶板。 正如我們已在儀錶板 UI 中建議的,我們建議您過渡到 Azure 安全中心。

![日誌分析安全儀表板][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>安全與稽核儀表板 - 新體驗

我們建議您切換到 Azure 安全中心。 它跨多個訂閱及其關聯的工作區以及更豐富的功能集提供相同的安全概述。

您可以在安全中心的[GitHub 儲存庫](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards)中獲取填充安全和審核儀錶板的原始日誌分析查詢。

## <a name="next-steps"></a>後續步驟

- 瞭解有關[Azure 安全中心](https://docs.microsoft.com/azure/security-center/)的更多詳細資訊。
- 瞭解有關[Azure 哨兵](https://docs.microsoft.com/azure/sentinel)的更多內容。

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
