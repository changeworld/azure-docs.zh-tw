---
title: 常見問題集 (FAQ)
description: 本文將回答有關 Azure 範疇的常見問題。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: eca0b9986c4da30adeeb02bc3d90d1e3d2892df7
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552000"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>關於 Azure 範疇的常見問題 (常見問題) 

## <a name="overview"></a>概觀

許多組織都缺乏對其資料的全面瞭解。 瞭解有哪些資料存在、資料所在位置，以及如何尋找及存取相關資料，是一項挑戰。 資料缺乏內容，例如歷程、分類和完整的中繼資料，讓商務使用者難以搜尋正確的資料並適當地使用該資料。 因此，只有一小部分收集的資料會用來通知業務決策。 最後，識別資料安全性問題和保護敏感性資料並不一致。 它需要持續的時間和精力，尤其是在維護資料的靈活性時。

Azure 範疇是一種資料治理解決方案。 它可協助客戶深入瞭解其所有資料，同時維持對其使用的控制權。 有了 Azure 範疇，組織就能探索及策展資料。 他們可取得資料資產的深入解析，並集中控管資料的存取權。

## <a name="purpose-of-this-faq"></a>此常見問題的目的

此常見問題會回答客戶和現場團隊經常詢問的常見問題。 其目的是要說明 Azure 範疇和相關解決方案的相關問題，例如 Azure 資料目錄 (ADC) Gen 2 (已淘汰的) 和 Azure 資訊保護。

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>中繼資料掃描和分類可用的來源類型有哪些？

|Azure|非 Azure|
|---------|---------|
|Azure Blob 儲存體|Power BI|
|Azure Synapse Analytics (SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata (2020) 的結尾提供|
|Azure SQL 受控執行個體|2020) 結尾提供 SAP ECC (|
|Azure 資料總管|2020) 結尾提供 SAP S/4 HANA (|
|Azure Data Lake Storage Gen1|2020) 結尾提供的 Hive 中繼存放區 (|
|Azure Data Lake Storage Gen2|--|
|Azure 檔案|--|
|Azure SQL Database|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>我們可以連接哪些資料系統/處理器，並取得歷程記錄？

|資料系統/處理器 
|---------
|Azure Data Factory：複製活動、資料流程活動 
|自訂歷程   
|Azure Data Share   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>ADC Gen 2、Azure 資訊保護和 Azure 範疇如何相關？

Azure 範疇最初是以 ADC Gen 2 的形式開始，但因為放寬了在範圍內。 它現在將 ADC Gen 2 的先進目錄功能與 Azure 資訊保護的資料分類、標記和合規性原則的強制功能結合在一起。 現今，它會更密切地配合資料治理的廣泛產業定義。

### <a name="what-happens-to-customers-using-adc-gen-1"></a>使用 ADC Gen 1 的客戶會發生什麼事？

Azure 範疇是 Microsoft 的目錄解決方案空間中所有產品創新的焦點。 ADC Gen 1 將繼續受到支援。

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>客戶是否可以在相同的訂用帳戶中有多個 Azure 範疇帳戶？

是的，我們針對每個訂用帳戶和每個租使用者支援許多 Azure 範疇帳戶。

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>我可以平行執行 ADC Gen 1 和 Azure 範疇嗎？

是。 兩者都是獨立的服務。

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>如何? 將現有的 ADC Gen 1 資料資產遷移至 Azure 範疇？

使用 Azure 範疇 Api 從 ADC Gen 1 解壓縮，並內嵌至 Azure 範疇。 針對詞彙，我們支援以 CSV 為基礎的大量工具。

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>如何? 使用 Azure 範疇為 SQL 資料表加密敏感性資料？

資料加密是在資料來源層級進行。 Azure 範疇只會儲存中繼資料。 它不會預覽資料。

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>ADC Gen 2 的所有功能是否存在於 Azure 範疇中？

是。

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>詞彙和分類之間有何差異？

詞彙使用命名慣例，後面接著資料的非技術/商務使用者，也稱為資料取用者。 這些類型的人員是商務分析師或資料科學家，他們使用 Azure 範疇根據商務使用量來搜尋特定類型的資料。 例如，供應鏈分析師可能需要搜尋 *SKU 類型* 和出 *貨詳細資料* 的條款。 他們會在詞彙中搜尋這些詞彙以找出相關的資料。
分類是套用至資料表、資料行或檔案層級之資料資產的標記，用以識別資產中有哪些資料。 您可以根據所找到的資料類型，自動或手動套用分類。 通常，您會使用分類標籤來識別資產是否包含機密資料，以及可能的敏感資料類型。

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Azure 範疇會在我的 Sharepoint 和 OneDrive 中掃描和分類電子郵件、Pdf 等嗎？

內部部署 SharePoint 網站和程式庫的掃描是透過 Azure 資訊保護掃描器來提供。 掃描器可透過客戶的 Microsoft 365 訂用帳戶使用，且具有下列 Sku： AIP P1、EMS E3 和 M365 E3。 如果您有任何一個 Sku，您應該擁有正確的權利，才能開始使用 Azure 資訊保護掃描器。

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>用於掃描的計算是什麼？
有 Microsoft 管理的掃描基礎結構。 針對我們支援的大部分 Azure/AWS 資源，您不需要部署掃描基礎結構。

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>是否有方法可透過 Azure Resource Manager (ARM) 範本/CLI/PowerShell 來布建 Azure 範疇？

是，ARM 範本可供使用

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>我已經在使用塔，可以輕鬆地移至 Azure 範疇嗎？

Azure 範疇與塔 API 相容。 如果您是從塔遷移，建議您先使用 Azure 範疇來掃描您的資料來源。 當您的帳戶中有可用的資產之後，您就可以使用類似的塔 Api 來整合（例如更新資產或新增自訂歷程）。 Azure 範疇會修改搜尋 API 以使用 Azure 搜尋服務，因此您應該能夠使用「前進搜尋」。

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>我可以在我的租使用者中建立多個目錄嗎？

是，您可以為每個訂用帳戶和每個租使用者建立多個 Azure 範疇帳戶。 您可以使用 Azure 範疇來檢查限制頁面 [管理和增加資源配額](how-to-manage-quotas.md)。

我們的 [Azure 範疇部署最佳做法](deployment-best-practices.md)中記載了您應該或不應該有多個帳戶的其他建議。

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>是否可以在單一 Azure 範疇帳戶中註冊多個租使用者？

否，目前為了掃描另一個租使用者的資料來源，您必須在該租使用者中建立個別的 Azure 範疇帳戶。

### <a name="does-azure-purview-support-column-level-lineage"></a>Azure 範疇是否支援資料行層級歷程記錄？

是，Azure 範疇支援資料行層級歷程。