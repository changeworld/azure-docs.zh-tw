---
title: 設計您的 Azure 監視器記錄部署 |Microsoft Docs
description: 本文說明客戶準備在 Azure 監視器中部署工作區的考慮和建議。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: 939a2e67d6d2c215f7a575b6b9bd08660fc03b27
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87008194"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>設計 Azure 監視器記錄部署

Azure 監視器會將[記錄](data-platform-logs.md)資料儲存在 log Analytics 工作區中，這是一種 Azure 資源，也是資料收集、匯總並作為系統管理界限的容器。 雖然您可以在 Azure 訂用帳戶中部署一或多個工作區，但您應該瞭解幾個考慮，以確保您的初始部署遵循我們的指導方針，為您提供符合成本效益、可管理和可擴充的部署，符合您組織的需求。

工作區中的資料會組織成資料表，其中每一個都會儲存不同種類的資料，並且根據產生資料的資源，擁有自己的唯一屬性集。 大部分的資料來源都會寫入其在 Log Analytics 工作區中的資料表。

![範例工作區資料模型](./media/design-logs-deployment/logs-data-model-01.png)

Log Analytics 工作區提供：

* 資料儲存體的地理位置。
* 藉由授與不同的使用者存取權限，遵循其中一個建議的設計策略來進行資料隔離。
* 設定的範圍，例如[定價層](./manage-cost-storage.md#changing-pricing-tier)、[保留期](./manage-cost-storage.md#change-the-data-retention-period)和[資料上限](./manage-cost-storage.md#manage-your-maximum-daily-data-volume)。

這篇文章提供設計和遷移考慮、存取控制總覽，以及瞭解我們建議您的 IT 組織所採用的設計整合的詳細總覽。



## <a name="important-considerations-for-an-access-control-strategy"></a>存取控制策略的重要考慮

識別您需要的工作區數目會受到下列一或多項需求的影響：

* 您是一家全球性公司，基於資料主權或合規性理由，您需要將記錄資料儲存在特定區域。
* 您使用 Azure，想要將工作區和它所管理的 Azure 資源放在相同區域中，以避免輸出資料傳輸費用。
* 您可以管理多個部門或商務群組，而且您想要每個人都能查看自己的資料，但不會看到其他人的資料。 此外，「合併跨部門」或「商務群組」視圖也不需要商務需求。

現今的 IT 組織會以集中式、分散式或兩種結構混合在一起的模式來進行模型化。 因此，下列工作區部署模型通常用來對應至下列其中一個組織結構：

* **集中式**：所有記錄都會儲存在中央工作區中，並由單一小組管理，Azure 監視器提供每個小組的區分存取權。 在此案例中，您可以輕鬆地管理、跨資源搜尋，以及交叉相互關聯記錄。 視您訂用帳戶中的多個資源所收集的資料量而定，工作區可能會大幅成長，並具有額外的系統管理負擔，以維護對不同使用者的存取控制。 此模型稱為「中樞和輪輻」。
* 非集中**式：每**個小組都有自己的工作區在自己的資源群組中建立並管理，而且每個資源會隔離記錄資料。 在此案例中，工作區可以保持安全，而且存取控制與資源存取一致，但很難以跨記錄相互關聯。 需要廣泛查看許多資源的使用者無法以有意義的方式來分析資料。
* **混合**式：安全性審查合規性需求會進一步使這種情況變得複雜，因為許多組織會平行執行兩種部署模型。 這通常會導致複雜、昂貴且難以維護的設定，以及記錄涵蓋範圍中的間距。

使用 Log Analytics 代理程式來收集資料時，您必須了解下列事項才能規劃代理程式的部署：

* 若要從 Windows 代理程式收集資料，您可以[將每個代理程式設定為向一或多個工作區報告](../../azure-monitor/platform/agent-windows.md)，即使其同時會向 System Center Operations Manager 管理群組報告也沒關係。 Windows 代理程式最多可以向四個工作區報告。
* Linux 代理程式不支援多重主目錄，只能向單一工作區報告。

如果您使用 System Center Operations Manager 2012 R2 或更新版本：

* 每個 Operations Manager 管理群組[只能連線到一個工作區](../platform/om-agents.md)。 
* 向管理群組報告的 Linux 電腦必須設定為直接向 Log Analytics 工作區報告。 如果您的 Linux 電腦已直接向工作區報告，而您想要使用 Operations Manager 來監視這些電腦，請遵循這些步驟來[向 Operations Manager 管理群組報告](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)。 
* 您可以在 Windows 電腦上安裝 Log Analytics Windows 代理程式，並讓其同時向已與工作區整合的 Operations Manager 以及不同工作區報告。

## <a name="access-control-overview"></a>存取控制概觀

使用角色型存取控制（RBAC），您可以只授與使用者和群組使用工作區中的監視資料所需的存取權數量。 這可讓您使用單一工作區，將收集到的資料儲存在所有資源上，以配合您的 IT 組織作業模型。 例如，您將存取權授與您負責裝載于 Azure 虛擬機器（Vm）上之基礎結構服務的小組，因此他們只能存取 Vm 所產生的記錄。 這會遵循我們的新資源內容記錄模型。 此模型的基礎是針對 Azure 資源所發出的每個記錄檔記錄，它會自動與此資源相關聯。 記錄會轉送到中央工作區，以根據資源來遵循範圍和 RBAC。

使用者可以存取的資料是由下表所列的因素組合所決定。 以下各節將說明每個步驟。

| 因數 | 描述 |
|:---|:---|
| [存取模式](#access-mode) | 使用者用來存取工作區的方法。  定義可用資料的範圍，以及套用的存取控制模式。 |
| [存取控制模式](#access-control-mode) | 在工作區上設定，以定義是否要在工作區或資源層級套用許可權。 |
| [權限](manage-access.md) | 適用于工作區或資源的個人或使用者群組的許可權。 定義使用者可存取的資料。 |
| [資料表層級 RBAC](manage-access.md#table-level-rbac) | 選擇性的細微許可權，適用于所有使用者，不論其存取模式或存取控制模式為何。 定義使用者可以存取的資料類型。 |

## <a name="access-mode"></a>存取模式

*存取模式*指的是使用者存取 Log Analytics 工作區的方式，並定義他們可以存取的資料範圍。 

使用者有兩個選項可存取資料：

* **工作區**內容：您可以在具有許可權的工作區中，查看所有記錄。 在此模式中的查詢範圍為工作區中所有資料表的所有資料。 當您以工作區作為範圍來存取記錄時，例如當您從 Azure 入口網站的 [ **Azure 監視器**] 功能表中選取 [**記錄**] 時，這就是使用的存取模式。

    ![工作區中的 Log Analytics 內容](./media/design-logs-deployment/query-from-workspace.png)

* **資源**內容：當您存取特定資源、資源群組或訂用帳戶的工作區時（例如當您從 Azure 入口網站中的 [資源] 功能表選取 [**記錄**] 時），只可以針對您有權存取的所有資料表中的資源來查看記錄。 此模式中的查詢範圍僅限於與該資源相關聯的資料。 此模式也會啟用細微的 RBAC。

    ![來自資源的 Log Analytics 內容](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > 記錄僅適用于資源內容查詢（如果它們已正確關聯至相關資源）。 目前，下列資源有一些限制：
    > - Azure 外部的電腦
    > - Service Fabric
    > - Application Insights
    >
    > 您可以藉由執行查詢並檢查您感興趣的記錄，來測試記錄是否已正確地與資源相關聯。 如果[_ResourceId](log-standard-properties.md#_resourceid)屬性中有正確的資源識別碼，則資料可供以資源為中心的查詢使用。

Azure 監視器會根據您執行記錄搜尋的內容，自動決定正確的模式。 範圍一律會顯示在 Log Analytics 的左上角區段中。

### <a name="comparing-access-modes"></a>比較存取模式

下表摘要說明存取模式：

| 問題 | 工作區-內容 | 資源內容 |
|:---|:---|:---|
| 每個模型的用途為何？ | 管理中心。 需要設定資料收集的系統管理員，以及需要存取各種資源的使用者。 目前也需要存取 Azure 外部資源的記錄。 | 應用程式小組。 受監視的 Azure 資源系統管理員。 |
| 使用者需要什麼才能查看記錄？ | 工作區的許可權。 請參閱[使用工作區許可權來管理存取權](manage-access.md#manage-access-using-workspace-permissions)中的**工作區許可權**。 | 資源的讀取權限。 請參閱[使用 Azure 許可權來管理存取權](manage-access.md#manage-access-using-azure-permissions)中的**資源許可權**。 許可權可以是繼承的（例如，從包含的資源群組），或直接指派給資源。 系統將會自動指派資源的記錄許可權。 |
| 許可權的範圍為何？ | 區域. 具有工作區存取權的使用者可以從他們有許可權的資料表查詢工作區中的所有記錄。 請參閱[資料表存取控制](manage-access.md#table-level-rbac) | Azure 資源。 使用者可以從任何工作區查詢可存取的特定資源、資源群組或訂用帳戶的記錄，但無法查詢其他資源的記錄。 |
| 使用者存取記錄的方式為何？ | <ul><li>從**Azure 監視器**] 功能表啟動**記錄**。</li></ul> <ul><li>從**Log Analytics 工作區**啟動**記錄**。</li></ul> <ul><li>從 Azure 監視器活頁[簿](../visualizations.md#workbooks)。</li></ul> | <ul><li>從 Azure 資源的功能表啟動**記錄**</li></ul> <ul><li>從**Azure 監視器**] 功能表啟動**記錄**。</li></ul> <ul><li>從**Log Analytics 工作區**啟動**記錄**。</li></ul> <ul><li>從 Azure 監視器活頁[簿](../visualizations.md#workbooks)。</li></ul> |

## <a name="access-control-mode"></a>存取控制模式

*存取控制模式*是每個工作區上的一項設定，可定義如何決定工作區的許可權。

* **需要工作區許可權**：此控制模式不允許細微的 RBAC。 若要讓使用者存取工作區，他們必須被授與工作區或特定資料表的許可權。

    如果使用者在工作區內容模式之後存取工作區，他們就可以存取已授與存取權之任何資料表中的所有資料。 如果使用者在資源內容模式之後存取工作區，他們就只能存取已授與存取權的任何資料表中該資源的資料。

    這是在2019年3月之前建立之所有工作區的預設設定。

* **使用資源或工作區許可權**：此控制模式允許細微的 RBAC。 使用者可以藉由指派 Azure 許可權，將存取權授與他們可以查看的資源相關聯的資料 `read` 。 

    當使用者存取工作區內容模式中的工作區時，會套用工作區許可權。 當使用者存取資源內容模式中的工作區時，只會驗證資源許可權，而且會忽略工作區許可權。 將使用者從工作區許可權中移除，並允許辨識其資源許可權，以啟用 RBAC。

    這是2019年3月之後所建立之所有工作區的預設設定。

    > [!NOTE]
    > 如果使用者只有工作區的資源許可權，則只有在工作區存取模式設定為 [**使用資源] 或 [工作區] 許可權**時，才能夠使用資源內容模式來存取工作區。

若要瞭解如何使用 PowerShell 變更入口網站中的存取控制模式，或使用 Resource Manager 範本，請參閱[設定存取控制模式](manage-access.md#configure-access-control-mode)。

## <a name="ingestion-volume-rate-limit"></a>內嵌磁片區速率限制

Azure 監視器是一種大規模的資料服務，服務對象為每月需傳送數 TB 資料 (且不斷成長) 的上千名客戶。 預設的 [攝取速率] 閾值會設定為每個工作區**6 GB/分鐘**。 這是估計值，因為根據記錄長度和其壓縮比率，實際大小可能會在資料類型之間有所不同。 此限制不適用於從代理程式或[資料收集器 API](data-collector-api.md)傳送的資料。

如果您以較高的速率將資料傳送至單一工作區，則會卸載某些資料，並每隔6小時將事件傳送至工作區中的*作業資料表，* 而臨界值會繼續超過閾值。 如果您的內嵌磁片區持續超過速率限制，或您希望很快就會到達，您可以傳送電子郵件至 LAIngestionRate@microsoft.com 或開啟支援要求，以要求增加您的工作區。
 
若要在您的工作區中收到這類事件的通知，請使用下列查詢建立[記錄警示規則](alerts-log.md)，並以大於的結果數目為零的警示邏輯基底。

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


## <a name="recommendations"></a>建議

![資源內容設計範例](./media/design-logs-deployment/workspace-design-resource-context-01.png)

此案例涵蓋您的 IT 組織訂用帳戶中的單一工作區設計，其不受資料主權或法規合規性的限制，或需要對應至您的資源部署所在的區域。 它可讓您的組織安全性和 IT 系統管理員小組能夠利用改良的 Azure 存取管理整合，以及更安全的存取控制。

所有資源、監視解決方案和深入解析（例如 Application Insights 和適用於 VM 的 Azure 監視器、支援由不同小組維護的基礎結構和應用程式）都設定為將其收集的記錄資料轉送至 IT 組織集中式共用工作區。 每個小組的使用者會被授與存取權給他們存取的資源記錄。

部署工作區架構之後，您可以使用[Azure 原則](../../governance/policy/overview.md)在 Azure 資源上強制執行此操作。 它提供一種方式來定義原則，並確保符合您的 Azure 資源，以便將其所有資源記錄傳送至特定工作區。 例如，使用 Azure 虛擬機器或虛擬機器擴展集時，您可以使用評估工作區合規性和報告結果的現有原則，或自訂以補救不符合規範的情況。  

## <a name="workspace-consolidation-migration-strategy"></a>工作區合併遷移策略

若客戶已部署多個工作區，且想要合併至資源內容存取模型，建議您採用累加方法來遷移至建議的存取模型，而且您不會嘗試快速或積極地達到此目的。 遵循遵循合理的時間軸來規劃、遷移、驗證和淘汰的逐步執行方法，可協助避免任何未規劃的事件或對您的雲端作業造成非預期的影響。 如果您沒有適用于合規性或商業理由的資料保留原則，您需要評估適當的時間長度，以便在程式期間將資料保留在您要遷移的工作區中。 當您重新設定資源以向共用工作區報告時，您仍然可以視需要分析原始工作區中的資料。 完成遷移之後，如果您在保留期間結束之前，將資料保留在原始工作區中，請勿將它刪除。

在規劃遷移到此模型時，請考慮下列事項：

* 瞭解哪些產業法規和內部原則必須符合您的資料保留期。
* 請確定您的應用程式小組可以在現有的資源內容功能中工作。
* 識別授與應用程式小組資源的存取權，並在開發環境中進行測試，然後才在生產環境中執行。
* 設定工作區以啟用 [**使用資源] 或 [工作區] 許可權**。
* 移除應用程式小組讀取和查詢工作區的許可權。
* 啟用和設定任何監視解決方案，例如容器和/或適用於 VM 的 Azure 監視器的 Azure 監視器、您的自動化帳戶，以及部署在原始工作區中的管理解決方案（例如更新管理、啟動/停止 Vm 等）。

## <a name="next-steps"></a>接下來的步驟

若要執行本指南中建議的安全性許可權和控制項，請參閱[管理對記錄的存取權](manage-access.md)。
