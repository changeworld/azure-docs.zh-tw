---
title: Azure 媒體服務 v2 到 v3 之間的術語和實體變更
description: 本文說明 Azure 媒體服務 v2 到 v3 之間的術語差異。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: b53fbcb62004a8af9b2470c76f64f1ace845c1a8
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898387"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>媒體服務 V2 和 V3 之間的術語和實體變更

![遷移指南標誌](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![遷移步驟2](./media/migration-guide/steps-2.svg)

本文說明 Azure 媒體服務 v2 到 v3 之間的術語差異。

## <a name="naming-conventions"></a>命名規範

檢查適用于媒體服務 V3 資源的命名慣例。 另請參閱 [命名 blob](assets-concept.md#naming)

## <a name="terminology-changes"></a>術語變更

- *定位器* 現在稱為 *串流定位器*。
- *通道* 現在稱為「*實況活動*」。
- *程式* 現在稱為「*即時輸出*」。
- 工作 *現在* 稱為「 *JobOutput*」，這是作業的一部分。

## <a name="entity-changes"></a>實體變更
| **V2 實體**<!-- row --> | **V3 實體** | **指引** | **V3 可以存取** | **由 V3 更新** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  此實體 `AccessPolicies` 不存在於 V3 中。 | 否 | 否 |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | 是 | 是 |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | 是 | 否 |
| `AssetFile`<!-- row --> | <!-- empty --> |此實體 `AssetFiles` 不存在於 V3 中。 雖然您上傳的 (儲存體 blob) 檔案仍被視為檔案。<br/><br/> 請改用 Azure 儲存體 Api 來列舉容器中的 blob。 有兩種方式可以將轉換套用至具有工作的檔案：<br/><br/>檔案已上傳至儲存體： URI 會包含要在儲存體帳戶內的資產上進行之作業的資產識別碼。<br/><br/>在轉換和作業程式期間要上傳的檔案：在儲存體中建立資產、傳回 SAS URL、將檔案上傳至儲存體，然後將轉換套用至檔案。 | 否 | 否 |
| `Channel`<!-- row --> | `LiveEvent` | 實況活動會取代 v2 API 中的通道。 它們會執行大部分的功能，並提供更多的新功能，例如即時轉譯、待命模式，以及 RTMPS 內嵌的支援。 <br/><br/>查看 [以案例為基礎的即時串流中的實況活動](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | 否 | 否 |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` 不再是實體，它現在是串流定位器的屬性。<br/><br/>  在 v3 中，內容金鑰資料會與 `StreamingLocator` 輸出加密) 的 (相關聯，或用於用戶端儲存體加密) 的資產本身 (。 | 是 | 否 |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | 是 | 否 |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` 包含在中 `ContentKeyPolicy` 。 | 是 | 否 |
| `IngestManifest`<!-- row --> | <!-- empty --> | 此實體 `IngestManifests` 不存在於 V3 中。 在 V3 中上傳檔案牽涉到 Azure 儲存體 API。 系統會先建立資產，然後將檔案上傳至相關聯的儲存體容器。 有許多方式可以將資料放入可改用的 Azure 儲存體容器中。 `JobInputHttp` 也提供一種方法，可讓您視需要從指定的 url 下載工作輸入。 | 否 | 否 |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | 有許多方式可以將資料放入可改用的 Azure 儲存體容器中。 `JobInputHttp` 也提供一種方法，可讓您視需要從指定的 url 下載工作輸入。 | 否 | 否 |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | 有許多方式可以將資料放入可改用的 Azure 儲存體容器中。 `JobInputHttp` 也提供一種方法，可讓您視需要從指定的 url 下載工作輸入。 | 否 | 否 |
| `Job`<!-- row --> | `Job` | 建立之前，請 `Transform` 先建立 `Job` 。 | 否 | 否 |
| `JobTemplate`<!-- row --> | `Transform` | 請改用 `Transform` 。 轉換是作業中的個別實體，而且是 reuseable 的。 | 否 | 否 |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | 是 | 否 |
| `MediaProcessor`<!-- row --> | <!-- empty --> | `MediaProcessor`在定義轉換時，請使用所需的預設值，而不是查閱要依名稱使用的。 使用的預設值將會決定作業系統所使用的媒體處理器。 請參閱以 [案例為基礎的編碼](migrate-v-2-v-3-migration-scenario-based-encoding.md)主題編碼。 <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | 否 | NA (V2 中的 readonly)  |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | V3 中的通知是透過 Azure 事件方格來處理。 `NotificationEndpoint`是由事件方格訂用帳戶註冊所取代，此註冊也會封裝要 (接收之通知類型的設定，此設定會由工作的處理常式、工作的工作 `JobNotificationSubscription` `TaskNotificationSubscription` 和遙測) 來處理 `ComponentMonitoringSetting` 。 V2 遙測是在 Azure 事件方格和 Azure 監視器之間分割，以納入較大的 Azure 生態系統的增強功能。 | 否 | 否 |
| `Program`<!-- row --> | `LiveOutput` | 即時輸出現在會取代 v3 API 中的程式。  | 否 | 否 |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | 串流端點的主要維持相同。 它們可用來動態封裝、加密及傳遞 HLS 和虛線內容，以供即時和隨選串流直接從來源或透過 CDN 傳遞。 新功能包括支援更好的 Azure 監視器整合和製作圖表。 |  是 | 是 |
| `Task`<!-- row --> | `JobOutput` | 由 `JobOutput` (取代為 API) 中不再是個別的實體。  請參閱以 [案例為基礎的編碼](migrate-v-2-v-3-migration-scenario-based-encoding.md)主題編碼。 | 否 | 否 |
| `TaskTemplate`<!-- row --> | `TransformOutput` | 由 `TransformOutput` (取代為 API) 中不再是個別的實體。 請參閱以 [案例為基礎的編碼](migrate-v-2-v-3-migration-scenario-based-encoding.md)主題編碼。 | 否 | 否 |
| `Inputs`<!-- row --> | `Inputs` | 輸入和輸出現在位於作業層級。 請參閱以[案例為基礎的編碼](migrate-v-2-v-3-migration-scenario-based-encoding.md)主題編碼主題 | 否 | 否 |
| `Outputs`<!-- row --> | `Outputs` | 輸入和輸出現在位於作業層級。  在 V3 中，元資料格式從 XML 變更為 JSON。  「實況輸出」會在建立時開始，並在刪除時結束。 請參閱以[案例為基礎的編碼](migrate-v-2-v-3-migration-scenario-based-encoding.md)主題編碼主題 | 否 | 否 |


| **其他變更** | **2**  | **V3** |
|---|---|---|
| **儲存體** <!--new row --> |||
| 存放裝置 <!--new row --> | | V3 Sdk 現已與儲存體 SDK 分離，可讓您更充分掌控您想要使用的儲存體 SDK 版本，並避免版本控制問題。                      |
| **編碼方式** <!--new row --> |||
| 編碼位元速率 <!--new row --> | 以 kbps 測量的位元速率，例如： 128 (kbps) | 每秒位數，例如： 128000 (位/秒) |
| 編碼 DRM FairPlay <!--new row --> | 在媒體服務 V2 中，可以指定初始化向量 (IV) 。 | 在媒體服務 V3 中，無法指定 FairPlay IV。|
| Premium 編碼器 <!--new row --> | Premium 編碼器和舊版索引子| [Premium 編碼器](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset)和舊版[媒體分析處理器](https://docs.microsoft.com/azure/media-services/previous/legacy-components) (Azure 媒體服務索引子2預覽、臉部 Redactor 等 ) 無法透過 V3 存取。 我們已將音訊通道對應的支援新增至標準編碼器。  請參閱 [媒體服務編碼 Swagger 檔中的音訊](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)。  | 請參閱以[案例為基礎的編碼](migrate-v-2-v-3-migration-scenario-based-encoding.md)主題編碼主題 |
| **轉換和作業** <!--new row -->|||
| 以作業為基礎的處理 HTTPS <!--new row --> |<!-- empty -->| 針對以檔案為基礎的工作處理，您可以使用 HTTPS URL 作為輸入。 您不需要有已儲存在 Azure 中的內容，也不需要建立資產。 |
| 作業的 ARM 範本 <!--new row --> | V2 中不存在 ARM 範本。 | 轉換可以用來建立可重複使用的設定、建立 Azure Resource Manager 範本，以及隔離多個客戶或租使用者之間的處理設定。 |
| **實況活動** <!--new row --> |||
| 串流端點 <!--new row --> | 串流端點代表可將內容直接傳遞至用戶端播放機應用程式，或傳遞至內容傳遞網路 (CDN) 以進行進一步散發的串流服務。 | 串流端點的主要維持相同。 它們可用來動態封裝、加密及傳遞 HLS 和虛線內容，以供即時和隨選串流直接從來源或透過 CDN 傳遞。  新功能包括支援更好的 Azure 監視器整合和製作圖表。 |
| 實況活動頻道 <!--new row --> | 通道負責處理即時串流內容。 通道會提供輸入端點 (內嵌 URL)，接著您再提供給即時轉碼器。 通道會從即時轉碼程式接收即時輸入資料流程，並可透過一或多個串流端點進行串流。 通道也會提供預覽端點 (預覽 URL)，您可在進一步處理和傳遞之前先用來預覽及驗證您的資料流。| 實況活動會取代 v2 API 中的通道。 它們會執行大部分的功能，並提供更多的新功能，例如即時轉譯、待命模式，以及 RTMPS 內嵌的支援。 |
| 實況活動程式 <!--new row --> | 程式可讓您控制即時資料流區段的發佈和儲存體。 通道會管理程式。 通道和程式的關聯性類似於傳統媒體，此處的通道有常數內容資料流，而程式的範圍是該通道上的某些計時事件。 您可以藉由設定屬性，指定您想要為程式保留記錄內容的時數 `ArchiveWindowLength` 。 此值最小可以設定為 5 分鐘，最大可以設定為 25 個小時。| 即時輸出現在會取代 v3 API 中的程式。 |
| 實況活動長度 <!--new row --> |<!-- empty -->| 您可以在使用媒體服務將單一位元速率貢獻摘要轉換成具有多個位元速率的輸出資料流程時，串流實況事件24/7。|
| 即時事件延遲 <!--new row --> |<!-- empty -->| 新的低延遲即時串流支援實況活動。 |
| 實況活動預覽 <!--new row --> |<!-- empty -->| 即時事件預覽版可支援動態封裝和動態加密。 這可在預覽版上實現內容保護，以及 DASH 和 HLS 封裝。 |
| 實況活動 RTMPS <!--new row --> |<!-- empty-->| 改進的 RTMPS 支援，並提供更高的穩定性及更多來源編碼器支援。 |
| 實況活動 RTMPS 安全內嵌 <!--new row --> | | 當您建立即時事件時，您會取得4個內嵌 Url。 4個內嵌 Url 幾乎相同、具有相同的串流權杖 `AppId` ，而且只有埠號碼部分是不同的。 其中兩個 URL 是 RTMPS 的主要部分和備份。| 
| 實況活動轉譯 <!--new row --> |<!-- empty--> | Azure 媒體服務會以不同的通訊協定提供影片、音訊和文字。 當您使用 MPEG-2 或 HLS/CMAF 發佈即時串流時，以及影片和音訊，我們的服務會提供 IMSC 1.1 相容 TTML 中的轉譯文字。|
| 實況活動待命模式 <!--new row --> | V2 沒有待命模式。 | [待命模式] 是新的 v3 功能，可協助管理即時事件的經常性集區。 客戶現在可以在將其轉換為執行中狀態之前，以較低的成本在待命模式中啟動實況活動。 這可改善通道的開始時間，並降低操作經常性集區的成本，以加快開始的速度。 |
| 實況活動計費 <!--new row --> | <!-- empty-->| 實況活動計費是以即時頻道計量為基礎。 |
| 即時輸出 <!--new row --> | 程式必須在建立後啟動。 | 「實況輸出」會在建立時開始，並在刪除時結束。 |

## <a name="next-steps"></a>後續步驟

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
