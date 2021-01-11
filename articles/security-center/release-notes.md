---
title: Azure 資訊安全中心版本資訊
description: Azure 資訊安全中心新增項目和變更內容的相關描述。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2021
ms.author: memildin
ms.openlocfilehash: 4e4ee8690822c6caf4a7c5bd69387ea00d6d00a8
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955445"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 資訊安全中心的新功能

資訊安全中心持續再開發改良。 為了讓您隨時掌握最新的開發訊息，本頁面會提供有關新功能、錯誤 (bug) 修正和已淘汰功能的資訊。

此頁面會經常更新，因此請時常瀏覽。 

若要了解即將推出的 *已規劃* 資訊安全中心變更，請參閱 [未來 Azure 資訊安全中心的重要變更](upcoming-changes.md)。 

> [!TIP]
> 如果想要尋找超過 6 個月的項目，請前往[Azure 資訊安全中心內新功能的封存](release-notes-archive.md)。


## <a name="january-2021"></a>2021 年 1 月

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-generally-available"></a>內部部署和多雲端電腦的弱點評量已正式推出

我們在十月宣佈了一個預覽版，該版本使用 [適用於伺服器的 Azure Defender](defender-for-servers-introduction.md) 整合式弱點評量掃描器 (由 Qualys 提供技術支援)，來掃描已啟用 Azure Arc 的伺服器。

現已正式推出。 

當您在非 Azure 機器上啟用 Azure Arc 時，資訊安全中心將可供手動且大規模地部署整合式弱點掃描器。

透過這項更新，您可以充分發揮 **適用於伺服器的 Azure Defender** 功效，以整合所有 Azure 和非 Azure 資產的弱點管理程式。

主要功能：

- 監視 Azure Arc 機器上的 VA (弱點評量) 掃描器佈建狀態
- 將整合式 VA 代理程式佈建到未受保護的 Windows 和 Linux Azure Arc 機器 (手動方式且大規模)
- 從已部署的代理程式接收和分析偵測到的弱點 (手動方式且大規模)
- 適用於 Azure VM 和 Azure Arc 機器的整合體驗

[深入了解如何將整合式弱點掃描器部署到混合式機器](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)。

[深入了解已啟用 Azure Arc 的伺服器](../azure-arc/servers/index.yml)。


## <a name="december-2020"></a>2020 年 12 月

12 月的更新包括：

