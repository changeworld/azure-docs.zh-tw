---
title: 遷移至媒體服務 API V3 的優點
description: 本文列出從媒體服務 v2 遷移至 v3 的優點。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 70f64813546c66c0f9e3533e09de192315f75600
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955066"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>步驟 1-瞭解遷移至媒體服務 API V3 的優點

![遷移指南標誌](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![遷移步驟2](./media/migration-guide/steps-1.svg)

建議您立即開始使用2020-05-01 版的 Azure 媒體服務 V3 API 來取得優點，因為新功能、功能和效能優化只適用于目前的 V3 API。

您可以使用正確的版本字串來變更入口網站中的 API 版本、最新的 Sdk、最新的 CLI 及 REST API。

具有 V3 的媒體服務已經大幅改進。  

## <a name="benefits-of-media-services-v3"></a>媒體服務 v3 的優點

| **V3 功能** | **優點** |
| --- | --- |
| **Azure 入口網站** | |
| Azure 入口網站更新 | Azure 入口網站已更新為包含 V3 API 實體的管理。 它可讓客戶使用入口網站來啟動即時串流、提交 V3 轉換作業、管理內容保護原則、串流端點、取得 API 存取、管理連結的儲存體帳戶，以及執行監視工作。 |
| **帳戶和儲存體** | |
| Azure 角色型存取控制 (RBAC)  | 客戶現在可以定義自己的角色，並控制媒體服務 ARM API 中每個實體的存取權。 這有助於控制 AAD 帳戶對資源的存取。 |
| 受控識別 | 受控識別可讓開發人員在 Azure AD 中提供 Azure 資源的身分識別，以免除管理認證的需求。 請參閱 [這裡](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的受控識別詳細資料。 |
| Private link 支援 | 客戶會透過其 VNet 上的 PrivateEndpoint 來存取媒體服務端點，以進行金鑰傳遞、Liveevent 和 Streamingendpoint。 |
| [客戶受控金鑰](concept-use-customer-managed-keys-byok.md) 或攜帶您自己的金鑰 (BYOK) 支援 | 客戶可以使用其 Azure Key Vault 中的金鑰，來加密其媒體服務帳戶中的資料。 |
| **資產** | |
| 資產可以有多個具有不同[動態封裝](dynamic-packaging-overview.md)和動態加密設定的[串流定位器](streaming-locators-concept.md)。 | 每個資產上允許100個串流定位器的限制。 客戶可以將媒體內容的單一複本儲存在資產中，但會以不同的串流原則或根據目標物件的內容保護原則來共用不同的串流 Url。
| **處理工作** ||
| V3 引進 [](transforms-jobs-concept.md)   以檔案為基礎之工作處理的轉換概念。 | Transform 可用來建置可重複使用的組態、建立 Azure Resource Manager 範本，並隔離多個客戶或租用戶之間的處理設定。 |
| 針對以檔案為基礎的工作處理，您可以使用 HTTP (S) URL 作為輸入。 | 您不需要有已儲存在 Azure 中的內容，也不需要建立輸入資產。 |
| **實況活動** ||
| Premium 1080p 實況活動 | 新的實況活動 SKU 可讓客戶取得最高可達 1080p 1080p 解析度的雲端編碼。 |
| 新的 [低延遲](live-event-latency.md) 即時串流支援實況活動。 | 這可讓使用者即時監看即時事件，而不是啟用此設定。 |
| 實況活動預覽支援 [動態封裝](dynamic-packaging-overview.md)   和動態加密。 | 這可啟用預覽的內容保護，以及破折號和 HLS 的封裝。 |
| 即時輸出取代程式 | 即時輸出比 v2 Api 中的程式實體更容易使用。 |
| 適用于實況活動的 RTMP 內嵌已獲得改善，並支援更多編碼器 | 提高穩定性並提供來源編碼器的彈性。 |
| 實況活動可以全天候串流 | 您可以裝載實況活動，並讓觀眾保持更長的時間。 |
| 實況活動實況轉譯 | 即時轉譯可讓客戶在實況活動廣播期間，即時自動將語音轉譯成文字。 這可大幅改善實況活動的協助工具。 |
| 實況活動的待命[模式](live-events-outputs-concept.md#standby-mode) | 處於待命狀態的實況活動成本比執行實況活動低。 如此一來，客戶就可以維護一組在幾秒鐘內準備好啟動的實況活動，成本比維護一組執行中的實況活動還要低。 針對大部分區域，待命實況活動的縮減定價將于2021年2月生效，其餘部分將于4月2021日遵循。
|**內容保護** ||
| [內容保護](content-key-policy-concept.md)  支援多索引鍵功能。 | 客戶現在可以在其串流定位器上使用多個內容加密金鑰。 |
| **監視** | |
| [Azure EventGrid](reacting-to-media-services-events.md) 通知支援 | EventGrid 通知的功能更豐富。 有更多類型的通知、更廣泛的 SDK 支援，可在您自己的應用程式中接收通知，以及更多可作為事件處理常式的現有 Azure 服務。 |
| [Azure 入口網站中的 Azure 監視器支援與整合](monitor-events-portal-how-to.md) | 這可讓客戶視覺化媒體服務帳戶配額使用量、串流端點的即時統計資料，以及實況活動的內嵌和封存統計資料。 客戶現在可以設定警示，並根據即時計量資料執行必要的動作。 |

## <a name="next-steps"></a>後續步驟

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
