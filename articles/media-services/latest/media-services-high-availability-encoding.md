---
title: 媒體服務和隨選影片（VOD）的高可用性
description: 本文概述您可用來加速 VOD 應用程式高可用性的 Azure 服務。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/15/2020
ms.author: inhenkel
ms.openlocfilehash: 2dca351133394e27a1d795dc9f5f958f730bbf42
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092077"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>媒體服務和隨選影片（VOD）的高可用性

## <a name="high-availability-for-vod"></a>VOD 的高可用性

Azure 架構檔中有一個稱為[Geodes](/azure/architecture/patterns/geodes)的高可用性設計模式。 其中說明如何將重複的資源部署到不同的地理區域，以提供擴充性和復原能力。  您可以使用 Azure 服務來建立這類架構，以涵蓋許多高可用性設計考慮，例如，冗余、健康情況監控、負載平衡，以及資料備份和復原。  如下所述的其中一種架構，其中包含解決方案中所使用之每項服務的詳細資料，以及如何使用個別服務來建立 VOD 應用程式的高可用性架構。

### <a name="sample"></a>範例

有一個範例可供您使用，以透過媒體服務和隨選影片（VOD）熟悉高可用性。 此外，它也會詳細說明如何將服務用於 VOD 案例。  此範例不打算在生產環境中用於其目前的表單。  仔細回顧範例程式碼和讀我檔案，特別是[失敗模式](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)的一節，然後再將它整合到生產應用程式。  隨選影片（VOD）之高可用性的生產環境，也應該仔細檢查其內容傳遞網路（CDN）策略。  查看[GitHub 上](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)的程式碼。

## <a name="overview-of-services"></a>服務總覽

此範例架構中使用的服務包括：

