---
title: Azure 應用配置常見問題解答
description: 有關 Azure 應用配置的常見問題
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 25187fd055f40e8b32d840ead2a9c54882446b88
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348779"
---
# <a name="azure-app-configuration-faq"></a>Azure 應用配置常見問題解答

本文回答了有關 Azure 應用配置的常見問題。

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>應用配置與 Azure 金鑰保存庫有什麼不同？

應用配置可説明開發人員管理應用程式設置和控制功能可用性。 它旨在簡化處理複雜配置資料的許多工。

應用配置支援：

- 階層命名空間
- 標記
- 廣泛的查詢
- 批次擷取
- 專業化管理業務
- 功能管理使用者介面

應用配置是對金鑰保存庫的補充，在大多數應用程式部署中，應並排使用這兩個庫。

## <a name="should-i-store-secrets-in-app-configuration"></a>我應該在應用配置中存儲機密嗎？

儘管應用配置提供了強化的安全性，但金鑰保存庫仍然是存儲應用程式機密的最佳位置。 金鑰保存庫提供硬體級加密、細微性訪問策略和管理操作（如證書輪換）。

您可以創建應用配置值，這些值引用存儲在金鑰保存庫中的機密。 有關詳細資訊，請參閱在[ASP.NET 核心應用中使用金鑰保存庫引用](./use-key-vault-references-dotnet-core.md)。

## <a name="does-app-configuration-encrypt-my-data"></a>應用配置是否加密我的資料？

是。 應用配置加密它保留的所有金鑰值，並加密網路通信。 金鑰名稱和標籤用作檢索配置資料的索引，並且未加密。

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>應用配置與 Azure 應用服務設置有什麼不同？

Azure 應用服務允許您為每個應用服務實例定義應用設置。 這些設置作為環境變數傳遞給應用程式代碼。 如果需要，可以將設置與特定部署槽相關聯。 有關詳細資訊，請參閱[配置應用設置](/azure/app-service/configure-common#configure-app-settings)。

相反，Azure 應用配置允許您定義可在多個應用之間共用的設置。 這包括在應用服務中運行的應用以及其他平臺。 應用程式代碼通過 .NET 和 JAVA 的配置提供程式、通過 Azure SDK 或直接通過 REST API 訪問這些設置。

您還可以在應用服務和應用配置之間導入和匯出設置。 此功能允許您根據現有應用服務設置快速設置新的應用配置存儲。 您還可以與依賴于應用服務設置的現有應用共用配置。

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>應用配置中存儲的金鑰和值是否有任何大小限制？

單個鍵值項的限制為 10 KB。

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>如何存儲多個環境（測試、暫存、生產等）的配置？

您可以控制誰可以在每商店級別訪問應用配置。 對需要不同許可權的每個環境使用單獨的存儲。 此方法提供最佳的安全隔離。

如果不需要環境之間的安全隔離，則可以使用標籤來區分配置值。 [使用標籤為不同的環境啟用不同的配置](./howto-labels-aspnet-core.md)提供了一個完整的示例。

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>使用應用配置的建議方法有哪些？

請參閱[最佳實踐](./howto-best-practices.md)。

## <a name="how-much-does-app-configuration-cost"></a>應用配置費用是多少？

有兩個定價層：

- 免費層
- 標準層。

如果在引入標準層之前創建了商店，則該商店會在一般可用性時自動移動到免費層。 您可以選擇升級到標準層或保留在免費層上。

不能將商店從標準層降級到免費層。 您可以在 Free 層中創建新存儲，然後將配置資料導入該存儲。

## <a name="which-app-configuration-tier-should-i-use"></a>我應該使用哪個應用配置層？

兩個應用配置層都提供核心功能，包括配置設置、功能標誌、金鑰保存庫引用、基本管理操作、指標和日誌。

以下是選擇層的注意事項。

- **每個訂閱的資源**：資源由單個配置存儲組成。 每個訂閱僅限於免費套餐中的一個配置存儲。 訂閱可以在標準層中具有無限數量的配置存儲。
- **每個資源的存儲**：在免費層中，每個配置存儲限制為 10 MB 的存儲。 在標準層中，每個配置存儲最多可以使用 1 GB 的存儲空間。
- **金鑰歷史記錄**：應用配置存儲對金鑰所做的所有更改的歷史記錄。 在免費套餐中，此歷史記錄存儲七天。 在標準層中，此歷史記錄存儲 30 天。
- **每天的請求**：免費套餐商店每天限制為 1，000 個請求。 一旦存儲達到 1，000 個請求，它將返回所有請求的 HTTP 狀態碼 429，直到 UTC 午夜。

    對於標準層商店，每天的前 200，000 個請求包含在每日費用中。 其他請求被計費為超額。

- **服務等級協定**：標準層的 SLA 可用性為 99.9%。 免費層沒有 SLA。
- **安全功能**：這兩個層都包括基本安全功能，包括使用 Microsoft 管理的金鑰進行加密、通過 HMAC 或 Azure 活動目錄進行身份驗證、RBAC 支援和託管標識。 標準層提供更高級的安全功能，包括專用連結支援和客戶管理的金鑰加密。
- **成本**：標準層商店每天收取使用費。 對於超過每日分配的請求，還收取超額費用。 使用免費套餐商店無需任何費用。

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>是否可以將商店從免費層升級到標準層？ 我可以將商店從標準層降級到免費層嗎？

您可以隨時從免費套餐升級到標準層。

不能將商店從標準層降級到免費層。 您可以在 Free 層中創建新存儲，然後將[配置資料導入該存儲](howto-import-export-data.md)。

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>向應用配置發出的請求數是否有任何限制？

免費套餐中的配置存儲限制為每天 1，000 個請求。 當請求速率超過每小時 20，000 個請求時，標準層中的配置存儲可能會遇到臨時限制。

當存儲達到其限制時，它將返回 HTTP 狀態碼 429，用於在時間段到期之前發出的所有請求。 回應`retry-after-ms`中的標頭在重試請求之前給出建議的等待時間（以毫秒為單位）。

如果應用程式定期遇到 HTTP 狀態碼 429 回應，請考慮重新設計它以減少發出的請求數。 有關詳細資訊，請參閱[減少對應用配置的請求](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>我的應用程式接收 HTTP 狀態碼 429 回應。 原因為何？

在這些情況下，您將收到 HTTP 狀態碼 429 回應：

* 超過免費套餐中商店的每日請求限制。
* 臨時限制，因為標準層中的存儲請求率很高。
* 頻寬使用過多。
* 嘗試在超過存儲報價時創建或修改金鑰。

檢查 429 回應的正文，瞭解請求失敗的具體原因。

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>如何接收有關新版本的公告以及與應用配置相關的其他資訊？

訂閱我們的[GitHub 公告存儲庫](https://github.com/Azure/AppConfiguration-Announcements)。

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>如何報告問題或提出建議？

你可以直接在[GitHub](https://github.com/Azure/AppConfiguration/issues)上聯繫我們。

## <a name="next-steps"></a>後續步驟

* [關於 Azure 應用程式組態](./overview.md)
