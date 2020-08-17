---
title: Azure 資訊安全中心版本資訊
description: Azure 資訊安全中心新增項目和變更內容的相關描述。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: memildin
ms.openlocfilehash: a635f32c5e5f1132d70fb503058267c8b33beb7c
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272055"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 資訊安全中心的新功能

Azure 安全性持續再開發改良。 為了讓您隨時掌握最新的開發訊息，此頁面提供下列相關資訊：

- 新功能
- 錯誤修正
- 已被取代的功能

此頁面會定期更新，因此請時常瀏覽。 如果想要尋找超過 6 個月的項目，請前往[Azure 資訊安全中心內新功能的封存](release-notes-archive.md)。


## <a name="august-2020"></a>2020 年 8 月

8月更新包括：

- [資產清查-您資產安全性狀態的強大新觀點](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [已新增服務主體建議](#service-principals-recommendation-added)
- [Vm 上的弱點評定-合併的建議和原則](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>資產清查-您資產安全性狀態的強大新觀點

安全性中心的資產清查提供了一種方式，可讓您查看已連線到「安全性中心」之資源的安全性狀態。

安全中心會定期分析 Azure 資源的安全性狀態，以找出潛在的安全性弱點。 接著，它會提供有關如何補救這些弱點的建議。 當任何資源有未處理的建議時，它們會出現在清查中。

您可以使用 view 和其篩選器來探索安全性狀態資料，並根據您的發現採取進一步的動作。

深入瞭解 [資產清查](asset-inventory.md)。


### <a name="service-principals-recommendation-added"></a>已新增服務主體建議

使用管理憑證管理其訂用帳戶的新安全中心客戶建議。

建議使用 **服務主體來保護您的訂用帳戶，而不是管理憑證** 建議您使用服務主體或 Azure Resource Manager 來更安全地管理您的訂閱。 

### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Vm 上的弱點評定-合併的建議和原則

安全性中心會檢查您的 Vm，以偵測它們是否正在執行弱點評估解決方案。 如果找不到任何弱點評定解決方案，則「安全性中心」會提供建議以簡化部署。

找到弱點時，「安全性中心」會提供建議，以摘要說明您可以視需要調查和補救的結果。

為了確保所有使用者都能擁有一致的體驗，不論他們使用的掃描器類型為何，我們都已整合四個建議至下列兩個：

|整合建議|變更描述|
|----|:----|
|**應在您的虛擬機器上啟用弱點評定解決方案**|取代下列兩個建議：<br> **•** 在 Qualys 支援的虛擬機器上啟用內建弱點評定解決方案 ( (現在已被取代，)  (包含在標準層中) <br> **•** 弱點評定解決方案應該安裝在您的虛擬機器上 (現在已淘汰)  (標準和免費層) |
|**應補救您虛擬機器中的弱點**|取代下列兩個建議：<br>**•** 補救在虛擬機器上找到的弱點 (由 Qualys) 支援 (現在已淘汰) <br>**•** 弱點評估解決方案應補救 (現在已淘汰的弱點) |
|||

現在，您將使用相同的建議來部署安全中心的弱點評定延伸模組或私用授權解決方案 ( "BYOL" ) 來自合作夥伴，例如 Qualys 或 Rapid7。

此外，當發現弱點並回報給「安全性中心」時，單一建議會將您的結果回報給您，而不論發現它們的弱點評估解決方案為何。

#### <a name="updating-dependencies"></a>更新相依性

如果您有參考先前建議或原則金鑰/名稱的腳本、查詢或自動化，請使用下清單格來更新參考：

##### <a name="before-august-2020"></a>2020年8月之前

|建議|影響範圍|
|----|:----|
|**在 Qualys 支援的虛擬機器上啟用內建弱點評定解決方案 () **<br>機碼：550e890b-e652-4d22-8274-60b3bdb24c63|內建|
|**補救在您的虛擬機器上發現的弱點 (Qualys 技術提供)**<br>機碼：1195afff-c881-495e-9bc5-1486211ae03f|內建|
|**虛擬機器上應安裝弱點評定解決方案**<br>機碼：01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**弱點評量解決方案應修復弱點**<br>機碼：71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|原則|影響範圍|
|----|:----|
|**虛擬機器上應啟用弱點評定**<br>原則識別碼：501541f7-f7e7-4cd6-868c-4190fdad3ac9|內建|
|**弱點評估解決方案應補救的弱點**<br>原則識別碼：760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>自2020年8月

|建議|影響範圍|
|----|:----|
|**應在您的虛擬機器上啟用弱點評定解決方案**<br>機碼： ffff0522-1e88-47fc-8382-2a80ba848f5d|內建 + BYOL|
|**應補救您虛擬機器中的弱點**<br>機碼：1195afff-c881-495e-9bc5-1486211ae03f|內建 + BYOL|
||||

|原則|影響範圍|
|----|:----|
|[**虛擬機器上應啟用弱點評定**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>原則識別碼：501541f7-f7e7-4cd6-868c-4190fdad3ac9 |內建 + BYOL|
||||



## <a name="july-2020"></a>2020 年 7 月

7月更新包括：
- [虛擬機器的弱點評定現在適用于非 marketplace 映射](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Azure 儲存體的威脅防護已擴充為包含 Azure 檔案儲存體和 Azure Data Lake Storage Gen2 (預覽) ](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [啟用威脅防護功能的八個新建議](#eight-new-recommendations-to-enable-threat-protection-features)
- [容器安全性改進-更快登錄掃描和重新整理檔](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [以新的建議和路徑規則中的萬用字元支援來更新適應性應用程式控制項](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [適用于 SQL advanced data security 的六個原則已淘汰](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>虛擬機器的弱點評定現在適用于非 marketplace 映射

部署弱點評估解決方案時，安全性中心先前在部署之前執行過驗證檢查。 檢查是確認目的地虛擬機器的 marketplace SKU。 

在此更新中，已移除檢查，您現在可以將弱點評定工具部署至「自訂」 Windows 和 Linux 機器。 自訂映射是您從 marketplace 預設值修改的映射。

雖然您現在可以在更多部電腦上部署 Qualys) 所提供的整合式弱點評定延伸模組 (，但只有在您使用 [部署 Qualys 內建弱點掃描器](built-in-vulnerability-assessment.md#deploying-the-qualys-built-in-vulnerability-scanner)時所列的作業系統，才能使用支援。

深入瞭解 [僅)  (標準層的虛擬機器整合式弱點掃描器 ](built-in-vulnerability-assessment.md)。

深入瞭解如何在 [部署夥伴弱點掃描解決方案](partner-vulnerability-assessment.md)的 Qualys 或 Rapid7 中，使用您自己的私用授權弱點評估解決方案。


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Azure 儲存體的威脅防護已擴充為包含 Azure 檔案儲存體和 Azure Data Lake Storage Gen2 (預覽) 

Azure 儲存體的威脅防護會偵測 Azure 儲存體帳戶可能有害的活動。 當安全性中心偵測到嘗試存取或惡意探索您的儲存體帳戶時，會顯示警示。 

無論您的資料是儲存為 blob 容器、檔案共用或資料 lake，都可以受到保護。 

深入瞭解 [Azure 儲存體的威脅防護](threat-protection.md#threat-protection-for-azure-storage-)。




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>啟用威脅防護功能的八個新建議

新增了八個新的建議，以提供簡單的方式來為下列資源類型啟用 Azure 資訊安全中心的威脅防護功能：虛擬機器、App Service 方案、Azure SQL Database 伺服器、電腦上的 SQL server、Azure 儲存體帳戶、Azure Kubernetes Service 叢集、Azure Container Registry 登錄和 Azure Key Vault 保存庫。

新的建議如下：

- **Azure SQL Database 伺服器應啟用進階資料安全性**
- **應在機器上的 SQL 伺服器啟用進階資料安全性**
- **應在 Azure App Service 方案上啟用進階威脅防護**
- **應在 Azure Container Registry 登錄上啟用進階威脅防護**
- **應在 Azure Key Vault 保存庫上啟用進階威脅防護**
- **應在 Azure Kubernetes Service 叢集上啟用進階威脅防護**
- **應在 Azure 儲存體帳戶上啟用進階威脅防護**
- **應在虛擬機器上啟用 Advanced 威脅防護**

這些新的建議屬於「 **啟用 Advanced 威脅防護** 」安全性控制措施。

這些建議也包含快速修正功能。 

> [!IMPORTANT]
> 補救任何這些建議將會產生保護相關資源的費用。 如果您目前的訂用帳戶中有相關的資源，則會立即開始這些費用。 或未來，如果您稍後再加入它們。
> 
> 例如，如果您的訂用帳戶中沒有任何 Azure Kubernetes Service 叢集，而您啟用了威脅防護，將不會產生任何費用。 未來，如果您在相同的訂用帳戶上新增叢集，該叢集將會自動受到保護，並會在該時間開始收費。

在 [ [安全性建議參考] 頁面](recommendations-reference.md)中，深入瞭解上述各項。

深入瞭解 [Azure 資訊安全中心中的威脅防護](https://docs.microsoft.com/azure/security-center/threat-protection)。




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>容器安全性改進-更快登錄掃描和重新整理檔

在容器安全性網域中持續投資的過程中，我們很高興能在安全中心動態掃描儲存在 Azure Container Registry 中的容器映射時，有顯著的效能改進。 掃描通常會在大約兩分鐘內完成。 在某些情況下，最多可能需要15分鐘的時間。

為了改善 Azure 資訊安全中心的容器安全性功能的清楚和指引，我們也已重新整理容器安全性檔案頁面。 

若要深入瞭解資訊安全中心的容器安全性，請閱讀下列文章：

- [簡介安全性中心的容器安全性功能](https://docs.microsoft.com/azure/security-center/container-security)
- [與 Azure Container Registry 整合的詳細資料](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)
- [與 Azure Kubernetes Service 整合的詳細資料](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)
- [如何掃描您的登錄並強化您的 Docker 主機](https://docs.microsoft.com/azure/security-center/monitor-container-security)
- [Azure Kubernetes Service 叢集威脅防護功能的安全性警示](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)
- [Azure Kubernetes Service 主機威脅防護功能的安全性警示](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)
- [容器的安全性建議](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>以新的建議和路徑規則中的萬用字元支援來更新適應性應用程式控制項

適應性應用程式控制功能已收到兩項重大更新：

* 新的建議會識別之前未允許的潛在合法行為。 您的彈性 **應用程式控制原則中的允許清單規則應該更新**為新的建議、提示您將新的規則加入至現有的原則，以減少自我調整應用程式控制違規警示中的誤報數目。

* 路徑規則現在支援萬用字元。 在此更新中，您可以使用萬用字元設定允許的路徑規則。 有兩個支援的案例：

    * 在路徑結尾使用萬用字元，以允許此資料夾和子資料夾內的所有可執行檔

    * 在路徑中間使用萬用字元來啟用具有變更資料夾名稱的已知可執行檔名稱 (例如，具有已知可執行檔的個人使用者資料夾、自動產生的資料夾名稱等) 。


[深入了解自適性應用程式控制](security-center-adaptive-application.md)。



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>適用于 SQL advanced data security 的六個原則已淘汰

與 SQL 電腦 advanced data security 相關的六個原則即將淘汰：

- 在 SQL 受控實例的 [advanced data security] 設定中，Advanced 威脅防護類型應設定為 [全部]
- 在 SQL server advanced data security 設定中，Advanced 威脅防護類型應設定為 [全部]
- SQL 受控執行個體的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址
- SQL 伺服器的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址
- 應在 SQL 受控執行個體進階資料安全性設定中，啟用傳給系統管理員和訂用帳戶擁有者的電子郵件通知
- 應在 SQL 伺服器進階資料安全性設定中啟用傳給系統管理員和訂用帳戶擁有者的通知

深入瞭解 [內建原則](security-center-policy-definitions.md)。





## <a name="june-2020"></a>2020 年 6 月

6月更新包括：
- [安全分數 API (預覽) ](#secure-score-api-preview)
- [SQL 電腦的 Advanced data security (Azure、其他雲端和內部內部部署)  (preview) ](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [有兩個新的建議可將 Log Analytics 代理程式部署至 Azure Arc 電腦 (preview) ](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [可大規模建立連續匯出和工作流程自動化設定的新原則](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [使用 Nsg 保護非網際網路對應虛擬機器的新建議](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [啟用威脅防護和 advanced data security 的新原則](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>安全分數 API (預覽) 

您現在可以透過 [安全分數 API](https://docs.microsoft.com/rest/api/securitycenter/securescores/) 來存取您的分數 (目前為預覽) 。 API 方法可讓您彈性地查詢資料，並在一段時間內建立您自己的安全分數報告機制。 例如，您可以使用 **安全分數** API 來取得特定訂用帳戶的分數。 此外，您可以使用 **安全分數控制項** API 來列出安全性控制項和您的訂用帳戶的目前分數。

如需使用安全分數 API 進行外部工具的範例，請參閱 [GitHub 社區的安全分數區域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。

深入瞭解 [Azure 資訊安全中心中的安全分數和安全性控制](secure-score-security-controls.md)措施。



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>SQL 電腦的 Advanced data security (Azure、其他雲端和內部內部部署)  (preview) 

Azure 資訊安全中心的 SQL 機器 advanced data Security 現在可保護裝載于 Azure、其他雲端環境中的 SQL 伺服器，甚至是內部部署機器。 這可延伸 Azure 原生 SQL Server 的保護，以完整支援混合式環境。

Advanced data security 為您的 SQL 電腦提供了弱點評定和先進的威脅防護，不論它們位於何處。

安裝套裝程式含兩個步驟：

1. 將 Log Analytics 代理程式部署至您 SQL Server 的主機電腦，以提供 Azure 帳戶的連線。

1. 在 [安全性中心的定價和設定] 頁面中啟用選用套件組合。

深入瞭解 [SQL 電腦的 advanced data security](security-center-iaas-advanced-data.md)。



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>有兩個新的建議可將 Log Analytics 代理程式部署至 Azure Arc 電腦 (preview) 

新增了兩個新的建議，可協助您將 [Log Analytics 代理程式](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) 部署到您的 Azure Arc 機，並確保其受 Azure 資訊安全中心保護：

- **Log Analytics 代理程式應該安裝在以 Windows 為基礎的 Azure Arc 電腦上 (Preview) **
- **Log Analytics 代理程式應該安裝在以 Linux 為基礎的 Azure Arc 電腦上 (Preview) **

這些新的建議會出現在與現有 (相關) 建議的四個安全性控制項中， **監視代理程式應該安裝在您的電腦上**：修復安全性設定、套用適應性應用程式控制、套用系統更新，以及啟用 endpoint protection。

這些建議也包含快速的修正功能，以協助加速部署流程。 

在 [ [計算和應用程式建議](recommendations-reference.md#recs-computeapp) ] 資料表中深入瞭解這兩個新的建議。

深入瞭解 Azure 資訊安全中心如何使用 [Log Analytics 代理程式](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent)中的代理程式？。

深入瞭解 [Azure Arc 電腦的擴充](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal)功能。



### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>可大規模建立連續匯出和工作流程自動化設定的新原則

將您組織的監視和事件回應程式自動化，可大幅改善調查和減輕安全性事件所花的時間。

若要在組織中部署自動化設定，請使用這些內建的「DeployIfdNotExist」 Azure 原則來建立及設定 [連續匯出](continuous-export.md) 和 [工作流程自動化](workflow-automation.md) 程式：

您可以在 Azure 原則中找到這些原則：


|目標  |原則  |原則識別碼  |
|---------|---------|---------|
|連續匯出至事件中樞|[為 Azure 資訊安全中心警示與建議部署「匯出至事件中樞」](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|連續匯出至 Log Analytics 工作區|[為 Azure 資訊安全中心警示與建議部署「匯出至 Log Analytics 工作區」](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|安全性警示的工作流程自動化|[為 Azure 資訊安全中心警示部署工作流程自動化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|安全性建議的工作流程自動化|[為 Azure 資訊安全中心建議部署工作流程自動化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

開始使用 [工作流程自動化範本](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)。

深入瞭解如何在 [連續匯出 Azure 資訊安全中心警示和建議的原則中，](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745)使用這兩個匯出原則。


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>使用 Nsg 保護非網際網路對應虛擬機器的新建議

「實行安全性最佳作法」安全性控制現在包含下列新的建議：

- **應使用網路安全性群組保護非網際網路對應的虛擬機器**

現有的建議、 **網際網路對應的虛擬機器應該使用網路安全性群組來保護，但無法**區別網際網路對向和非網際網路對應的 vm。 這兩種情況下，如果未將 VM 指派給網路安全性群組，就會產生高嚴重性的建議。 這種新的建議可分隔非網際網路對應的電腦，以減少誤報，並避免不必要的高嚴重性警示。

深入瞭解 [網路建議](recommendations-reference.md#recs-network) 表。




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>啟用威脅防護和 advanced data security 的新原則

以下的新原則已新增至 ASC 預設方案，其設計目的是要協助針對相關的資源類型啟用威脅防護或 advanced data security。

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

深入瞭解 [Azure 資訊安全中心中的威脅防護](https://docs.microsoft.com/azure/security-center/threat-protection)。





## <a name="may-2020"></a>2020 年 5 月

中的更新可能包括：
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

深入瞭解[資訊安全中心的虛擬機器整合式弱點評估](built-in-vulnerability-assessment.md)。



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Just-In-Time (JIT) 虛擬機器 (VM) 存取的變更

資訊安全中心包含可保護 VM 管理連接埠的選用功能。 這可讓您防禦最常見的暴力密碼破解攻擊形式。

此更新會對這項功能進行下列變更：

- 建議您在 VM 上啟用 JIT 的建議已重新命名。 以前是「Just-In-Time 網路存取控制應套用在虛擬機器上」，現在是：「應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠」。

- 只有在有開啟的管理埠時，才會觸發建議。

深入瞭解 [JIT 存取功能](security-center-just-in-time.md)。


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>自訂建議已移至個別的安全性控制

增強的安全分數所引進的一個安全性控制是「實行安全性最佳作法」。 針對您的訂閱所建立的任何自訂建議都會自動放在該控制項中。 

為了讓您更輕鬆地找到您的自訂建議，我們已將其移至專用的安全性控制「自訂建議」。 此控制項不會影響您的安全分數。

深入瞭解[在 Azure 資訊安全中心之內的增強型安全分數 (預覽)](secure-score-security-controls.md) 中出現的安全性控制項。


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>在控制項中或以一般清單的方式，加入查看建議的切換

安全性控制是相關安全性建議的邏輯群組， 會反映您的易受攻擊面。 控制項是一組安全性建議，其中包含有助於執行建議的指示。

若要立即查看您的組織如何保護每個個別的受攻擊面，請參閱每個安全性控制的分數。

根據預設，您的建議會顯示在 [安全性] 控制項中。 在此更新中，您也可以將它們顯示為清單。 若要按受影響資源的健全狀況狀態排序，以顯示為方便查看的簡明清單，請使用新的切換功能 [依據控制項分組]。 切換功能位在入口網站中的清單上方。

安全性控制措施和此切換功能皆屬於新的安全分數體驗。 請記得在入口網站中傳送您的意見反應給我們。

深入瞭解[在 Azure 資訊安全中心之內的增強型安全分數 (預覽)](secure-score-security-controls.md) 中出現的安全性控制項。

![建議的 [依控制項分組] 開關](\media\secure-score-security-controls\recommendations-group-by-toggle.gif)

### <a name="expanded-security-control-implement-security-best-practices"></a>擴充的安全性控制「實作安全性最佳做法」 

增強的安全分數所引進的一個安全性控制是「實行安全性最佳作法」。 當此控制項中有建議時，不會影響到安全分數。 

在此更新中，有三個建議已移出原先放在其中的控制項，並新增至此最佳做法控制項。 我們已經採取此步驟，是因為我們判斷這三個建議的風險低於一開始所考慮的風險。

此外，也引進了兩個新的建議，並將其新增至這個控制項。

這三個已移動的建議如下：

- **應在您訂閱上具有讀取權限的帳戶上啟用 MFA** (原本在「啟用 MFA」控制項中)
- **具有讀取權限的外部帳戶應從您的訂閱**中移除 (原本是在「管理存取權和權限」控制項中)
- **您的訂閱最多可指定 3 位擁有者** (原本是在「管理存取權和權限」控制項中)

新增至控制項的兩個新建議如下：

- 您**應在 windows 虛擬機器上安裝來賓設定延伸模組 (Preview) ** -使用[Azure 原則來賓](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)設定可讓您在虛擬機器中， (僅限 Windows) 的伺服器和應用程式設定。

- **您應在您的電腦上啟用 Windows Defender 惡意探索防護 (Preview) ** Windows Defender 惡意探索防護會利用 Azure 原則來賓設定代理程式。 「惡意探索防護」有四個元件，設計用來鎖定裝置，使其免於遭受惡意程式碼攻擊的各種攻擊和封鎖行為，同時讓企業能夠平衡本身的安全性風險和生產力需求 (僅限 Windows)。

若要深入瞭解 Windows Defender 惡意探索防護，請參閱[建立及部署惡意探索防護原則](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)。

深入瞭解增強的安全分數中的安全性控制 [ (預覽) ](secure-score-security-controls.md)。



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>具有自訂中繼資料的自訂原則現已正式推出

資訊安全中心建議體驗、安全分數和法規合規性標準儀表板中現在包含自訂原則現。 這項功能現已正式推出，可讓您在資訊安全中心中擴充組織的安全性評估涵蓋範圍。 

在 Azure 原則中建立自訂方案、新增原則並將其上線至 Azure 資訊安全中心，並將其視覺化為建議。

我們現在也新增了編輯自訂建議中繼資料的選項。 中繼資料選項包括嚴重性、補救步驟、威脅資訊等等。  

深入瞭解[使用詳細資料來增強您的自訂建議](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information)。



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>損毀傾印分析功能移轉至無檔案攻擊偵測 

我們會將 Windows 損毀傾印分析 (CDA) 偵測功能整合到[無檔案攻擊偵測](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless)。 無檔案攻擊偵測分析會針對 Windows 機器引進下列安全性警示的改良版本：探索到程式碼插入、偵測到偽裝的 Windows 模組、發現的程式碼，以及偵測到可疑的程式碼片段。

此轉換的一些優點：

- **主動式和及時的惡意程式碼偵測** -CDA 方法，其中涉及等候損毀發生，然後執行分析以找出惡意成品。 使用無檔案攻擊偵測可在執行中時，為記憶體內部威脅提供主動式識別。 

- **擴充的警示** - 來自無檔案攻擊偵測的安全性警示包含無法從 CDA 取得的擴充，例如使用中的網路連線資訊。 

- **警示匯總** - 當 CDA 在單一損毀傾印中偵測到多個攻擊模式時，它會觸發多個安全性警示。 無檔案攻擊偵測會將所有已識別的攻擊模式從相同的程式合併到單一警示，而不需要相互關聯多個警示。

- **降低 Log Analytics 工作區的需求** - 包含潛在敏感性資料的損毀傾印不會再上傳至您的 Log Analytics 工作區。



## <a name="april-2020"></a>2020 年 4 月

四月的更新包括：
- [動態合規性套件現已正式推出](#dynamic-compliance-packages-are-now-generally-available)
- [Azure 資訊安全中心免費層中現在已包含身分識別建議](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>動態合規性套件現已正式推出

Azure 資訊安全中心的法規合規性儀表板現在包含**動態相容性套件** (現已正式運作)，以追蹤額外的產業和法規標準。

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

深入瞭解[身分識別與存取建議](recommendations-reference.md#recs-identity)。

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

深入瞭解[建立邏輯應用程式](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。


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

深入瞭解 [Azure Kubernetes Service 與資訊安全中心整合](azure-kubernetes-service-integration.md)。

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