- [適用於機器上的 SQL 伺服器的 Azure Defender 已正式發行](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Synapse Analytics 專用 SQL 集區的「適用於 SQL 的 Azure Defender」支援已正式發行](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [全域系統管理員現在可以自我授與租用戶層級權限](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [兩個新的 Azure Defender 方案：適用於 DNS 的 Azure Defender 和適用於 Resource Manager 的 Azure Defender (預覽)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Azure 入口網站中的新安全性警示頁面 (預譼)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Azure SQL Database 與 SQL 受控執行個體中全新的資訊安全中心體驗](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [更新的資產清查工具和篩選器](#asset-inventory-tools-and-filters-updated)
- [與要求 SSL 憑證的 Web 應用程式有關的建議不再屬於安全分數](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [建議頁面具有環境、嚴重性和可用回應的新篩選器](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [連續匯出有新的資料類型及改良的 deployifnotexist 原則](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>適用於機器上的 SQL 伺服器的 Azure Defender 已正式發行

Azure 資訊安全中心提供兩個適用於 SQL 伺服器的 Azure Defender 方案：

- **適用於 Azure SQL 資料庫伺服器的 Azure Defender** - 保護您的 Azure 原生 SQL 伺服器 
- **適用於機器上的 SQL 伺服器的 Azure Defender** - 將相同的保護擴充至混合式、多重雲端和內部部署環境中的 SQL 伺服器

根據此公告，**適用於 SQL 的 Azure Defender** 現已可保護您的資料庫及其資料，無論其位於何處。

適用於 SQL 的 Azure Defender 包含弱點評量功能。 弱點評量工具組含下列進階功能：

- **基準設定** (新功能)，可以智慧方式精簡弱點掃描的結果，使其更有可能呈現實際的安全性問題。 建立基準安全性狀態後，弱點評量工具只會報告與該基準狀態的偏差。 在後續的掃描中，會將符合基準的結果視為通過。 這可讓您和分析師專注於真正的重要事項。
- **詳細的基準測試資訊** 可協助您 *了解* 探索到的結果，及其與您的資源有關的原因。
- **補救指令碼** 可協助您降低已識別的風險。

深入了解[適用於 SQL 的 Azure Defender](defender-for-sql-introduction.md)。


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Synapse Analytics 專用 SQL 集區的「適用於 SQL 的 Azure Defender」支援已正式發行

Azure Synapse Analytics (先前為 SQL DW) 是一項結合企業資料倉儲和巨量資料分析的分析服務。 專用 SQL 集區是 Azure Synapse 的企業資料倉儲功能。 深入了解[什麼是 Azure Synapse Analytics (先前為 SQL DW)？](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)。

適用於 SQL 的 Azure Defender 可使用下列功能來保護您的專用 SQL 集區：

- **進階威脅防護**，可偵測威脅和攻擊 
- **弱點評量功能**，可識別及補救安全性設定錯誤

適用於 SQL 的 Azure Defender 對 Azure Synapse Analytics SQL 集區的支援會自動新增至 Azure 資訊安全中心的 Azure SQL 資料庫組合。 您可以在 Azure 入口網站的 Synapse 工作區頁面中找到新的 [適用於 SQL 的 Azure Defender] 索引標籤。

深入了解[適用於 SQL 的 Azure Defender](defender-for-sql-introduction.md)。


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>全域系統管理員現在可以自我授與租用戶層級權限

具有 **全域管理員** 之 Azure Active Directory 角色的使用者，可能會擁有整個租用戶的權限，但缺少 Azure 權限，無法在 Azure 資訊安全中心中查看整個組織的資訊。 

若要自我指派的租用戶層級權限，請遵循[自我授權租用戶範圍權限](security-center-management-groups.md#grant-tenant-wide-permissions-to-yourself)中的指示。


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>兩個新的 Azure Defender 方案：適用於 DNS 的 Azure Defender 和適用於 Resource Manager 的 Azure Defender (預覽)

我們為 Azure 環境新增了兩個新的雲端原生廣度威脅防護功能。

這些新的防護功能可大幅提升抵禦威脅執行者攻擊的復原能力，並顯著增加了 Azure Defender 保護的 Azure 資源數目。

- **適用於 Resource Manager 的 Azure Defender** - 會自動監視組織中執行的所有資源管理作業。 如需詳細資訊，請參閱：
    - [適用於 Resource Manager 的 Azure Defender 簡介](defender-for-resource-manager-introduction.md)
    - [回應適用於 Resource Manager 的 Azure Defender 警示](defender-for-resource-manager-usage.md)
    - [適用於 Resource Manager 的 Azure Defender 所提供的警示清單](alerts-reference.md#alerts-resourcemanager)

- **適用於 DNS 的 Azure Defender** - 持續監視來自 Azure 資源的所有 DNS 查詢。 如需詳細資訊，請參閱：
    - [適用於 DNS 的 Azure Defender 簡介](defender-for-dns-introduction.md)
    - [回應適用於 DNS 的 Azure Defender 警示](defender-for-dns-usage.md)
    - [適用於 DNS 的 Azure Defender 所提供的警示清單](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Azure 入口網站中的新安全性警示頁面 (預覽)

Azure 資訊安全中心的安全性警示頁面已經過重新設計，可提供：

- **改善的警示分級體驗** - 協助減少警示疲乏，並更容易專注於最相關的威脅，此清單包含可自訂的篩選條件和群組選項
- **警示清單中的詳細資訊** - 例如 MITRE ATT & ACK 策略
- **建立範例警示的按鈕** - 若要評估 Azure Defender 功能並測試您的警示組態 (針對 SIEM 整合、電子郵件通知和工作流程自動化)，您可以從所有 Azure Defender 方案建立範例警示
- **配合 Azure Sentinel 的事件體驗** - 針對同時使用這兩種產品的客戶，在兩者之間切換目前是比較直接的體驗，而且很容易彼此相互學習
- **更好的效能**，適用於大型警示清單
- **鍵盤導覽**，導覽整個警示清單
- **來自 Azure Resource Graph 的警示** - 您可以在 Azure Resource Graph 中查詢警示，類似於 Kusto 的 API，適用於所有資源。 如果您要建立自己的警示儀表板，這個方法也很有用。 [深入了解 Azure 資源群組](../governance/resource-graph/index.yml)。

若要存取新的體驗，請使用安全性警示頁面頂端橫幅中的 [立即試用] 連結。

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="附有新預覽警示體驗連結的橫幅":::

若要從新的警示體驗建立範例警示，請參閱[產生範例 Azure Defender 警示](security-center-alert-validation.md#generate-sample-azure-defender-alerts)。


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Azure SQL Database 與 SQL 受控執行個體中全新的資訊安全中心體驗 

SQL 內的資訊安全中心體驗可讓您存取下列資訊安全中心和適用於 SQL 的 Azure Defender 功能：

- **安全性建議** – 資訊安全中心會定期分析所有連線 Azure 資源的安全性狀態，以找出潛在的安全性錯誤設定。 接著，會提供如何補救這些弱點以及改善組織安全性狀態的建議。
- **安全性警示** - 這是一項偵測服務，會持續監視 Azure SQL 活動是否有威脅，例如 SQL 插入式攻擊、暴力密碼破解攻擊和權限濫用。 這項服務會在資訊安全中心觸發詳細和動作導向的安全性警示，並提供使用 Azure Sentinel (Microsoft 的 Azure 原生 SIEM 解決方案) 繼續調查的選項。
- **結果** – 一項弱點評量服務，會持續監視 Azure SQL 設定並協助補救弱點。 評量掃描會提供 Azure SQL 安全性狀態的概觀，以及詳細的安全性結果。     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Azure 資訊安全中心的 SQL 安全性功能可從 Azure SQL 中取得":::


### <a name="asset-inventory-tools-and-filters-updated"></a>更新的資產清查工具和篩選器

Azure 資訊安全中心的清查頁面已使用下列變更進行更新：

- 在工具列中新增了 **指南和意見反應**。 這會開啟一個窗格，其中包含相關資訊和工具的連結。 
- 在資源可用的預設篩選器中新增了 **訂用帳戶篩選器**。
- **開啟查詢** 連結可開啟目前的篩選選項作為 Azure Resource Graph 查詢 (先前稱為「Resource Graph Explorer 中的檢視」)。
- 每個篩選器的 **運算子選項**。 現在您可以選擇 '=' 以外的其他邏輯運算子。 例如，您可以尋找所有具有作用中建議，且其標題包含「加密」字串的資源。 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="資產清查篩選中的運算子選項具有控制項":::

如需清查的詳細資訊，請參閱[利用資產清查來探索和管理您的資源](asset-inventory.md)。


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>與要求 SSL 憑證的 Web 應用程式有關的建議不再屬於安全分數

「Web 應用程式對於所有傳入要求均應要求 SSL 憑證」建議已從安全性控制 **管理存取權和權限** (最多值 4 分) 移至 **實作安全性最佳做法** (沒有分數)。 

確定您的 Web 應用程式會要求憑證，必定可提高其安全性。 但對於不對外公開的 Web 應用程式，這一點則無關緊要。 如果您透過 HTTP 存取您的網站，而非 HTTPS，將不會收到任何用戶端憑證。 因此如果您的應用程式需要用戶端憑證，請勿允許透過 HTTP 傳入您應用程式的要求。 深入了解如何[為 Azure App Service 設定 TLS 相互驗證](../app-service/app-service-web-configure-tls-mutual-auth.md)。

經此變更後，現在的建議只是建議的最佳做法，而不會影響您的分數。 

在[安全性控制項及其建議](secure-score-security-controls.md#security-controls-and-their-recommendations)中了解各個安全性控制中有哪些建議。


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>建議頁面具有環境、嚴重性和可用回應的新篩選器

Azure 資訊安全中心會監視所有連線的資源，並產生安全性建議。 請使用這些建議來強化您的混合式雲端狀態，並追蹤是否符合與您的組織、產業和國家/地區相關的原則和標準。

隨著資訊安全中心持續擴充其涵蓋範圍和功能，安全性建議清單也逐月增長。 如需範例，請參閱[已新增 29 個預覽建議，以增加 Azure 安全性效能評定的涵蓋範圍](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)。

隨著清單不斷增長，我們必須能夠篩選出最重要的建議。 在 11 月，我們在建議頁面中新增了篩選器 (請參閱[建議清單現在包含篩選器](#recommendations-list-now-includes-filters))。

本月份新增的篩選準則提供了根據下列項目來精簡建議清單的選項：

- **環境** - 檢視您的 AWS、GCP 或 Azure 資源 (或任何組合) 的建議
- **嚴重性** - 根據資訊安全中心所設定的嚴重性分類來檢視建議
- **回應動作** - 根據資訊安全中心回應選項的可用性來檢視建議：快速修正、拒絕和強制執行

    > [!TIP]
    > 回應動作篩選器取代了 **可用快速修正 (是/否)** 篩選器。 
    > 
    > 深入了解以下各個回應選項：
    > - [快速修正補救](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [防止強制/拒絕建議的設定錯誤](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="依安全性控制分組的建議" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>連續匯出有新的資料類型及改良的 deployifnotexist 原則

Azure 資訊安全中心的連續匯出工具可讓您匯出資訊安全中心的建議和警示，以在您的環境中與其他監視工具搭配使用。

「連續匯出」可讓您完整自訂要匯出的項目及其目的地。 如需完整詳細資訊，請參閱[持續匯出資訊安全中心資料](continuous-export.md)。

這些工具已透過下列方式進行強化和擴充：

- **已強化連續匯出的 deployifnotexist 原則**。 現在的原則可以：

    - **檢查設定是否已啟用。** 若未啟用，原則會顯示為不符合規範，並建立符合規範的資源。 在[設定連續匯出](continuous-export.md#set-up-a-continuous-export)中，深入了解「使用 Azure 原則索引標籤進行大規模部署」中提供的 Azure 原則範本。

    - **支援匯出安全性結果。** 使用 Azure 原則範本時，您可以將連續匯出設定為包含結果。 這在匯出含有「子」建議的建議時有其重要性，例如弱點評量掃描器的結果，或「父系」建議「您應在機器上安裝系統更新」的特定系統更新結果。
    
    - **支援匯出安全分數資料。**

- **已新增法規合規性評量資料 (處於預覽階段)。** 現在，您可以持續將法規合規性評量的更新 (包括任何自訂計畫的更新) 匯出至 Log Analytics 工作區或事件中樞。 這項功能無法在國家/主權雲端上使用。

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="將法規合規性評量資訊包含在連續匯出資料中的選項。":::


## <a name="november-2020"></a>2020 年 11 月

11 月的更新包括：

- [已新增 29 個預覽建議，以增加 Azure 安全性效能評定的涵蓋範圍](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 已新增至資訊安全中心的法規合規性儀表板](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [建議清單現在包含篩選器](#recommendations-list-now-includes-filters)
- [自動佈建體驗已改善並擴充](#auto-provisioning-experience-improved-and-expanded)
- [連續匯出中現在可以使用安全分數 (預覽)](#secure-score-is-now-available-in-continuous-export-preview)
- [「您應在機器上安裝系統更新」建議現在包含子建議](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations)
- [Azure 入口網站中的原則管理頁面現在會顯示預設原則指派的狀態](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>已新增 29 個預覽建議，以增加 Azure 安全性效能評定的涵蓋範圍

Azure 安全性效能評定是 Microsoft 針對以通用合規性架構為基礎的安全性和合規性最佳做法所撰寫的一組 Azure 特定指導方針。 [深入了解 Azure 安全性效能評定](../security/benchmarks/introduction.md)。

下列 29 個預覽建議已新增到 Azure 資訊安全中心，以增加 Azure 安全性效能評定的涵蓋範圍。

預覽建議不會將資源轉譯為「狀況不良」，這些項目也不會納入您安全分數的計算範圍。 您可以盡可能加以補救，以在預覽期間結束時計入您的分數。 若要了解如何回應這些建議，請參閱[Azure 資訊安全中心的補救建議](security-center-remediate-recommendations.md)。

| 安全性控制                     | 新建議                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 加密傳輸中的資料              | - 應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]<br>- 應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]<br>- API 應用程式的 TLS 應該更新為最新版本<br>- 函式應用程式的 TLS 應該更新為最新版本<br>- Web 應用程式的 TLS 應該更新為最新版本<br>- 您的 API 應用程式應要求使用 FTPS<br>- 您的函式應用程式應要求使用 FTPS<br>- Web 應用程式中應該要求 FTPS                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 管理存取權和權限        | - Web 應用程式應該要求所有要求提供 SSL 憑證<br>- API 應用程式應該使用受控識別<br>- 函式應用程式應該使用受控識別<br>- Web 應用程式應該使用受控識別                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 限制未經授權的網路存取 | - PostgreSQL 伺服器應啟用私人端點<br>- MariaDB 伺服器應啟用私人端點<br>- MySQL 伺服器應啟用私人端點                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 啟用稽核和記錄          | - 應啟用 App Service 中的診斷記錄                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| 實作安全性最佳做法    | - 應該為虛擬機器啟用 Azure 備份<br>- 應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份<br>- 應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份<br>- 應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份<br>- API 應用程式的 PHP 應該更新為最新版本<br>- Web 應用程式的 PHP 應該更新為最新版本<br>- API 應用程式的 Java 應該更新為最新版本<br>- 函式應用程式的 Java 應該更新為最新版本<br>- Web 應用程式的 Java 應該更新為最新版本<br>- API 應用程式的 Python 應該更新為最新版本<br>- 函式應用程式的 Python 應該更新為最新版本<br>- Web 應用程式的 Python 應該更新為最新版本<br>- SQL 伺服器的稽核保留期應該至少設為 90 天 |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

相關連結：

- [深入了解 Azure 安全性效能評定](../security/benchmarks/introduction.md)
- [深入了解 Azure API 應用程式](../app-service/app-service-web-tutorial-rest-api.md)
- [深入了解 Azure 函式應用程式](../azure-functions/functions-overview.md)
- [深入了解 Azure Web 應用程式](../app-service/overview.md)
- [深入了解適用於 MariaDB 的 Azure 資料庫](../mariadb/overview.md)
- [深入了解適用於 MySQL 的 Azure 資料庫](../mysql/overview.md)
- [深入了解適用於 PostgreSQL 的 Azure 資料庫](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 已新增至資訊安全中心的法規合規性儀表板

NIST SP 800-171 R2 標準版現已提供內建方案，可與 Azure 資訊安全中心的法規合規性儀表板搭配使用。 如需這些控制項的對應，請參閱 [NIST SP 800-171 R2 法規遵循內建方案的詳細資料](../governance/policy/samples/nist-sp-800-171-r2.md)。 

若要將標準套用至您的訂用帳戶，並持續監視合規性狀態，請使用[自訂合規性儀表板中的一組標準](update-regulatory-compliance-packages.md)中的指示。

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="資訊安全中心的法規合規性儀表板中的 NIST SP 800 171 R2 標準版":::

如需此合規性標準的詳細資訊，請參閱 [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)。


### <a name="recommendations-list-now-includes-filters"></a>建議清單現在包含篩選器

您現在可以根據準則範圍來篩選安全性建議清單。 在下列範例中，已篩選建議清單以顯示下列建議：

- **正式推出** (亦即非預覽)
- 用於 **儲存體帳戶**
- 支援 **快速修正** 補救

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="建議清單的篩選器":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>自動佈建體驗已改善並擴充

自動佈建功能會透過在新的和現有的 Azure VM 上安裝必要的擴充功能，以協助降低管理負荷，以便其可從資訊安全中心的保護中獲益。 

隨著 Azure 資訊安全中心成長，已開發更多擴充功能，而資訊安全中心可以監視更大的資源類型清單。 自動佈建工具現在已進行擴充，以利用 Azure 原則的功能來支援額外的擴充功能和資源類型。

您現在可以設定的自動佈建：

- Log Analytics 代理程式
- (新增) Kubernetes 的 Azure 原則附加元件
- (新增) Microsoft Dependency Agent

如需深入了解，請[從 Azure 資訊安全中心自動佈建代理程式和擴充功能](security-center-enable-data-collection.md)。


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>連續匯出中現在可以使用安全分數 (預覽)

在連續匯出安全分數時，您可以即時地將分數的變更串流至 Azure 事件中樞或 Log Analytics 工作區。 使用此功能可以：

- 使用動態報告追蹤一段時間的安全分數
- 將安全分數資料匯出至 Azure Sentinel (或任何其他 SIEM)
- 將此資料與您可能已用來監視組織中安全分數的任何程序進行整合

深入了解如何[連續匯出資訊安全中心資料](continuous-export.md)。


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations"></a>「您應在機器上安裝系統更新」建議現在包含子建議

**您應在機器上安裝系統更新** 建議已有所增強。 新版本會包含每個遺漏更新的子建議，並提供下列改良功能：

- Azure 入口網站的 Azure 資訊安全中心頁面會提供經過重新設計的體驗。 **您應在機器上安裝系統更新** 的建議詳細資料包含結果清單，如下所示。 當您選取單一結果時，詳細資料窗格隨即開啟，其中含有補救資訊的連結和受影響的資源清單。

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="在入口網站體驗中開啟其中一個子建議，以取得更新的建議":::

- Azure Resource Graph (ARG) 的建議有更豐富的資料。 ARG 是專門設計來提供有效率資源探索的 Azure 服務。 您可以使用 ARG 在一組指定的訂用帳戶中大規模查詢，以便有效率地控管您的環境。 

    對於 Azure 資訊安全中心，您可以使用 ARG 和 [Kusto 查詢語言 (KQL)](/azure/data-explorer/kusto/query/) 來查詢各種安全性狀態資料。

    先前如果您在 ARG 中查詢此建議，唯一可用的資訊就是必須在機器上補救建議。 增強版本的下列查詢則會傳回每個遺漏的系統更新，並依機器分組。

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Azure 入口網站中的原則管理頁面現在會顯示預設原則指派的狀態

您現在可以在 Azure 入口網站資訊安全中心的 [安全性原則] 頁面中，看到您的訂用帳戶是否已獲指派預設資訊安全中心原則。

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="顯示預設原則指派的 Azure 資訊安全中心原則管理頁面":::

## <a name="october-2020"></a>2020 年 10 月

10 月的更新包括：
- [內部部署和多雲端電腦的弱點評量 (預覽)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [已新增 Azure 防火牆建議 (預覽)](#azure-firewall-recommendation-added-preview)
- [已使用快速修正更新「Kubernetes Service 上應定義授權 IP 範圍」建議](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [法規合規性儀表板現在會包含用來移除標準的選項](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [已從 Azure Resource Graph (ARG) 中移除 Microsoft.Security/securityStatuses 資料表](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>內部部署和多雲端電腦的弱點評量 (預覽)

[適用於伺服器的 Azure Defender](defender-for-servers-introduction.md) 整合式弱點評量掃描器 (由 Qualys 提供技術支援) 現在會掃描已啟用 Azure Arc 的伺服器。

當您在非 Azure 機器上啟用 Azure Arc 時，資訊安全中心將可供手動且大規模地部署整合式弱點掃描器。

透過這項更新，您可以充分發揮 **適用於伺服器的 Azure Defender** 功效，以整合所有 Azure 和非 Azure 資產的弱點管理程式。

主要功能：

- 監視 Azure Arc 機器上的 VA (弱點評量) 掃描器佈建狀態
- 將整合式 VA 代理程式佈建到未受保護的 Windows 和 Linux Azure Arc 機器 (手動方式且大規模)
- 從已部署的代理程式接收和分析偵測到的弱點 (手動方式且大規模)
- 適用於 Azure VM 和 Azure Arc 機器的整合體驗

[深入了解如何將整合式弱點掃描器部署到混合式機器](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)。

[深入了解已啟用 Azure Arc 的伺服器](../azure-arc/servers/index.yml)。


### <a name="azure-firewall-recommendation-added-preview"></a>已新增 Azure 防火牆建議 (預覽)

已新增新建議，以使用 Azure 防火牆來保護您所有的虛擬網路。

**虛擬網路應該受到 Azure 防火牆的保護** 這項建議建議您限制對虛擬網路的存取，並使用 Azure 防火牆來防止潛在威脅。

深入了解 [Azure 防火牆](https://azure.microsoft.com/services/azure-firewall/)。


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>已使用快速修正更新「Kubernetes Service 上應定義授權 IP 範圍」建議

**Kubernetes Service 上應定義授權 IP 範圍** 建議現在具有快速修正選項。

如需此建議及所有其他資訊安全中心建議的詳細資訊，請參閱[安全性建議 - 參考指南](recommendations-reference.md)。

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="具有快速修正選項的「Kubernetes Service 上應定義授權 IP 範圍」建議":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>法規合規性儀表板現在會包含用來移除標準的選項

資訊安全中心的法規合規性儀表板會根據您符合特定合規性控制和需求的程度，提供合規性狀態的見解。

此儀表板包含一組預設的法規標準。 如果所提供的標準中有任何一個與您的組織無關，現在從 UI 中移除訂用帳戶標準的流程變得很簡單。 標準只能在 *訂用帳戶* 層級移除；不能在管理群組範圍移除。

若要深入了解，請參閱 [從儀表板移除標準](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard)。


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>已從 Azure Resource Graph (ARG) 中移除 Microsoft.Security/securityStatuses 資料表

Azure Resource Graph 是 Azure 中的一項服務，透過大規模查詢一組指定訂用帳戶的能力，提供有效率的資源探索，讓您可以有效地治理環境。 

對於 Azure 資訊安全中心，您可以使用 ARG 和 [Kusto 查詢語言 (KQL)](/azure/data-explorer/kusto/query/) 來查詢各種安全性狀態資料。 例如：

- 資產清查利用 (ARG)
- 我們已記載範例 ARG 查詢，說明如何[識別未啟用多重要素驗證 (MFA) 的帳戶](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

在 ARG 中，有可供您在查詢中使用的資料表。

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer 和可用的資料表":::

> [!TIP]
> ARG 文件會在 [Azure Resource Graph 資料表和資源類型參考](../governance/resource-graph/reference/supported-tables-resources.md)中列出所有可用的資料表。

在此更新中，已移除 **Microsoft.Security/securityStatuses** 資料表。 securityStatuses API 仍然可供使用。

資料取代可供 Microsoft.Security/Assessments 資料表使用。

Microsoft.Security/securityStatuses 與 Microsoft.Security/Assessments 之間的主要差異在於，當第一個項目顯示評量的彙總時，第二個項目會為每個評量保留一筆記錄。

例如，Microsoft.Security/securityStatuses 傳回的結果包含兩個 policyAssessments 的陣列：

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
然而，Microsoft.Security/Assessments 會針對每個這類原則評量保留一筆記錄，如下所示：

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**使用 securityStatuses 將現有 ARG 查詢轉換成現在使用 assessments 資料表的範例：**

參考 SecurityStatuses 的查詢：

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

評量資料表的取代查詢：

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

深入了解下列連結：
- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)
- [Kusto 查詢語言 (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>2020 年 9 月

9 月的更新包括：
- [全新的資訊安全中心外觀！](#security-center-gets-a-new-look)
- [已發行 Azure Defender](#azure-defender-released)
- [適用於 Key Vault 的 Azure Defender 已正式推出](#azure-defender-for-key-vault-is-generally-available)
- [適用於儲存體的 Azure Defender 保護已可正式用於檔案儲存體和 ADLS Gen2](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [資產清查工具現已正式推出](#asset-inventory-tools-are-now-generally-available)
- [停用容器登錄和虛擬機器掃描的特定弱點結果](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [讓資源豁免建議](#exempt-a-resource-from-a-recommendation)
- [資訊安全中心內的 AWS 和 GCP 連接器引入多雲端體驗](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Kubernetes 工作負載保護建議組合](#kubernetes-workload-protection-recommendation-bundle)
- [連續匯出現在支援弱點評量結果](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [在建立新資源時強制執行建議，以防止安全性設定錯誤](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [已改善網路安全性群組建議](#network-security-group-recommendations-improved)
- [已淘汰預覽 AKS 建議：「Pod 安全性原則應定義於 Kubernetes Services 中」](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [已改善Azure 資訊安全中心的電子郵件通知](#email-notifications-from-azure-security-center-improved)
- [安全分數不包含預覽建議](#secure-score-doesnt-include-preview-recommendations)
- [建議現在包含嚴重性指標和時效性間隔](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>全新的資訊安全中心外觀！

我們已全面更新資訊安全中心入口網站頁面的 UI。 新頁面包含新的概觀頁面，以及安全分數、資產清查和 Azure Defender 的儀表板。

現在，在重新設計的概觀頁面上，有一個可用來存取安全分數、資產清查和 Azure Defender 儀表板的磚。 還有一個連結至法規合規性儀表板的磚。

深入了解[概觀頁面](overview-page.md)。


### <a name="azure-defender-released"></a>已發行 Azure Defender

**Azure Defender** 是整合於資訊安全中心的雲端工作負載保護平台 (CWPP)，可為您的 Azure 和混合式工作負載提供進階的智慧型保護功能。 其會取代資訊安全中心的標準定價層選項。 

當您從 Azure 資訊安全中心的 [定價和設定] 區域啟用 Azure Defender 時，下列 Defender 方案會同時啟用，並為您環境的計算、資料和服務層提供全面的防護：

- [適用於伺服器的 Azure Defender](defender-for-servers-introduction.md)
- [適用於 App Service 的 Azure Defender](defender-for-app-service-introduction.md)
- [適用於儲存體的 Azure Defender](defender-for-storage-introduction.md)
- [適用於 SQL 的 Azure Defender](defender-for-sql-introduction.md)
- [適用於 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)
- [適用於 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md)
- [適用於容器登錄的 Azure Defender](defender-for-container-registries-introduction.md)

這些方案會分別在資訊安全中心文件中說明。

Azure Defender 會透過其專用儀表板，為虛擬機器、SQL 資料庫、容器、Web 應用程式及網路等項目提供安全性警示和進階威脅防護。

[深入了解 Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>適用於 Key Vault 的 Azure Defender 已正式推出

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 

**適用於 Key Vault 的 Azure Defender** 提供適用於 Azure Key Vault 的 Azure 原生進階威脅防護，可額外提供多一層的安全情報。 透過擴充，適用於 Key Vault 的 Azure Defender 可保護許多與您 Key Vault 帳戶相依的資源。

選擇性方案現已正式推出。 這項功能在預覽版時稱為「適用於 Azure Key Vault 的進階威脅防護」。

此外，Azure 入口網站中的 Key Vault 頁面現在包含專屬的 **安全性** 頁面，用以提供 **資訊安全中心** 的建議和警示。

請在＜[適用於 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)＞中深入了解。


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>適用於儲存體的 Azure Defender 保護已可正式用於檔案儲存體和 ADLS Gen2 

**適用於儲存體的 Azure Defender** 會偵測您 Azure 儲存體帳戶上可能有害的活動。 不論您的資料儲存為 Blob 容器、檔案共用或資料湖，皆可受到保護。

現已正式提供 [Azure 檔案儲存體](../storage/files/storage-files-introduction.md)和 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 的支援。

從 2020 年 10 月 1 日起，我們將開始收取保護這些服務資源的費用。

請在＜[適用於儲存體的 Azure Defender](defender-for-storage-introduction.md)＞中深入了解。


### <a name="asset-inventory-tools-are-now-generally-available"></a>資產清查工具現已正式推出

Azure 資訊安全中心的資產清查頁面會提供單一頁面，讓您檢視已連線至資訊安全中心的資源有何安全性狀態。

資訊安全中心會定期分析 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 然後為您提供如何補救這些弱點的建議。

只要任何資源有未處理的建議，這些建議就會出現在清查中。

如需詳細資訊，請參閱[利用資產清查來探索和管理您的資源](asset-inventory.md)。



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>停用容器登錄和虛擬機器掃描的特定弱點結果

Azure Defender 包含弱點掃描程式，可掃描您 Azure Container Registry 和虛擬機器中的映像。

如果您的組織需要忽略某個結果，而不是將其修復，您可以選擇性地停用該結果。 停用的結果不會影響您的安全分數或產生不想要的雜訊。

當某個結果符合停用規則中定義的準則時，其就不會出現在結果清單中。

此選項位在建議詳細資料頁面：

- **應補救 Azure Container Registry 映像中的弱點**
- **應補救虛擬機器中的弱點**

請在＜[停用容器映像的特定結果](defender-for-container-registries-usage.md#disable-specific-findings-preview)＞和＜[停用虛擬機器的特定結果](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)＞中深入了解。


### <a name="exempt-a-resource-from-a-recommendation"></a>讓資源豁免建議

有時候，資源會因為特定建議而列為狀況不良 (因此降低您的安全分數)，但您認為該資源並非狀況不良。 其可能已由資訊安全中心未追蹤的程序進行補救。 或者您的組織可能已決定接受該特定資源的風險。 

在這種情況下，您可以建立豁免規則，並確保該資源未來不會列在狀況不良的資源中。 這些規則可以包含下列所述的已記載理由。

請在＜[從建議與安全分數中豁免資源](exempt-resource.md)＞中深入了解。


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>資訊安全中心內的 AWS 和 GCP 連接器引入多雲端體驗

由於雲端工作負載通常需要跨越多個雲端平台，因此雲端安全性服務必須執行相同動作。

Azure 資訊安全中心現在會保護 Azure、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 中的工作負載。

將您的 AWS 和 GCP 帳戶上架到資訊安全中心，即可整合 AWS 安全性中樞、GCP 安全性命令和 Azure 資訊安全中心。 

請在＜[將您的 AWS 帳戶連線到 Azure 資訊安全中心](quickstart-onboard-aws.md)＞及＜[將您的 GCP 帳戶連線到 Azure 資訊安全中心](quickstart-onboard-gcp.md)＞中深入了解。


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Kubernetes 工作負載保護建議組合

為了確保預設的 Kubernetes 工作負載是安全的，資訊安全中心會新增 Kubernetes 層級的強化建議，包括 Kubernetes 許可控制的強制選項。

如果您已在 AKS 叢集上安裝適用於 Kubernetes 的 Azure 原則附加元件，則針對 Kubernetes API 伺服器提出的每個要求都會依據預先定義的最佳做法來加以監視，然後再保存到叢集。 接著，您可以進行設定以強制執行最佳做法，並要求未來的工作負載遵循。

例如，您可以要求不得建立具特殊權限的容器，而且未來要求這麼做的任何要求都會遭到封鎖。

請在＜[使用 Kubernetes 許可控制保護工作負載的最佳做法](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)＞中深入了解。


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>連續匯出現在支援弱點評量結果

使用連續匯出將您的警示和建議即時串流至 Azure 事件中樞、Log Analytics 工作區或 Azure 監視器。 然後您可以在該處整合此資料與 SIEM (例如 Azure Sentinel、Power BI、Azure 資料總管等等)。

資訊安全中心的整合式弱點評量工具會傳回您資源的相關結果，以作為「父系」建議中可採取動作的建議，例如「應補救虛擬機器中的弱點」。 

現在，當您選取建議並啟用 [包含安全性結果] 選項時，即可透過連續匯出來匯出安全性結果。

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="在連續匯出設定中包含安全性結果切換" :::

相關頁面：

- [資訊安全中心的 Azure 虛擬機器整合式弱點評量解決方案](deploy-vulnerability-assessment-vm.md)
- [資訊安全中心的 Azure Container Registry 映像整合式弱點評量解決方案](defender-for-container-registries-usage.md)
- [連續匯出](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>在建立新資源時強制執行建議，以防止安全性設定錯誤

錯誤的安全性設定是安全性事件的主要原因。 資訊安全中心現在可以協助「避免」因特定建議而造成的新資源設定錯誤。 

這項功能可協助保護您工作負載的安全，並穩定您的安全分數。

根據特定建議來強制執行安全設定的模式有兩種：

- 使用 Azure 原則的 **Deny** 效果，以防止建立狀況不良的資源

- 使用 [強制執行] 選項，以利用 Azure 原則的 **DeployIfNotExist** 效果，以及在建立資源時自動補救不符合規範的資源
 
這可在選取的安全性建議上取得，也可以在資源詳細資料頁面的頂端找到。

請在＜[防止強制/拒絕建議的設定錯誤](prevent-misconfigurations.md)＞中深入了解。

###  <a name="network-security-group-recommendations-improved"></a>已改善網路安全性群組建議

下列與網路安全性群組相關的安全性建議已經過改良，可減少一些誤判實例。

- 在與您 VM 建立關聯的 NSG 上應限制所有網路連接埠
- 應關閉虛擬機器上的管理連接埠
- 應使用網路安全性群組保護網際網路對應的虛擬機器
- 子網路應該與網路安全性群組建立關聯


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>已淘汰預覽 AKS 建議：「Pod 安全性原則應定義於 Kubernetes Services 中」

預覽建議「Pod 安全性原則應定義於 Kubernetes Services 中」已淘汰，如 [Azure Kubernetes Service](../aks/use-pod-security-policies.md) 文件中所述。

Pod 安全性原則 (預覽) 功能即將淘汰，2020 年 10 月 15 日之後將不再可供使用，請改為使用適用於 AKS 的 Azure 原則。

淘汰 Pod 安全性原則 (預覽) 之後，您必須在任何使用已淘汰功能的現有叢集上停用此功能，以執行未來的叢集升級並保留在 Azure 支援的範圍中。


### <a name="email-notifications-from-azure-security-center-improved"></a>已改善Azure 資訊安全中心的電子郵件通知

關於安全性警示的下列電子郵件區域已獲得改善： 

- 已針對所有嚴重性層級的警示，新增傳送電子郵件通知的功能
- 已新增在訂用帳戶上對不同 Azure 角色的使用者發出通知的功能
- 根據預設，針對高嚴重性的警示 (其成為真正缺口的機率很高)，我們會主動通知訂用帳戶擁有者
- 我們已從電子郵件通知設定頁面移除 [電話號碼] 欄位

請在＜[設定安全性警示的電子郵件通知](security-center-provide-security-contact-details.md)＞中深入了解。


### <a name="secure-score-doesnt-include-preview-recommendations"></a>安全分數不包含預覽建議 

資訊安全中心會持續評估資源、訂用帳戶、組織的安全性問題。 然後將所有的發現彙總成一個分數，讓您可以立即得知目前的安全性情況：分數越高，風險層級越低。

探索到新威脅時，資訊安全中心會透過新建議來提供新的安全性意見。 為了避免意外變更您的安全分數，以及提供可讓您在新建議影響到分數之前先進行探索的寬限期，標示為 **預覽** 的建議已不再包含於您的安全分數計算中。 這些建議仍會盡可能進行補救，以在預覽期間結束時計入您的分數。

此外，**預覽** 建議不會將資源轉譯為「狀況不良」。

預覽建議的範例：

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="使用預覽旗標的建議":::

[深入了解安全分數](secure-score-security-controls.md)。


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>建議現在包含嚴重性指標和時效性間隔

建議的詳細資料頁面現在包含時效性間隔指標 (如果適用)，並清楚顯示建議的嚴重性。

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="顯示時效性和嚴重性的建議頁面":::



## <a name="august-2020"></a>2020 年 8 月

8 月的更新包括：

- [資產清查 - 資產安全性狀態的強大新觀點](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [已新增 Azure Active Directory 安全性預設值的支援 (適用於多重要素驗證)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [已新增服務主體建議](#service-principals-recommendation-added)
- [VM 上的弱點評量 - 合併的建議和原則](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [新增至 ASC_default 方案的新 AKS 安全性原則 - 僅供個人預覽版的客戶使用](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>資產清查 - 資產安全性狀態的強大新觀點

資訊安全中心的資產清查 (目前為預覽狀態) 可針對已連線至資訊安全中心的資源，提供檢視其安全性狀態的方式。

資訊安全中心會定期分析 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 然後為您提供如何補救這些弱點的建議。 只要任何資源有未處理的建議，這些建議就會出現在清查中。

您可以使用此檢視及其篩選器來瀏覽您的安全性狀態資料，並根據您的結果採取進一步的動作。

在＜[資產清查](asset-inventory.md)＞中深入了解。


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>已新增 Azure Active Directory 安全性預設值的支援 (適用於多重要素驗證)

資訊安全中心已新增[安全性預設值](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)的完整支援，這是免費的 Microsoft 身分識別安全性保護。

安全性預設值提供預先設定的身分識別安全性設定，可防止您的組織遭受一般身分識別相關的攻擊。 總體來說，安全性預設值已保護超過 500 萬個租用戶，而 50,000 個租用戶還會受到資訊安全中心的保護。

資訊安全中心現在會在發現 Azure 訂用帳戶未啟用安全性預設值時，提供安全性建議。 到目前為止，資訊安全中心都建議您使用條件式存取來啟用多重要素驗證，這是 Azure Active Directory (AD) 進階授權的一部分。 我們現在建議使用免費 Azure AD 的客戶，也啟用安全性預設值。 

我們的目標是鼓勵更多客戶使用 MFA 保護其雲端環境，並降低最嚴重且高度影響您[安全分數](secure-score-security-controls.md)的其中一個風險。

深入了解[安全性預設值](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)。


### <a name="service-principals-recommendation-added"></a>已新增服務主體建議

已加入新建議，建議使用管理憑證來管理其訂用帳戶的資訊安全中心客戶切換到服務主體。

「**應使用服務主體來保護訂用帳戶，而不是使用管理憑證**」的建議會勸您使用服務主體或 Azure Resource Manager，以更安全地管理您的訂用帳戶。 

深入了解 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>VM 上的弱點評量 - 合併的建議和原則

資訊安全中心會檢查您的 VM，偵測其是否正在執行弱點評量解決方案。 如果找不到任何弱點評量解決方案，資訊安全中心會提供簡化部署的建議。

發現弱點時，資訊安全中心會提供結果的總結建議，以供您調查並視需要進行補救。

為了確保所有使用者都有一致的體驗，不論他們使用的掃描程式類型為何，我們都已將四個建議整合到下列兩個建議中：

|統一的建議|變更描述|
|----|:----|
|**虛擬機器上應啟用弱點評估解決方案**|取代下列兩項建議：<br> **•** 在虛擬機器上啟用內建弱點評量解決方案 (Qualys 技術支援 (現已淘汰)) (隨附於標準層)<br> **•** 虛擬機器上應該安裝弱點評量解決方案 (現已淘汰) (隨附於標準層和免費層)|
|**應補救虛擬機器中的弱點**|取代下列兩項建議：<br>**•** 補救在您虛擬機器上發現的弱點 (Qualys 技術提供) (現已淘汰)<br>**•** 弱點評量解決方案應補救弱點 (現已淘汰)|
|||

現在您將使用相同的建議，從合作夥伴 (例如 Qualys 或 Rapid7) 部署資訊安全中心的弱點評量擴充功能或私下授權的解決方案 ("BYOL")。

此外，當發現弱點並回報資訊安全中心時，不論發現這些弱點的弱點評量解決方案為何，都會有單一建議會向您發出這些結果的警示。

#### <a name="updating-dependencies"></a>更新相依性

如果您有參考先前建議或原則金鑰/名稱的指令碼、查詢或自動化，請使用下列資料表來更新參考：

##### <a name="before-august-2020"></a>2020 年 8 月之前

|建議|影響範圍|
|----|:----|
|**在虛擬機器上啟用內建弱點評量解決方案 (Qualys 技術支援)**<br>機碼：550e890b-e652-4d22-8274-60b3bdb24c63|內建|
|**補救在您的虛擬機器上發現的弱點 (Qualys 技術提供)**<br>機碼：1195afff-c881-495e-9bc5-1486211ae03f|內建|
|**虛擬機器上應安裝弱點評定解決方案**<br>機碼：01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**弱點評量解決方案應修復弱點**<br>機碼：71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|原則|影響範圍|
|----|:----|
|**虛擬機器上應啟用弱點評定**<br>原則識別碼：501541f7-f7e7-4cd6-868c-4190fdad3ac9|內建|
|**弱點評量解決方案應補救弱點**<br>原則識別碼：760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>從 2020 年 8 月起

|建議|影響範圍|
|----|:----|
|**虛擬機器上應啟用弱點評估解決方案**<br>金鑰：ffff0522-1e88-47fc-8382-2a80ba848f5d|內建 + BYOL|
|**應補救虛擬機器中的弱點**<br>機碼：1195afff-c881-495e-9bc5-1486211ae03f|內建 + BYOL|
||||

|原則|影響範圍|
|----|:----|
|[**虛擬機器上應啟用弱點評量**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>原則識別碼：501541f7-f7e7-4cd6-868c-4190fdad3ac9 |內建 + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>新增至 ASC_default 方案的新 AKS 安全性原則 - 僅供個人預覽版的客戶使用

為了確保預設的 Kubernetes 工作負載是安全的，資訊安全中心會新增 Kubernetes 層級的原則和強化建議，包括 Kubernetes 許可控制的強制選項。

此專案的初期階段包括個人預覽版，以及將新原則 (預設為停用) 新增至 ASC_default 方案。

您可以放心地忽略這些原則，並不會對您的環境造成任何影響。 如果您想要啟用這些原則，請在 https://aka.ms/SecurityPrP 註冊預覽版，然後從下列選項中選取：

1. **單一預覽** – 僅加入此個人預覽版。 明確提及「ASC 連續掃描」作為您想要加入的預覽。
1. **進行中的計畫** – 新增到此個人預覽版及未來的個人預覽版。 您必須完成設定檔和隱私權合約。