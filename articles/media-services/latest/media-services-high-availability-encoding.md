---
title: 隨選媒體服務和影片隨選 (VOD) 的高可用性
description: 本文概述您可用來加速 VOD 應用程式高可用性的 Azure 服務。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: conceptual
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0b6233552501fbe1578f3abe4e203d725ecddb4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707790"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>隨選媒體服務和影片隨選 (VOD) 的高可用性

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="high-availability-for-vod"></a>VOD 的高可用性

Azure 架構檔中有一個稱為 [Geodes](/azure/architecture/patterns/geodes) 的高可用性設計模式。 它說明如何將重複的資源部署到不同的地理區域，以提供擴充性和復原能力。  您可以使用 Azure 服務來建立這類架構，以涵蓋許多高可用性設計考慮，例如冗余、健康情況監視、負載平衡和資料備份和復原。  下列其中一種架構如下所述，其中包含解決方案中所使用之每個服務的詳細資料，以及如何使用個別服務來建立 VOD 應用程式的高可用性架構。

### <a name="sample"></a>範例

您可以使用範例來熟悉媒體服務的高可用性，以及隨選 (VOD) 的隨選影片。 此外，也會詳細說明如何將服務用於 VOD 案例。  此範例不適合在生產環境中使用其目前的表單。  仔細檢查範例程式碼和讀我檔案，特別是 [失敗模式](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming) 的章節，然後再將其整合到實際執行應用程式。  針對隨選影片 (VOD) 的高可用性生產環境，也應仔細檢查其內容傳遞網路 (CDN) 策略。  查看 [GitHub 上](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)的程式碼。

## <a name="overview-of-services"></a>服務總覽

此範例架構中使用的服務包括：

