---
title: Azure 資訊安全中心內新功能的封存
description: 說明 Azure 資訊安全中心從六個月前及更早版本的新功能和變更。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2020
ms.author: memildin
ms.openlocfilehash: 751ee19225e7e550f368fff2415cd07f25b02d25
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539921"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Azure 資訊安全中心內新功能的封存？

[Azure 資訊安全中心的新功能？](release-notes.md)主要版本資訊頁面包含過去六個月的更新，而此頁面包含較舊的項目。

本頁面提供以下相關資訊：

- 新功能
- 錯誤修正
- 已被取代的功能


## <a name="july-2020"></a>2020 年 7 月

7 月的更新包括：
- [虛擬機器的弱點評量現在可用於非市集映像](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Azure 儲存體的威脅防護已擴充為包含 Azure 檔案儲存體和 Azure Data Lake Storage Gen2 (預覽)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [啟用威脅防護功能的八個新建議](#eight-new-recommendations-to-enable-threat-protection-features)
- [容器安全性改善 - 加快登錄掃描和重新整理文件的速度](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [使用新建議更新自適性應用程式控制和路徑規則中支援萬用字元](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [已淘汰 SQL 進階資料安全性的六個原則](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>虛擬機器的弱點評量現在可用於非市集映像

部署弱點評量解決方案時，資訊安全中心已先執行驗證檢查，然後再進行部署。 檢查是要確認目的地虛擬機器的市集SKU。 

此更新中已移除這項檢查，您現在可以將弱點評量工具部署到「自訂」的 Windows 和 Linux 電腦。 自訂映像是您從市集預設值中修改的映像。

雖然您現在可以在許多電腦上部署整合式弱點評量擴充功能 (由 Qualys 提供技術支援)，但只有當您使用＜[將整合式弱點掃描程式部署到標準層 VM 中](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)＞所列的作業系統時，才能受到支援

深入了解[適用於虛擬機器的整合式弱點掃描程式 (需要 Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)。

若要深入了解如何從 Qualys 或 Rapid7 使用您自己的私下授權弱點評量解決方案，請參閱[部署合作夥伴弱點掃描解決方案](deploy-vulnerability-assessment-vm.md)。


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Azure 儲存體的威脅防護已擴充為包含 Azure 檔案儲存體和 Azure Data Lake Storage Gen2 (預覽)

Azure 儲存體的威脅防護會偵測 Azure 儲存體帳戶上可能有害的活動。 資訊安全中心會在偵測到儲存體帳戶上有存取或惡意探索的嘗試時，顯示警示。 

不論您的資料儲存為 Blob 容器、檔案共用或資料湖，皆可受到保護。




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>啟用威脅防護功能的八個新建議

已新增八項新建議，目的是提供簡單的方式來為下列資源類型啟用 Azure 資訊安全中心的威脅防護功能：虛擬機器、App Service 方案、Azure SQL Database 伺服器、機器上的 SQL 伺服器、Azure 儲存體帳戶、Azure Kubernetes Service 叢集、Azure Container Registry 登錄，以及 Azure Key Vault 保存庫。

新建議如下：

- **Azure SQL Database 伺服器應啟用進階資料安全性**
- **應在機器上的 SQL 伺服器啟用進階資料安全性**
- **應在 Azure App Service 方案上啟用進階威脅防護**
- **應在 Azure Container Registry 登錄上啟用進階威脅防護**
- **應在 Azure Key Vault 保存庫上啟用進階威脅防護**
- **應在 Azure Kubernetes Service 叢集上啟用進階威脅防護**
- **應在 Azure 儲存體帳戶上啟用進階威脅防護**
- **應在虛擬機器上啟用進階威脅防護**

這些新建議屬於 **啟用 Azure Defender** 安全性控制。

這些建議也包含快速修正功能。 

> [!IMPORTANT]
> 補救這些建議中的任何一項，都會產生相關資源的保護費用。 如果您目前的訂用帳戶中有相關資源，這些費用就會立即產生。 或是，如果您是在日後新增這些資源，則費用會在未來產生。
> 
> 例如，如果您的訂用帳戶中沒有任何 Azure Kubernetes Service 叢集，那麼您啟用威脅防護並不會產生任何費用。 如果您未來會在相同的訂用帳戶上新增叢集，則該叢集將會自動受到保護，並於該時間開始收費。

如需詳細資訊，請參閱[安全性建議參考頁面](recommendations-reference.md)。

深入了解 [Azure 資訊安全中心內的威脅防護](azure-defender.md)。




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>容器安全性改善 - 加快登錄掃描和重新整理文件的速度

在不斷投資容器安全性領域的過程中，我們很高興資訊安全中心在對 Azure Container Registry 中儲存的容器映像進行動態掃描時，已大幅地改善效能。 現在，掃描通常會在大約兩分鐘內完成。 在某些情況下，最多可能需要 15 分鐘的時間。

為了在 Azure 資訊安全中心的容器安全功能上改善簡潔度和指引方式，我們也重新整理了容器安全性的文件頁面。 

若要深入了解資訊安全中心的容器安全性，請參閱下列文章：

- [資訊安全中心的容器安全功能概觀](container-security.md)
- [與 Azure Container Registry 整合的詳細資料](defender-for-container-registries-introduction.md)
- [與 Azure Kubernetes Service 整合的詳細資料](defender-for-kubernetes-introduction.md)
- [如何掃描您的登錄並強化 Docker 主機](container-security.md)
- [來自 Azure Kubernetes Service 叢集威脅防護功能的安全性警示](alerts-reference.md#alerts-akscluster)
- [來自 Azure Kubernetes Service 主機威脅防護功能的安全性警示](alerts-reference.md#alerts-containerhost)
- [容器的安全性建議](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>使用新建議更新自適性應用程式控制和路徑規則中支援萬用字元

自適性應用程式控制功能已收到兩個重大更新：

* 新建議會識別先前未允許的可能合理行為。 「**您應該更新自適性應用程式控制原則中的允許清單規則**」建議會提示您將新規則新增至現有原則，以減少自適性應用程式控制違規警示中的誤判數量。

* 路徑規則現在支援萬用字元。 在此更新中，您可以使用萬用字元來設定允許的路徑規則。 支援的案例有兩種：

    * 在路徑結尾使用萬用字元，以允許此資料夾和子資料夾中的所有可執行檔

    * 在路徑中間使用萬用字元，可啟用其資料夾名稱不固定的已知可執行檔名稱 (例如，具有已知可執行檔的個人使用者資料夾、自動產生的資料夾名稱等等)。


[深入了解自適性應用程式控制](security-center-adaptive-application.md)。



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>已淘汰 SQL 進階資料安全性的六個原則

與 SQL 機器進階資料安全性相關的六個原則即將淘汰：

- 在 SQL 受控執行個體的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]
- 在 SQL 伺服器的進階資料安全性設定中，進階威脅防護類型應設定為「全部」
- SQL 受控執行個體的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址
- SQL 伺服器的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址
- 應在 SQL 受控執行個體進階資料安全性設定中，啟用傳給系統管理員和訂用帳戶擁有者的電子郵件通知
- 應在 SQL 伺服器進階資料安全性設定中啟用傳給系統管理員和訂用帳戶擁有者的通知

深入了解[內建原則](./policy-reference.md)。



## <a name="june-2020"></a>2020 年 6 月

六月的更新包括：
- [安全分數 API (預覽)](#secure-score-api-preview)
- [SQL 電腦的 Advanced data security (Azure、其他雲端和內部部署)  (preview) ](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [將 Log Analytics 代理程式部署至 Azure Arc 機器的兩項新建議 (預覽)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [可大規模建立連續匯出和工作流程自動化設定的新原則](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [使用 NSG 來保護非網際網路面向虛擬機器的新建議](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [啟用威脅防護和進階資料安全性的新原則](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>安全分數 API (預覽)

您現在可以透過[安全分數 API](/rest/api/securitycenter/securescores/) (目前為預覽狀態) 來存取您的分數。 API 方法可讓您靈活地查詢資料，並在一段時間後建立您自己的安全分數報告機制。 例如，您可以使用 **安全分數** API 來取得特定訂用帳戶的分數。 此外，您可以使用 **安全分數控制項** API 來列出您訂用帳戶的安全性控制項和目前分數。

如需安全分數 API 搭配使用外部工具的範例，請參閱 [GitHub 社群的安全分數區域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。

深入了解 [Azure 資訊安全中心內的安全分數和安全性控制項](secure-score-security-controls.md)。



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>SQL 電腦的 Advanced data security (Azure、其他雲端和內部部署)  (preview) 

Azure 資訊安全中心的 SQL 機器進階資料安全性現在會保護裝載於 Azure、其他雲端環境，甚至是內部部署機器上的 SQL Server。 這會擴充 Azure 原生的 SQL 伺服器保護，進而完整支援混合式環境。

進階資料安全性可為您的 SQL 機器提供弱點評量和進階威脅防護，且無論機器位於何處都適用。

設定包含兩個步驟：

1. 將 Log Analytics 代理程式部署到您 SQL Server 的主機機器，以提供 Azure 帳戶的連線。

1. 在資訊安全中心的 [定價和設定] 頁面中啟用選用套件組合。

深入了解[適用於 SQL 機器的進階資料安全性](defender-for-sql-usage.md)。



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>將 Log Analytics 代理程式部署至 Azure Arc 機器的兩項新建議 (預覽)

已新增兩個新建議，協助您將 [Log Analytics 代理程式](../azure-monitor/platform/log-analytics-agent.md)部署至您的 Azure Arc 機器，並確保其受到 Azure 資訊安全中心的保護：

- **Windows 版 Azure Arc 機器上應安裝 Log Analytics 代理程式 (預覽)**
- **Linux 版 Azure Arc 機器上應安裝 Log Analytics 代理程式 (預覽)**

這些新建議會出現在與現有 (相關) 建議 (**您的機器上應安裝監視代理程式**) 相同的四個安全性控制項中：補救安全性設定、套用自適性應用程式控制、套用系統更新，以及啟用端點保護。

這些建議也包含快速修正功能，可協助加速部署程序。 

在[計算和應用程式建議](recommendations-reference.md#recs-compute)資料表中深入了解這兩項新的建議。

若要深入了解 Azure 資訊安全中心如何使用代理程式，請參閱[什麼是 Log Analytics 代理程式？](faq-data-collection-agents.md#what-is-the-log-analytics-agent)。

深入了解[適用於 Azure Arc 機器的擴充功能](../azure-arc/servers/manage-vm-extensions.md)。


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>可大規模建立連續匯出和工作流程自動化設定的新原則

自動化組織的監視和事件回應程程序，可大幅改善調查和緩解安全性事件所需的時間。

若要在整個組織中部署您的自動化設定，請使用這些內建的 'DeployIfdNotExist' Azure 原則來建立和設定[連續匯出](continuous-export.md)和[工作流程自動化](workflow-automation.md)程序：

您可以在 Azure 原則中找到這些原則：


|目標  |原則  |原則識別碼  |
|---------|---------|---------|
|連續匯出至事件中樞|[為 Azure 資訊安全中心警示與建議部署「匯出至事件中樞」](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|連續匯出至 Log Analytics 工作區|[為 Azure 資訊安全中心警示與建議部署「匯出至 Log Analytics 工作區」](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|安全性警示的工作流程自動化|[為 Azure 資訊安全中心警示部署工作流程自動化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|安全性建議的工作流程自動化|[為 Azure 資訊安全中心建議部署工作流程自動化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

開始使用[工作流程自動化範本](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)。

若要深入了解如何使用這兩個匯出原則，請參閱 [使用提供的原則大規模地設定工作流程自動化](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) 和 [設定連續匯出](continuous-export.md#set-up-a-continuous-export)。


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>使用 NSG 來保護非網際網路面向虛擬機器的新建議

「實作安全性最佳做法」的安全性控制項現在包含下列新的建議：

- **應使用網路安全性群組保護非網際網路對應的虛擬機器**

「**網際網路面向虛擬機器應使用網路安全性群組加以保護**」的現有建議，並無法區分網際網路面向和非網際網路面向的 VM。 針對這兩種情況，如果 VM 未指派給網路安全性群組，就會產生高嚴重性建議。 這項新的建議會區隔非網際網路面向的機器，以減少誤判，並避免不必要的高嚴重性警示。

請在[網路建議](recommendations-reference.md#recs-networking)資料表中深入了解。




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>啟用威脅防護和進階資料安全性的新原則

以下新原則已新增至 ASC 預設方案，其設計目的是為了協助相關資源類型啟用威脅防護或進階資料安全性。

您可以在 Azure 原則中找到這些原則：


| 原則                                                                                                                                                                                                                                                                | 原則識別碼                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Azure SQL Database 伺服器應啟用進階資料安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [應在機器上的 SQL 伺服器啟用進階資料安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [應在 Azure 儲存體帳戶上啟用進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [應在 Azure Key Vault 保存庫上啟用進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [應在 Azure App Service 方案上啟用進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [應在 Azure Container Registry 登錄上啟用進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [應在 Azure Kubernetes Service 叢集上啟用進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [應在虛擬機器上啟用進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

深入了解 [Azure 資訊安全中心內的威脅防護](azure-defender.md)。


## <a name="may-2020"></a>2020 年 5 月

5 月的更新包括：
- [重複警示歸併規則 (預覽)](#alert-suppression-rules-preview)
- [虛擬機器弱點評估現已正式推出](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Just-In-Time (JIT) 虛擬機器 (VM) 存取的變更](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [自訂建議已移至個別的安全性控制](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [在控制項中或以一般清單的方式，加入查看建議的切換](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [擴充的安全性控制「實作安全性最佳做法」](#expanded-security-control-implement-security-best-practices)
- [具有自訂中繼資料的自訂原則現已正式推出](#custom-policies-with-custom-metadata-are-now-generally-available)
- [損毀傾印分析功能移轉至無檔案攻擊偵測](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>重複警示歸併規則 (預覽)

這項新功能 (目前處於預覽狀態) 有助於減少警示疲勞。 可使用規則自動隱藏已知為無害或組織一般活動的相關警示。 如此您就可以專注處理最為密切相關的威脅。 

仍會產生符合已啟用歸併規則的警示，但其狀態會設定為 [關閉]。 您可以在 Azure 入口網站中看到狀態，或是您存取資訊安全中心的安全性警示的方式。

歸併規則會定義應會自動關閉警示的條件。 一般會使用歸併規則來執行下列動作：

- 隱藏已識別為誤判為真的警示

- 隱藏太常觸發而不實用的警示

深入瞭解[隱藏來自 Azure 資訊安全中心威脅防護的警示](alerts-suppression-rules.md)。


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>虛擬機器弱點評估現已正式推出

資訊安全中心標準層級現在包含了虛擬機器的整合式弱點評估，不需額外付費。 此延伸模組是由 Qualys 提供技術支援，但會直接回報給資訊安全中心。 您不需要 Qualys 授權或 Qualys 帳戶，一切都可以在資訊安全中心內流暢進行。

新的解決方案可持續掃描您的虛擬機器，並找出弱點，然後在資訊安全中心中呈現結果。 

若要部署解決方案，請使用新的安全性建議：

「在虛擬機器上啟用內建弱點評定解決方案 (Qualys 技術支援)」

深入瞭解[資訊安全中心的虛擬機器整合式弱點評估](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)。



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Just-In-Time (JIT) 虛擬機器 (VM) 存取的變更

資訊安全中心包含可保護 VM 管理連接埠的選用功能。 這可讓您防禦最常見的暴力密碼破解攻擊形式。

此更新會對這項功能進行下列變更：

- 建議您在 VM 上啟用 JIT 的建議已重新命名。 以前是「Just-In-Time 網路存取控制應套用在虛擬機器上」，現在是：「應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠」。

- 只有在有開啟的管理連接埠時才會觸發建議。

深入瞭解 [JIT 存取功能](security-center-just-in-time.md)。


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>自訂建議已移至個別的安全性控制

增強型安全分數所引進的其中一個安全性控制項是「實作安全性最佳做法」。 針對您的訂閱所建立的任何自訂建議都會自動放在該控制項中。 

為了讓您更輕鬆地找到您的自訂建議，我們已將其移至專用的安全性控制「自訂建議」。 此控制項不會影響您的安全分數。

深入瞭解[在 Azure 資訊安全中心之內的增強型安全分數 (預覽)](secure-score-security-controls.md) 中出現的安全性控制項。


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>在控制項中或以一般清單的方式，加入查看建議的切換

安全性控制是相關安全性建議的邏輯群組， 會反映您的易受攻擊面。 控制項是一組安全性建議，其中包含有助於執行建議的指示。

若要立即查看您的組織如何保護每個個別的受攻擊面，請參閱每個安全性控制的分數。

根據預設，您的建議會顯示在 [安全性] 控制項中。 在此更新中，您也可以將其顯示為清單。 若要按受影響資源的健全狀況狀態排序，以顯示為方便查看的簡明清單，請使用新的切換功能 [依據控制項分組]。 切換功能位在入口網站中的清單上方。

安全性控制措施和此切換功能皆屬於新的安全分數體驗。 請記得在入口網站中傳送您的意見反應給我們。

深入瞭解[在 Azure 資訊安全中心之內的增強型安全分數 (預覽)](secure-score-security-controls.md) 中出現的安全性控制項。

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="建議的「依據控制項分組」切換":::

### <a name="expanded-security-control-implement-security-best-practices"></a>擴充的安全性控制「實作安全性最佳做法」 

增強型安全分數所引進的其中一個安全性控制項是「實作安全性最佳做法」。 當此控制項中有建議時，不會影響到安全分數。 

在此更新中，有三個建議已移出原先放在其中的控制項，並新增至此最佳做法控制項。 我們已經採取此步驟，是因為我們判斷這三個建議的風險低於一開始所考慮的風險。

此外，也引進了兩個新的建議，並將其新增至這個控制項。

這三個已移動的建議如下：

- **應在您訂閱上具有讀取權限的帳戶上啟用 MFA** (原本在「啟用 MFA」控制項中)
- **具有讀取權限的外部帳戶應從您的訂閱** 中移除 (原本是在「管理存取權和權限」控制項中)
- **您的訂閱最多可指定 3 位擁有者** (原本是在「管理存取權和權限」控制項中)

新增至控制項的兩個新建議如下：

- **Windows 虛擬機器上應該安裝來賓設定擴充功能 (預覽)** - 使用 [Azure 原則來賓設定](../governance/policy/concepts/guest-configuration.md)在虛擬機器中提供伺服器和應用程式設定的可見度 (僅限 Windows)。

- **您的機器上應該啟用 Windows Defender 惡意探索防護 (預覽)** - Windows Defender 惡意探索防護會運用 Azure 原則來賓設定代理程式的功能。 「惡意探索防護」有四個元件，設計用來鎖定裝置，使其免於遭受惡意程式碼攻擊的各種攻擊和封鎖行為，同時讓企業能夠平衡本身的安全性風險和生產力需求 (僅限 Windows)。

若要深入瞭解 Windows Defender 惡意探索防護，請參閱[建立及部署惡意探索防護原則](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)。

在[增強型安全分數 (預覽)](secure-score-security-controls.md) 中深入了解安全性控制項。



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>具有自訂中繼資料的自訂原則現已正式推出

資訊安全中心建議體驗、安全分數和法規合規性標準儀表板中現在包含自訂原則現。 這項功能現已正式推出，可讓您在資訊安全中心中擴充組織的安全性評估涵蓋範圍。 

在 Azure 原則中建立自訂方案、新增原則並將其上線至 Azure 資訊安全中心，並將其視覺化為建議。

我們現在也新增了編輯自訂建議中繼資料的選項。 中繼資料選項包括嚴重性、補救步驟、威脅資訊等等。  

深入瞭解[使用詳細資料來增強您的自訂建議](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information)。



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>損毀傾印分析功能移轉至無檔案攻擊偵測 

我們會將 Windows 損毀傾印分析 (CDA) 偵測功能整合到[無檔案攻擊偵測](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers)。 無檔案攻擊偵測分析會針對 Windows 機器引進下列安全性警示的改良版本：探索到程式碼插入、偵測到偽裝的 Windows 模組、發現的程式碼，以及偵測到可疑的程式碼片段。

此轉換的一些優點：

- **主動式和即時的惡意程式碼偵測** - 涉及等待損毀然後執行分析以尋找惡意成品所用的 CDA 方法。 使用無檔案攻擊偵測可在執行中時，為記憶體內部威脅提供主動式識別。 

- **擴充的警示** - 來自無檔案攻擊偵測的安全性警示包含無法從 CDA 取得的擴充，例如使用中的網路連線資訊。 

- **警示匯總** - 當 CDA 在單一損毀傾印中偵測到多個攻擊模式時，它會觸發多個安全性警示。 無檔案攻擊偵測會將所有已識別的攻擊模式從相同的程式合併到單一警示，而不需要相互關聯多個警示。

- **降低 Log Analytics 工作區的需求** - 包含潛在敏感性資料的損毀傾印不會再上傳至您的 Log Analytics 工作區。






## <a name="april-2020"></a>2020 年 4 月

4 月的更新包括：
- [動態合規性套件現已正式推出](#dynamic-compliance-packages-are-now-generally-available)
- [Azure 資訊安全中心免費層中現在已包含身分識別建議](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>動態合規性套件現已正式推出

Azure 資訊安全中心的法規合規性儀表板現在包含 **動態相容性套件** (現已正式運作)，以追蹤額外的產業和法規標準。

您可以從 [資訊安全中心安全性原則] 頁面，將動態相容性套件新增至您的訂閱或管理群組。 當您上架標準或基準測試時，標準會出現在您的法規合規性儀表板中，並將所有相關聯的相容性資料對應為評量。 任何已上架之標準的摘要報告都可供下載。

現在，您可以加入標準，例如：

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official 與 UK NHS**
- **加拿大聯邦 PBMM**
- **Azure CIS 1.1.0 (新)** (這是更完整的 Azure CIS 1.1.0 標記法)

此外，我們最近新增了 **Azure 安全性基準測試**，這是 Microsoft 針對以通用合規性架構為基礎的安全性和合規性最佳做法所撰寫的 Azure 特定指導方針。 當儀表板可供使用時，將會支援其他標準。  
 
深入了解[如何在您的法規合規性儀表板中自訂一組標準](update-regulatory-compliance-packages.md)。


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Azure 資訊安全中心免費層中現在已包含身分識別建議

Azure 資訊安全中心免費層的身分識別和存取安全性建議現已正式推出。 這是讓雲端安全性狀態管理 (CSPM) 功能免費的一部份。 到目前為止，這些建議僅適用於標準定價層。

身分識別與存取建議的範例包括：

- 「應在您訂閱上具有擁有者權限的帳戶上啟用多重要素驗證。」
- 「應針對您的訂閱指定最多 3 位擁有者。」
- 「已取代帳戶應該從您的訂閱中移除。」

如果您擁有免費定價層的訂閱，其安全分數會受到這項變更的影響，因為這些訂閱永遠不會評估其身分識別和存取安全性。

深入瞭解[身分識別與存取建議](recommendations-reference.md#recs-identityandaccess)。

深入瞭解[監視身分識別及存取](security-center-identity-access.md)。



## <a name="march-2020"></a>2020 年 3 月

3月更新包括：

- [工作流程自動化現已正式推出](#workflow-automation-is-now-generally-available)
- [Azure 資訊安全中心與 Windows 管理中心整合](#integration-of-azure-security-center-with-windows-admin-center)
- [Azure Kubernetes Service 的防護](#protection-for-azure-kubernetes-service)
- [改良的 Just-In-Time 體驗](#improved-just-in-time-experience)
- [Web 應用程式的兩個安全性建議已被取代](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>工作流程自動化現已正式推出

Azure 資訊安全中心的工作流程自動化功能現已正式推出。 可使用此功能自動對安全性警示和建議觸發 Logic Apps。 此外，您還可以使用手動觸發程式來取得警示，以及具有快速修正選項的所有建議。

每個安全性程式都包含事件回應的多個工作流程。 這些流程可能包括通知相關的專案利害關係人、啟動變更管理程式，以及套用特定的補救步驟。 安全性專家建議您盡可能自動化這些程式的許多步驟。 自動化會藉由確保按照您預先定義的需求快速、一致地執行程式步驟，來減少額外負荷並改善您的安全性。

如需執行工作流程之自動和手動資訊安全中心功能的詳細資訊，請參閱[工作流程自動化](workflow-automation.md)。

深入瞭解[建立邏輯應用程式](../logic-apps/logic-apps-overview.md)。


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Azure 資訊安全中心與 Windows 管理中心整合

現在可以將您的內部部署 Windows 伺服器直接從 Windows 系統管理中心移至 Azure 資訊安全中心。 資訊安全中心接著會變成您的單一窗格，可供查看所有 Windows 系統管理中心資源的安全性資訊，包括內部部署伺服器、虛擬機器和其他 PaaS 工作負載。

將伺服器從 Windows 系統管理中心移至 Azure 資訊安全中心之後，您將能夠：

- 在 Windows 系統管理中心的資訊安全中心延伸模組中，查看安全性警示和建議。
- 在 Azure 入口網站 (或透過 API) 中的資訊安全中心，查看安全性狀態，並擷取 Windows 管理中心受控伺服器的其他詳細資訊。

深入瞭解[如何整合 Azure 資訊安全中心與 Windows 管理中心](windows-admin-center-integration.md)。


### <a name="protection-for-azure-kubernetes-service"></a>Azure Kubernetes Service 的防護

Azure 資訊安全中心正在擴充其容器安全性功能，以保護 Azure Kubernetes Service (AKS)。

熱門的開放原始碼平台 Kubernetes 已獲得廣泛採用，現在已成為容器協調流程的業界標準。 雖然這種實作相當廣泛，仍然不瞭解如何保護 Kubernetes 環境。 保護容器化應用程式的受攻擊面需要專業知識，以確保能夠安全地設定基礎結構，並持續監視潛在威脅。

資訊安全中心防禦包括：

- **探索與可見度** - 在向資訊安全中心註冊的訂閱內，持續探索受控 AKS 執行個體。
- **安全性建議** - 可採取動作的建議，以協助您符合 AKS 的安全性最佳做法。 這些建議會包含在您的安全分數中，以確保這些建議會被視為組織安全性狀態的一部份。 如需 AKS 相關的建議，您可以參閱「應使用角色型存取控制來限制對 Kubernetes Service 叢集的存取」。
- **威脅防護** - 透過 AKS 部署的持續分析，資訊安全中心在主機和 AKS 叢集層級偵測到威脅和惡意活動的警示。

深入瞭解 [Azure Kubernetes Service 與資訊安全中心整合](defender-for-kubernetes-introduction.md)。

深入瞭解[資訊安全中心內的容器安全性功能](container-security.md)。


### <a name="improved-just-in-time-experience"></a>改良的 Just-In-Time 體驗

適用於 Azure 資訊安全中心的 Just-In-Time 工具保護您的管理埠所用的功能、操作和 UI 經過強化，如下所示： 

- **理由欄位** - 透過 Azure 入口網站的 Just-In-Time 頁面要求存取虛擬機器 (VM) 時，可以使用新的選用欄位來輸入要求的理由。 輸入此欄位的資訊可在活動記錄檔中追蹤。 
- **自動清除多餘的 Just-In-Time 規則** - 每當您更新 JIT 原則時，就會自動執行清除工具來檢查整個規則集的有效性。 此工具會尋找您的原則中的規則與 NSG 中的規則之間的不符之處。 如果清除工具發現不相符的問題，它會判斷原因，而當安全地移除不需要的內建規則。 清理程式永遠不會刪除您已建立的規則。 

深入瞭解 [JIT 存取功能](security-center-just-in-time.md)。


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Web 應用程式的兩個安全性建議已被取代

與 Web 應用程式相關的兩個安全性建議會被取代： 

- 應強化 IaaS NSG 上 Web 應用程式的規則。
    (相關原則：應在 IaaS 上強化 Web 應用程式的 NSG 規則)

- 應限制對應用程式服務的存取。
    (相關原則：應限制存取應用程式服務 [預覽])

這些建議不會再出現在資訊安全中心的建議清單中。 相關的原則將不再包含在名為「資訊安全中心預設」的方案中。

深入瞭解[安全性建議](recommendations-reference.md)。




## <a name="february-2020"></a>2020 年 2 月

### <a name="fileless-attack-detection-for-linux-preview"></a>適用於 Linux 的無檔案攻擊偵測 (預覽)

當攻擊者增加採用 stealthier 方法以避免偵測時，除了 Windows 之外，Azure 資訊安全中心也會擴充 Linux 的無檔案攻擊偵測。 無檔案攻擊會運用軟體弱點，並將惡意承載插入良性系統程序，然後在記憶體中隱藏。 這些技術：

- 最小化或排除磁碟上的惡意程式碼追蹤
- 藉由以磁碟為基礎的惡意程式碼掃描解決方案，大幅降低偵測的機會

若要對付這項威脅，Azure 資訊安全中心在 2018 年 10 月發行 Windows 的無檔案攻擊偵測，而且現在也已在 Linux 上擴充無檔案攻擊偵測。 



## <a name="january-2020"></a>2020 年 1 月

### <a name="enhanced-secure-score-preview"></a>增強的安全分數 (預覽)

Azure 資訊安全中心的安全分數功能增強版本現已提供預覽。 在此版本中，會將多個建議分組為安全性控制措施，以便更清楚地反映易受攻擊面 (例如，限制對管理連接埠的存取)。

請熟悉預覽階段中的安全分數變更，並決定有助於您進一步保護環境的其他補救。

深入瞭解 [增強的安全分數 (預覽) ](secure-score-security-controls.md)。



## <a name="november-2019"></a>2019 年 11 月

11 月的更新包括：
 - [北美洲區域中 Azure Key Vault 的威脅防護 (預覽) ](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Azure 儲存體的威脅防護包括惡意程式碼信譽檢測](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Logic Apps 的工作流程自動化 (預覽)](#workflow-automation-with-logic-apps-preview)
 - [適用於大量資源的快速修正已正式推出](#quick-fix-for-bulk-resources-generally-available)
 - [掃描容器映像的弱點 (預覽)](#scan-container-images-for-vulnerabilities-preview)
 - [其他法規合規性標準 (預覽)](#additional-regulatory-compliance-standards-preview)
 - [Azure Kubernetes Service 的威脅防護 (預覽)](#threat-protection-for-azure-kubernetes-service-preview)
 - [虛擬機器弱點評估 (預覽)](#virtual-machine-vulnerability-assessment-preview)
 - [Azure 虛擬機器上 SQL 伺服器的進階資料安全性 (預覽)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [支援自訂原則 (預覽)](#support-for-custom-policies-preview)
 - [使用適用於社區及合作夥伴的平台來擴充 Azure 資訊安全中心的涵蓋範圍](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [與建議和警示匯出的進階整合 (預覽)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [從 Windows 管理中心將內部部署伺服器上線到資訊安全中心 (預覽)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>北美洲區域中 Azure Key Vault 的威脅防護 (預覽) 

Azure Key Vault 是一項重要的服務，可讓您集中管理雲端中的金鑰、秘密、密碼編譯金鑰與原則，藉此保護資料並改善雲端應用程式的效能。 由於 Azure Key Vault 會儲存敏感性和業務關鍵資料，因此對於金鑰保存庫和其中所儲存資料需要最高的安全性。

Azure 資訊安全中心對 Azure Key Vault 的威脅防護支援提供增加額外的安全情報層級，用於偵測金鑰保存庫中異常且可能有害的存取意圖或攻擊。 這個新的防護層可讓客戶不需是安全性專家或管理安全性監視系統，就能解決其金鑰保存庫的威脅。 這項功能在北美洲區域中為公開預覽狀態。


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Azure 儲存體的威脅防護包括惡意程式碼信譽檢測

Azure 儲存體的威脅防護提供由 Microsoft 威脅情報提供技術支援的新偵測，以使用雜湊信譽分析來偵測上傳至 Azure 儲存體的惡意程式碼，以及來自作用中 Tor 結束節點的可疑存取 (匿名 Proxy)。 您現在可以使用 Azure 資訊安全中心，跨儲存體帳戶檢視偵測到的惡意程式碼。


### <a name="workflow-automation-with-logic-apps-preview"></a>Logic Apps 的工作流程自動化 (預覽)

具有集中受控安全性和 IT/營運的組織會實作內部工作流程程序，以在其環境中發現不一致的情況時，在組織內驅動所需的動作。 在許多情況下，這些工作流程是可重複的程序，且自動化可以大幅簡化組織內的程序。

如今，我們在資訊安全中心引進了一項新功能，可讓客戶運用 Azure Logic Apps 建立自動化設定並建立原則，以根據特定的 ASC 結果 (例如「建議」或「警示」) 加以自動觸發。 Azure 邏輯應用程式可以設定為執行邏輯應用程式連接器大量社群所支援的任何自訂動作，或使用資訊安全中心所提供的其中一個範本，例如傳送電子郵件或開啟 ServiceNow™ 票證。

如需執行工作流程之自動及手動資訊安全中心功能的詳細資訊，請參閱[工作流程自動化](workflow-automation.md)。

若要了解如何建立 Logic Apps，請參閱 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)。


### <a name="quick-fix-for-bulk-resources-generally-available"></a>適用於大量資源的快速修正已正式推出

由於在「安全分數」中使用者會受指定許多工作，有效補救大型車隊的問題可能會成為一項挑戰。

為了簡化安全性設定錯誤的補救措施，以及能夠快速修復大量資源的建議並改善您的安全分數，請使用「快速修正」補救。

此作業可讓您選取要套用補救的資源，並啟動將代表您設定的補救動作。

快速修正今日已在 [資訊安全中心建議] 頁面中正式推出。

請查看[安全性建議參考指南](recommendations-reference.md)中的哪些建議已啟用快速修正。


### <a name="scan-container-images-for-vulnerabilities-preview"></a>掃描容器映像的弱點 (預覽)

Azure 資訊安全中心現在可以掃描 Azure Container Registry 中的容器映像是否有弱點。

映像掃描的運作方式是剖析容器映像檔案，然後檢查是否有任何已知的弱點 (由 Qualys 提供技術支援)。

將新的容器映像推送至 Azure Container Registry 時，會自動觸發掃描本身。 找到的弱點將會呈現為資訊安全中心建議，並包含在 Azure 安全分數中，以及如何加以修補來減少其所允許受攻擊面的相關資訊。


### <a name="additional-regulatory-compliance-standards-preview"></a>其他法規合規性標準 (預覽)

[法規合規性] 儀表板可讓您深入解析以資訊安全中心評量為基礎的合規性狀態。 儀表板會顯示您的環境如何符合特定法規標準及產業基準所指定的控制項和需求，並提供如何解決這些需求的規範性建議。

[法規合規性] 儀表板目前支援四個內建標準：Azure CIS 1.1.0、PCI-DSS、ISO 27001 和 SOC-TSP。 我們現在宣佈其他支援標準的公開預覽版本：NIST SP 800-53 R4、SWIFT CSP CSCF v2020、加拿大聯邦 PBMM 和英國官方搭配英國 NHS。 我們也會發行 Azure CIS 1.1.0 的更新版本，涵蓋來自標準和增強擴充性的更多控制項。

[深入了解如何在您的法規合規性儀表板中自訂一組標準](update-regulatory-compliance-packages.md)。


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Azure Kubernetes Service 的威脅防護 (預覽)

Kubernetes 很快就成為在雲端中部署及管理軟體的新標準。 少數人都有豐富的 Kubernetes 經驗，且大部分都只著重於一般工程和系統管理而忽略安全性層面。 必須謹慎設定 Kubernetes 環境以確保安全，因此沒有任何針對容器受攻擊面門戶保持開啟，不讓攻擊者有機可乘。 資訊安全中心會將其在容器空間中的支援擴充至 Azure 中最快速成長的服務之一 - Azure Kubernetes Service (AKS)。

此公開預覽版本中的新功能包括：

- **探索與可見度** - 在資訊安全中心註冊的訂用帳戶內持續探索受控 AKS 執行個體。
- **安全分數建議** -可操作的專案，以協助客戶遵守 AKS 的安全性最佳作法，並提高其安全分數。 建議事項包括「應使用角色型存取控制來限制對 Kubernetes Service 叢集的存取」等專案。
- **威脅偵測** - 主機和以叢集為基礎的分析，例如「偵測到特殊權限的容器」。


### <a name="virtual-machine-vulnerability-assessment-preview"></a>虛擬機器弱點評估 (預覽)

安裝在虛擬機器中的應用程式通常會有可能導致虛擬機器缺口的弱點。 我們宣佈安全中心標準層包含虛擬機器的內建弱點評定，無須額外付費。 弱點評量是由 Qualys 提供且處於公開預覽，可讓您持續掃描虛擬機器上所有已安裝的應用程式，以找出易受攻擊的應用程式，並在資訊安全中心入口網站的體驗中呈現結果。 資訊安全中心會負責所有部署作業，因此使用者無須執行任何額外的工作。 接下來，我們打算提供弱點評估選項，以支援客戶的獨特商務需求。

[深入了解 Azure 虛擬機器的弱點評量](deploy-vulnerability-assessment-vm.md)。


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure 虛擬機器上 SQL 伺服器的進階資料安全性 (預覽)

Azure 資訊安全中心對於在 IaaS VM 上執行的 SQL DB 進行威脅防護和弱點評估支援現為預覽狀態。

[弱點評估](../azure-sql/database/sql-vulnerability-assessment.md)是容易設定的服務，可探索、追蹤及協助您修復潛在的資料庫弱點。 其可讓您在 Azure 安全分數中看見安全性狀態，且包含解決安全性問題和增強資料庫防護性的步驟。

[進階威脅防護](../azure-sql/database/threat-detection-overview.md)偵測到了異常活動，這表示有不尋常及可能有害的活動試圖存取或惡意探索您的 SQL 伺服器。 其會持續監視您的資料庫是否有可疑的活動，並在發現異常資料庫存取模式時，提供動作導向的安全性警示。 這些警示會提供可疑活動的詳細資料，以及調查與降低威脅的建議動作。


### <a name="support-for-custom-policies-preview"></a>支援自訂原則 (預覽)

Azure 資訊安全中心現在支援自訂原則 (預覽階段)。

我們的客戶一直以來都想要根據他們在 Azure 原則中建立的原則，使用自己的安全性評量來擴充其目前在資訊安全中心的安全性評量涵蓋範圍。 透過自訂原則的支援，現在就可以做到這一點。

資訊安全中心建議體驗、安全分數和法規合規性標準儀表板中將會包含這些新原則。 透過自訂原則的支援，您現在可以在 Azure 原則中建立自訂方案，然後將其新增為資訊安全中心內的原則，並將其視覺化為建議。


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>使用適用於社區及合作夥伴的平台來擴充 Azure 資訊安全中心的涵蓋範圍

使用資訊安全中心接收來自 Microsoft 的建議，還能接收來自其他合作夥伴 (例如 Check Point、Tenable 和 CyberArk) 的現有解決方案建議，並且將獲得提供更多整合。  資訊安全中心的簡單上線流程可將您現有的解決方案連線到資訊安全中心，讓您能夠在單一位置檢視您的安全性狀態建議、執行整合的報告，並針對內建及合作夥伴建議來運用所有資訊安全中心的功能。 您也可以將資訊安全中心建議匯出至合作夥伴產品。

[深入了解 Microsoft 情報安全性聯盟](https://www.microsoft.com/security/partnerships/intelligent-security-association)。



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>與建議和警示匯出的進階整合 (預覽)

為了在資訊安全中心之上啟用企業層級案例，現在可以在 Azure 入口網站或 API 以外的其他地方，取用資訊安全中心警示和建議。 這些可以直接匯出至事件中樞和 Log Analytics 工作區。 以下是您可以針對這些新功能建立的幾個工作流程：

- 您可以透過 [匯出至 Log Analytics 工作區]，使用 Power BI 來建立自訂儀表板。
- 使用 [匯出至事件中樞] 時，您可以將資訊安全中心警示和建議匯出至您的協力廠商 SIEM、即時的協力廠商解決方案，或 Azure 資料總管。


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>從 Windows 管理中心將內部部署伺服器上線到資訊安全中心 (預覽)

Windows 管理中心是 Windows Server 的管理入口網站，這些伺服器不會部署在提供其數個 Azure 管理功能 (例如備份和系統更新) 的 Azure 中。 我們最近新增了將這些非 Azure 伺服器上線的功能，可直接從 Windows 管理中心體驗受到 ASC 保護。

使用這項新體驗時，使用者要將 WAC 伺服器上線至 Azure 資訊安全中心，並直接在 Windows 管理中心體驗中檢視其安全性警示和建議。


## <a name="september-2019"></a>2019 年 9 月

9 月的更新包括：

 - [使用自適性應用程式控制改進項目來管理規則](#managing-rules-with-adaptive-application-controls-improvements)
 - [使用 Azure 原則控制容器安全性建議](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>使用自適性應用程式控制改進項目來管理規則

已改善使用自適性應用程式控制來管理虛擬機器規則的體驗。 Azure 資訊安全中心的自適性應用程式控制可協助您控制哪些應用程式可以在您的虛擬機器上執行。 除了規則管理的一般改進之外，還有新的優點可讓您控制在新增規則時，將會保護哪些檔案類型。

[深入了解自適性應用程式控制](security-center-adaptive-application.md)。


### <a name="control-container-security-recommendation-using-azure-policy"></a>使用 Azure 原則控制容器安全性建議

您現在可以透過 Azure 原則，啟用或停用在容器安全性中補救弱點的 Azure 資訊安全中心建議。

若要檢視已啟用的安全性原則，請從資訊安全中心開啟 [安全性原則] 頁面。


## <a name="august-2019"></a>2019 年 8 月

8 月的更新包括：

 - [Azure 防火牆的 Just-in-time (JIT) VM 存取](#just-in-time-jit-vm-access-for-azure-firewall)
 - [單鍵補救以提升您的安全性狀態 (預覽)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [跨租用戶管理](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Azure 防火牆的 Just-in-time (JIT) VM 存取 

適用於 Azure 防火牆的 Just-in-time (JIT) VM 存取現已正式推出。 除了 NSG 保護的環境之外，您還可以使用其來保護 Azure 防火牆保護的環境。

JIT VM 存取會使用您的 NSG 和 Azure 防火牆規則，只在需要時提供對 VM 的受控制存取，藉此減少網路體積型攻擊的風險。

當您為 VM 啟用 JIT 時，會建立一個原則來決定要保護的連接埠、連接埠維持開啟多久，以及可存取這些連接埠的已核准 IP 位址。 此原則可協助您隨時控制使用者在要求存取時可以執行的動作。

要求會記錄在 Azure 活動記錄中，因此您可以輕鬆地監視及稽核存取。 Just-In-Time 也可協助您快速識別已啟用 JIT 的現有 VM，以及建議使用 JIT 的 VM。

[深入了解 Azure 防火牆](../firewall/overview.md)。


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>單鍵補救以提升您的安全性狀態 (預覽)

安全分數是一個可協助您評估工作負載安全性狀態的工具。 其會檢閱您的安全性建議，並為您排列這些建議的優先順序，讓您了解要先執行哪些建議。 這可協助您找出最嚴重的安全性弱點，以優先安排調查。

為了簡化安全性設定錯誤的補救措施，並協助您快速改善您的安全分數，我們新增了一項新功能，可讓您只需按一下，就能在大量資源上補救建議。

此作業可讓您選取要套用補救的資源，並啟動將代表您設定的補救動作。

請查看[安全性建議參考指南](recommendations-reference.md)中的哪些建議已啟用快速修正。


### <a name="cross-tenant-management"></a>跨租用戶管理

資訊安全中心現在支援跨租用戶管理案例做為 Azure Lighthouse 的一部分。 這可讓您在資訊安全中心中獲得可見度，並管理多個租用戶的安全性狀態。 

[深入了解跨租用戶管理體驗](security-center-cross-tenant-management.md)。


## <a name="july-2019"></a>2019 年 7 月

### <a name="updates-to-network-recommendations"></a>網路建議的更新

Azure 資訊安全中心 (ASC) 已啟動新的網路建議，並改進了一些現有的建議。 現在，使用資訊安全中心可確保您的資源獲得更高的網路保護。 

[深入了解網路建議](recommendations-reference.md#recs-networking)。


## <a name="june-2019"></a>2019 年 6 月

### <a name="adaptive-network-hardening---generally-available"></a>自適性網路強化 - 正式推出

在公用雲端中執行之工作負載的其中一個最大受攻擊面，就是與公用網際網路之間的連線。 我們的客戶發現，很難知道應採用哪些網路安全性群組 (NSG) 規則，以確保 Azure 工作負載僅供必要的來源範圍使用。 透過這項功能，資訊安全中心會了解 Azure 工作負載的網路流量和連線模式，並為網際網路對應的虛擬機器提供 NSG 規則建議。 這可協助我們的客戶更有效地設定其網路存取原則，並限制其暴露於攻擊的風險。 

[深入了解自適性網路強化](security-center-adaptive-network-hardening.md)。