| 圖示 | 名稱 | 說明 |
| :--: | ---- | ----------- |
|![image](media/media-services-high-availability-encoding/azure-media-services.svg)| 媒體服務帳戶 | **描述:**<br>媒體服務帳戶是在 Azure 中管理、加密、編碼、分析和串流處理媒體內容的起點。 它會與 Azure 儲存體帳戶資源相關聯。 帳戶和所有相關聯的存放裝置都必須位於相同的 Azure 訂用帳戶中。<br><br>**VOD 使用：**<br>這些是您用來編碼及提供影片和音訊資產的服務。  若要獲得高可用性，您至少要設定兩個媒體服務帳戶，分別在不同的區域中。 [深入瞭解 Azure 媒體服務](media-services-overview.md)。 |
|![image](media/media-services-high-availability-encoding/storage-account.svg)| 儲存體帳戶 | **描述:**<br>Azure 儲存體帳戶包含您的所有 Azure 儲存體資料物件： blob、檔案、佇列、資料表和磁片。 資料可透過 HTTP 或 HTTPS 從世界各地存取。<br><br>每個區域中的每個媒體服務帳戶都會有相同區域中的儲存體帳戶。<br><br>**VOD 使用：**<br>您可以儲存用於 VOD 處理和串流的輸入和輸出資料。 [深入瞭解 Azure 儲存體](../../storage/common/storage-introduction.md)。 |
|![image](media/media-services-high-availability-encoding/storage-account-queue.svg)| Azure 儲存體佇列 | **描述:**<br>Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。<br><br>**VOD 使用：**<br>佇列可以用來傳送和接收訊息，以協調不同模組之間的活動。 此範例會使用 Azure 儲存體的佇列，但 Azure 會提供其他類型的佇列，例如服務匯流排和 Service Fabric 可靠的佇列，這可能會更符合您的需求。 [深入瞭解 Azure 佇列](../../storage/queues/storage-queues-introduction.md)。 |
|![image](media/media-services-high-availability-encoding/azure-cosmos-db.svg)| Azure Cosmos DB  | **描述:**<br>Azure Cosmos DB 是 Microsoft 的全域散發多模型資料庫服務，可在全球任何數目的 Azure 區域之間獨立調整輸送量和儲存體。<br><br>**VOD 使用：**<br>資料表可以用來儲存作業輸出狀態記錄，並追蹤每個媒體服務實例的健全狀況狀態。 您也可以追蹤/記錄每次呼叫媒體服務 API 的狀態。 [深入瞭解 Azure Cosmos DB](../../cosmos-db/introduction.md)。  |
|![image](media/media-services-high-availability-encoding/managed-identity.svg)| 受控識別 | **描述:**<br>受控識別是 Azure AD 的一項功能，可在 Azure AD 中提供自動管理的身分識別。 它可用來向任何支援 Azure AD 驗證的服務進行驗證，包括 Key Vault，而不需要將認證儲存在程式碼中。<br><br>**VOD 使用：**<br>Azure Functions 可以使用受控識別來向媒體服務實例進行驗證，以連接到 Key Vault。 [深入瞭解受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。 |
|![image](media/media-services-high-availability-encoding/key-vault.svg)| Key Vault | **描述:**<br>Azure Key Vault 可用來安全地儲存權杖、密碼、憑證、API 金鑰和其他秘密，並嚴密控制其存取權。 它也可以用來作為金鑰管理解決方案。 Azure Key Vault 可讓您輕鬆地建立和控制用來加密資料的加密金鑰。 它可以輕鬆地布建、管理和部署公用和私人傳輸層安全性/安全通訊端層（TLS/SSL）憑證，以便與 Azure 和內部連線的資源搭配使用。 秘密和金鑰可以透過軟體或 FIPS 140-2 Level 2 驗證的 Hsm 來保護。<br><br>**VOD 使用：**<br>Key Vault 可用來為您的應用程式設定服務主體的存取原則。  它可以用來儲存儲存體帳戶的連接字串。 我們會使用 Key Vault 將連接字串儲存至儲存體帳戶和 cosmos db。 您也可以使用 Key Vault 來儲存整體叢集設定。 針對每個媒體服務實例，您可以儲存訂用帳戶識別碼、資源組名和帳戶名稱。 如需詳細資訊，請參閱範例中的使用方式。 [深入瞭解 Key Vault](../../key-vault/general/overview.md)。 |
|![image](media/media-services-high-availability-encoding/function-app.svg)| Azure Functions | **描述:**<br>執行少量的程式碼（稱為「函式」），而不需擔心具有 Azure Functions 的應用程式基礎結構。 [深入瞭解 Azure Functions](../../azure-functions/functions-overview.md)。<br><br>**VOD 使用：**<br>Azure Functions 可以用來儲存 VOD 應用程式的模組。  VOD 應用程式的模組可能包括：<br><br>**作業排程模組**<br>作業排程模組是用來將新作業提交至媒體服務叢集（不同區域中的兩個或多個實例）。 它會追蹤每個媒體服務實例的健全狀況狀態，並將新作業提交至下一個狀況良好的實例。<br><br>**作業狀態模組**<br>作業狀態模組會接聽來自 Azure 事件方格服務的作業輸出狀態事件。 它會將事件儲存到事件存放區，以將其餘模組的媒體服務 Api 呼叫次數降至最低。<br><br>**實例健全狀況模組**<br>此模組會追蹤已提交的作業，並判斷每個媒體服務實例的健全狀況狀態。 它會追蹤已完成的工作、失敗的工作和永遠不會完成的作業。<br><br>**布建模組**<br>此模組會布建已處理的資產。 它會將資產資料複製到所有媒體服務實例，並設定 Azure Front 開門服務，以確保即使某些媒體服務實例無法使用，也可以串流處理資產。 它也會設定串流定位器。<br><br>**作業驗證模組**<br>此模組會追蹤每個已提交的作業、重新提交失敗的作業，並在作業成功完成後，執行作業資料清除。  |
|![image](media/media-services-high-availability-encoding/application-service.svg)| App Service （和方案）  | **描述:**<br>Azure App Service 是 HTTP 型服務，用來裝載 Web 應用程式、REST API 和行動後端。 它支援 .NET、.NET Core、JAVA、Ruby、Node.js、PHP 或 Python。 應用程式會在 Windows 和 Linux 架構的環境中執行和調整。<br><br>**VOD 使用：**<br>每個模組都是由 App Service 所主控。 [深入瞭解 App Service](../../app-service/overview.md)。 |
|![image](media/media-services-high-availability-encoding/azure-front-door.svg)| Azure Front Door | **描述:**<br>Azure Front 門是用來定義、管理及監視網路流量的全域路由，方法是將最佳效能和快速全域容錯移轉優化以獲得高可用性。<br><br>**VOD 使用：**<br>Azure Front 可以用來將流量路由至串流端點。 [深入瞭解 Azure Front 大門](../../frontdoor/front-door-overview.md)。  |
|![image](media/media-services-high-availability-encoding/event-grid-subscription.svg)| Azure Event Grid | **描述:**<br>事件方格針對以事件為基礎的架構所建立的內建支援，提供來自 Azure 服務（例如儲存體 blob 和資源群組）的事件。 它也支援自訂主題事件。 篩選可用來將特定事件路由至不同的端點、多播至多個端點，以及確保事件確實傳遞。 它會在每個區域中的多個容錯網域之間，以及跨可用性區域，以將可用性最大化。<br><br>**VOD 使用：**<br>事件方格可用來追蹤所有應用程式事件，並儲存它們以保存作業狀態。 [深入瞭解 Azure 事件方格](../../event-grid/overview.md)。 |
|![image](media/media-services-high-availability-encoding/application-insights.svg)| Application Insights | **描述:** <br>Application Insights 是 Azure 監視器的一項功能，其為適用於開發人員和 DevOps 專業人員的可擴充應用程式效能管理 (APM) 服務。 它可用來監視即時應用程式。 它會偵測效能異常，並包含分析工具來診斷問題，並瞭解使用者如何使用應用程式。 它是設計來協助您持續改善效能和可用性。<br><br>**VOD 使用：**<br>所有記錄都可以傳送至 Application Insights。 藉由搜尋成功建立的作業訊息，可以查看哪些實例已處理每項作業。 它可能包含所有已提交的工作中繼資料，包括唯一識別碼和實例名稱資訊。 [深入瞭解 Application Insights](../../azure-monitor/app/app-insights-overview.md)。 |
## <a name="architecture"></a>架構

此高階圖表顯示提供給您開始使用高可用性和媒體服務的範例架構。

[![隨選影片（VOD）高層級架構圖表 ](media/media-services-high-availability-encoding/high-availability-architecture.svg)](media/media-services-high-availability-encoding/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>最佳作法

### <a name="regions"></a>區域

* [建立](https://review.docs.microsoft.com/azure/media-services/latest/create-account-cli-how-to)兩個（或更多） Azure 媒體服務帳戶。 這兩個帳戶必須位於不同的區域。 如需詳細資訊，請參閱[部署 Azure 媒體服務服務的區域](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。
* 將您的媒體上傳到您計畫提交作業所在的相同區域。 如需如何開始編碼的詳細資訊，請參閱[從 HTTPS URL 建立作業輸入](https://review.docs.microsoft.com/azure/media-services/latest/job-input-from-http-how-to)或[從本機檔案建立作業輸入](https://review.docs.microsoft.com/azure/media-services/latest/job-input-from-local-file-how-to)。
* 如果您接著需要將[作業](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)重新提交至另一個區域，您可以使用 `JobInputHttp` 或使用，將 `Copy-Blob` 資料從來源資產容器複製到替代區域中的資產容器。

### <a name="monitoring"></a>監視

* 透過 `JobStateChange` Azure 事件方格訂閱每個帳戶中的訊息。
    * 透過 Azure 入口網站或 CLI[註冊事件](https://review.docs.microsoft.com/azure/media-services/latest/reacting-to-media-services-events)（您也可以使用事件方格管理 SDK 來執行）
    * 使用[EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) （其以原生方式支援媒體服務事件）。
    * 您也可以透過 Azure Functions 使用事件方格事件。

    其他資訊：

    * 請參閱[音訊分析範例](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)，其中會示範如何使用 Azure 事件方格來監視作業，包括新增回復，以防 Azure 事件方格訊息因某些原因而延遲。
    * 請查看[適用于媒體服務事件的 Azure 事件方格架構](https://review.docs.microsoft.com/azure/media-services/latest/media-services-event-schemas)。

* 當您建立[作業](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)時：
    * 從目前使用的帳戶清單中隨機選取帳戶（此清單通常會包含這兩個帳戶，但如果偵測到問題，則可能只包含一個帳戶）。 如果清單是空的，請引發警示，讓操作員可以進行調查。
    * 建立記錄，以追蹤每個傳遞作業和所用的區域/帳戶。
* 當您的 `JobStateChange` 處理常式收到工作已達到排程狀態的通知時，請記錄它進入排程狀態的時間，以及所使用的區域/帳戶。
* 當您的 `JobStateChange` 處理常式收到工作已達到處理狀態的通知時，請將作業的記錄標記為「處理中」，並記錄它進入處理狀態的時間。
* 當您的 `JobStateChange` 處理常式收到工作已達到最終狀態（已完成/已錯誤/已取消）的通知時，請適當地標記作業的記錄。
* 有個別的程式，可定期查看作業的記錄
    * 如果您的工作處於 [已排程] 狀態，但在指定區域的合理時間內尚未前進到處理狀態，請從您目前使用的帳戶清單中移除該區域。 根據您的業務需求，您可以決定立即取消這些作業，並將它們重新提交至另一個區域。 或者，您可以提供更多時間來移至下一個狀態。
    * 如果已從帳戶清單中移除某個區域，請在將其新增回清單之前，監視它的復原。 區域健康情況可以透過區域中的現有作業（如果未取消並重新提交）來監視，方法是在一段時間後將帳戶新增回清單，並由操作員監視 Azure 通訊，以瞭解可能會影響 Azure 媒體服務的中斷。

## <a name="next-steps"></a>後續步驟

* 查看程式[代碼範例](/samples/browse/?products=azure-media-services)