| 圖示 | 名稱 | 描述 |
| :--: | ---- | ----------- |
|![這是媒體服務帳戶圖示。](media/media-services-high-availability-encoding/azure-media-services.svg)| 媒體服務帳戶 | **描述：**<br>媒體服務帳戶是在 Azure 中管理、加密、編碼、分析和串流處理媒體內容的起點。 它會與 Azure 儲存體帳戶資源相關聯。 帳戶和所有相關聯的儲存體都必須位於相同的 Azure 訂用帳戶中。<br><br>**VOD 用途：**<br>這些是您用來編碼和傳遞影片和音訊資產的服務。  為了達到高可用性，您至少要設定兩個媒體服務帳戶，每個帳戶都在不同的區域中。 [深入瞭解 Azure 媒體服務](media-services-overview.md)。 |
|![這是儲存體帳戶圖示。](media/media-services-high-availability-encoding/storage-account.svg)| 儲存體帳戶 | **描述：**<br>Azure 儲存體帳戶包含您所有的 Azure 儲存體資料物件： blob、檔案、佇列、資料表和磁片。 資料可透過 HTTP 或 HTTPS 從世界各地存取。<br><br>每個區域中的每個媒體服務帳戶都會有相同區域中的儲存體帳戶。<br><br>**VOD 用途：**<br>您可以儲存用於 VOD 處理和串流處理的輸入和輸出資料。 [深入瞭解 Azure 儲存體](../../storage/common/storage-introduction.md)。 |
|![這是 Azure 儲存體的佇列圖示。](media/media-services-high-availability-encoding/storage-account-queue.svg)| Azure 儲存體佇列 | **描述：**<br>Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。<br><br>**VOD 用途：**<br>您可以使用佇列來傳送和接收訊息，以協調不同模組之間的活動。 此範例會使用 Azure 儲存體佇列，但 Azure 會提供其他類型的佇列，例如服務匯流排和 Service Fabric 可靠的佇列，這可能更符合您的需求。 [深入瞭解 Azure 佇列](../../storage/queues/storage-queues-introduction.md)。 |
|![這是 Azure Cosmos DB 圖示。](media/media-services-high-availability-encoding/azure-cosmos-db.svg)| Azure Cosmos DB  | **描述：**<br>Azure Cosmos DB 是 Microsoft 的全域散發多模型資料庫服務，可獨立調整全球任意數量的 Azure 區域之間的輸送量和儲存體。<br><br>**VOD 用途：**<br>您可以使用資料表來儲存作業輸出狀態記錄，以及追蹤每個媒體服務實例的健全狀況狀態。 您也可以追蹤/記錄每個媒體服務 API 呼叫的狀態。 [深入瞭解 Azure Cosmos DB](../../cosmos-db/introduction.md)。  |
|![這是受控識別圖示。](media/media-services-high-availability-encoding/managed-identity.svg)| 受控識別 | **描述：**<br>受控識別是 Azure AD 的功能，可在 Azure AD 中提供自動管理的身分識別。 它可用來向任何支援 Azure AD authentication 的服務進行驗證，包括 Key Vault，而不需要將認證儲存在程式碼中。<br><br>**VOD 用途：**<br>Azure Functions 可以使用受控識別來向媒體服務實例進行驗證，以連接到 Key Vault。 [深入瞭解受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。 |
|![這是 Key Vault 圖示。](media/media-services-high-availability-encoding/key-vault.svg)| Key Vault | **描述：**<br>Azure Key Vault 可以用來安全地儲存權杖、密碼、憑證、API 金鑰和其他秘密，並嚴密控制其存取權。 它也可用來做為金鑰管理解決方案。 Azure Key Vault 可讓您輕鬆地建立和控制用來加密資料的加密金鑰。 它可以輕鬆地布建、管理及部署公用和私人傳輸層安全性/安全通訊端層 (TLS/SSL) 憑證，以便與 Azure 和內部連線的資源搭配使用。 秘密和金鑰可受到軟體或 FIPS 140-2 Level 2 驗證的 Hsm 保護。<br><br>**VOD 用途：**<br>Key Vault 可以用來為您的應用程式設定服務主體的存取原則。  可以用來將連接字串儲存到儲存體帳戶。 我們使用 Key Vault 將連接字串儲存至儲存體帳戶和 cosmos db。 您也可以使用 Key Vault 來儲存整體的叢集設定。 您可以針對每個媒體服務實例儲存訂用帳戶識別碼、資源組名和帳戶名稱。 如需詳細資訊，請參閱範例中的使用方式。 [深入瞭解 Key Vault](../../key-vault/general/overview.md)。 |
|![這是 Azure Functions 圖示。](media/media-services-high-availability-encoding/function-app.svg)| Azure Functions | **描述：**<br>執行一小段程式碼 (稱為「函式」 ) ，而不需要擔心應用程式基礎結構的 Azure Functions。 [深入瞭解 Azure Functions](../../azure-functions/functions-overview.md)。<br><br>**VOD 用途：**<br>Azure Functions 可以用來儲存 VOD 應用程式的模組。  VOD 應用程式的模組可能包括：<br><br>**作業排程模組**<br>作業排程模組可將新作業提交至媒體服務叢集， (不同區域中的兩個或多個實例) 。 它會追蹤每個媒體服務實例的健全狀況狀態，並將新作業提交至下一個狀況良好的實例。<br><br>**作業狀態模組**<br>作業狀態模組會接聽來自 Azure 事件方格服務的作業輸出狀態事件。 它會將事件儲存至事件存放區，以將 rest 模組的媒體服務 Api 呼叫次數降至最低。<br><br>**實例健全狀況模組**<br>此課程模組會追蹤已提交的作業，並判斷每個媒體服務實例的健全狀況狀態。 它會追蹤已完成的工作、失敗的工作和從未完成的作業。<br><br>**布建模組**<br>此課程模組會布建已處理的資產。 它會將資產資料複製到所有媒體服務實例，並設定 Azure Front Door 服務，以確保即使某些媒體服務實例無法使用，也可以串流資產。 它也會設定串流定位器。<br><br>**作業驗證模組**<br>此課程模組會追蹤每個提交的工作、重新提交失敗的作業，並在作業成功完成後執行作業資料清除。  |
|![這是 App Service 圖示。](media/media-services-high-availability-encoding/application-service.svg)| App Service (和規劃)   | **描述：**<br>Azure App Service 是 HTTP 型服務，用來裝載 Web 應用程式、REST API 和行動後端。 它支援 .NET、.NET Core、JAVA、Ruby、Node.js、PHP 或 Python。 應用程式會在 Windows 與 Linux 架構的環境中執行及調整。<br><br>**VOD 用途：**<br>每個模組都是由 App Service 所主控。 [深入瞭解 App Service](../../app-service/overview.md)。 |
|![這是 Azure Front Door 圖示。](media/media-services-high-availability-encoding/azure-front-door.svg)| Azure Front Door | **描述：**<br>Azure Front Door 用來定義、管理及監視網路流量的全域路由，方法是優化以獲得最佳效能，並針對高可用性進行快速全域容錯移轉。<br><br>**VOD 用途：**<br>Azure Front Door 可以用來將流量路由傳送至串流端點。 [深入瞭解 Azure Front Door](../../frontdoor/front-door-overview.md)。  |
|![這是 Azure 事件方格圖示。](media/media-services-high-availability-encoding/event-grid-subscription.svg)| Azure Event Grid | **描述：**<br>事件方格是針對以事件為基礎的架構所建立的內建支援，適用于來自 Azure 服務的事件，例如儲存體 blob 和資源群組。 它也支援自訂主題事件。 篩選準則可用來將特定事件路由至不同的端點、多播至多個端點，以及確定事件會可靠地傳遞。 它會以原生方式分散到每個區域中的多個容錯網域，以及跨可用性區域，以最大化可用性。<br><br>**VOD 用途：**<br>事件方格可用來追蹤所有應用程式事件，並加以儲存以保存作業狀態。 [深入瞭解 Azure 事件方格](../../event-grid/overview.md)。 |
|![這是 Application Insights 圖示。](media/media-services-high-availability-encoding/application-insights.svg)| Application Insights | **描述：** <br>Application Insights 是 Azure 監視器的一項功能，其為適用於開發人員和 DevOps 專業人員的可擴充應用程式效能管理 (APM) 服務。 它是用來監視即時應用程式。 它會偵測效能異常，並包含分析工具來診斷問題，並瞭解使用者如何運用應用程式。 它是設計來協助您持續改善效能和可用性。<br><br>**VOD 用途：**<br>所有記錄都可以傳送至 Application Insights。 您可以藉由搜尋成功建立的工作訊息來查看每個工作的實例處理方式。 它可能包含所有提交的工作中繼資料，包括唯一識別碼和實例名稱資訊。 [深入瞭解 Application Insights](../../azure-monitor/app/app-insights-overview.md)。 |
## <a name="architecture"></a>架構

此高階圖表顯示提供的範例架構，可讓您開始使用高可用性和媒體服務。

[![隨選影片 (VOD) 高層級架構圖表 ](media/media-services-high-availability-encoding/high-availability-architecture.svg)](media/media-services-high-availability-encoding/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>最佳作法

### <a name="regions"></a>區域

* [建立](https://review.docs.microsoft.com/azure/media-services/latest/create-account-cli-how-to) 兩個 (或多個) Azure 媒體服務帳戶。 這兩個帳戶必須位於不同的區域。 如需詳細資訊，請參閱 [部署 Azure 媒體服務服務的區域](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。
* 將您的媒體上傳到您打算提交作業的相同區域。 如需如何開始編碼的詳細資訊，請參閱 [從 HTTPS URL 建立工作輸入](https://review.docs.microsoft.com/azure/media-services/latest/job-input-from-http-how-to) 或 [從本機檔案建立工作輸入](https://review.docs.microsoft.com/azure/media-services/latest/job-input-from-local-file-how-to)。
* 如果您接著需要將 [作業](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept) 重新提交到另一個區域，您可以使用 `JobInputHttp` 或使用 `Copy-Blob` 將資料從來源資產容器複製到替代區域中的資產容器。

### <a name="monitoring"></a>監視

* 透過 `JobStateChange` Azure 事件方格訂閱每個帳戶中的訊息。
    * 透過 Azure 入口網站或 CLI[註冊事件](https://review.docs.microsoft.com/azure/media-services/latest/reacting-to-media-services-events) (您也可以使用事件方格管理 SDK 來進行事件) 
    * 使用支援媒體服務事件的 [EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (原生) 。
    * 您也可以透過 Azure Functions 使用事件方格事件。

    其他資訊：

    * 請參閱 [音訊分析範例](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept) ，其中會示範如何使用 Azure 事件方格來監視作業，包括新增回復，以防 Azure 事件方格訊息因某些原因而延遲。
    * 請參閱 [媒體服務事件的 Azure 事件方格架構](https://review.docs.microsoft.com/azure/media-services/latest/media-services-event-schemas)。

* 當您建立 [作業](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)時：
    * 從目前使用的帳戶清單中隨機選取帳戶 (這份清單通常會包含這兩個帳戶，但是如果偵測到問題，可能只會包含一個帳戶) 。 如果清單是空的，請引發警示，讓操作員可以調查。
    * 建立記錄以追蹤每個傳遞作業和使用的區域/帳戶。
* 當您的 `JobStateChange` 處理常式取得作業已達到排程狀態的通知時，請記錄其進入排程狀態的時間，以及所使用的區域/帳戶。
* 當您的 `JobStateChange` 處理常式取得作業已達到處理狀態的通知時，請將作業的記錄標示為處理，並記錄其進入處理狀態的時間。
* 當您的 `JobStateChange` 處理常式收到工作已達到最後狀態 (完成/錯誤/取消) 的通知時，請正確地標示作業的記錄。
* 有個別的進程，可定期查看您的作業記錄
    * 如果您的工作處於已排程狀態，但在指定區域的合理時間內尚未前進到處理狀態，請從目前使用的帳戶清單中移除該區域。 根據您的業務需求，您可以決定立即取消這些作業，並將其重新提交到另一個區域。 或者，您可以讓他們有更多時間移到下一個狀態。
    * 如果區域已從帳戶清單中移除，請監視該區域以進行復原，然後再將其新增回清單。 區域健康情況可以透過區域中現有的工作進行監視 (如果未取消和重新提交) ，請在一段時間後將帳戶新增回清單，以及藉由操作員監視可能會影響 Azure 媒體服務的中斷情況。

## <a name="next-steps"></a>後續步驟

* 查看程式 [代碼範例](/samples/browse/?products=azure-media-services)
