---
title: Azure 應用程式組態常見問題
description: 關於 Azure 應用程式組態的常見問題
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 25187fd055f40e8b32d840ead2a9c54882446b88
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80348779"
---
# <a name="azure-app-configuration-faq"></a>Azure 應用程式組態常見問題

本文會回答有關 Azure 應用程式組態的常見問題。

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>應用程式組態與 Azure Key Vault 有何不同？

應用程式組態可協助開發人員管理應用程式設定，並控制功能的可用性。 它的目的是要簡化處理複雜設定資料的許多工。

應用程式組態支援：

- 階層命名空間
- 標記
- 大量查詢
- 批次擷取
- 特殊化的管理作業
- 功能管理使用者介面

應用程式組態補充 Key Vault，而這兩者應該在大部分的應用程式部署中並存使用。

## <a name="should-i-store-secrets-in-app-configuration"></a>我應該將秘密儲存在應用程式組態嗎？

雖然應用程式組態提供強化的安全性，但 Key Vault 仍然是儲存應用程式秘密的最佳位置。 Key Vault 提供硬體層級加密、細微存取原則和管理作業（例如憑證輪替）。

您可以建立應用程式組態值來參考儲存在 Key Vault 中的秘密。 如需詳細資訊，請參閱[在 ASP.NET Core 應用程式中使用 Key Vault 參考](./use-key-vault-references-dotnet-core.md)。

## <a name="does-app-configuration-encrypt-my-data"></a>應用程式組態加密我的資料嗎？

是。 應用程式組態會加密它所保留的所有金鑰值，並將網路通訊加密。 金鑰名稱和標籤會用來做為抓取設定資料的索引，而且不會加密。

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>應用程式組態與 Azure App Service 設定有何不同？

Azure App Service 可讓您定義每個 App Service 實例的應用程式設定。 這些設定會以環境變數的形式傳遞給應用程式程式碼。 如有需要，您可以將設定與特定部署位置產生關聯。 如需詳細資訊，請參閱[設定應用程式設定](/azure/app-service/configure-common#configure-app-settings)。

相反地，Azure 應用程式組態可讓您定義可以在多個應用程式之間共用的設定。 這包括在 App Service 中執行的應用程式，以及其他平臺。 您的應用程式程式碼會透過 .NET 和 JAVA 的設定提供者、透過 Azure SDK，或直接透過 REST Api 來存取這些設定。

您也可以在 App Service 和應用程式組態之間匯入和匯出設定。 這項功能可讓您根據現有的 App Service 設定快速地設定新的應用程式組態存放區。 您也可以與依賴 App Service 設定的現有應用程式共用設定。

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>應用程式組態中儲存的索引鍵和值是否有任何大小限制？

單一索引鍵/值專案有 10 KB 的限制。

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>如何儲存多個環境的設定（測試、預備、生產等等）？

您可以控制誰可以存取每個商店層級的應用程式組態。 針對每個需要不同許可權的環境使用個別的存放區。 這種方法可提供最佳的安全性隔離。

如果您不需要環境之間的安全性隔離，可以使用標籤來區別設定值。 [使用標籤來為不同的環境啟用不同](./howto-labels-aspnet-core.md)的設定，提供完整的範例。

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>使用應用程式組態的建議方式為何？

請參閱[最佳做法](./howto-best-practices.md)。

## <a name="how-much-does-app-configuration-cost"></a>應用程式組態的成本是多少？

定價層有兩種：

- 免費層
- 標準層。

如果您在引進標準層之前建立了存放區，它會在公開上市時自動移至免費層。 您可以選擇升級至標準層，或保留在免費層。

您無法將存放區從標準層降級至免費層。 您可以在免費層中建立新的存放區，然後將設定資料匯入該存放區。

## <a name="which-app-configuration-tier-should-i-use"></a>應該使用哪個應用程式組態層？

應用程式組態層都提供核心功能，包括 config 設定、功能旗標、Key Vault 參考、基本管理作業、計量和記錄。

以下是選擇層的考慮。

- **每個訂**用帳戶的資源：資源是由單一設定存放區所組成。 每個訂用帳戶僅限免費層中的一個設定存放區。 在標準層中，訂用帳戶可以有不限數量的設定存放區。
- **每個資源的儲存體**：在免費層中，每個設定存放區的儲存空間限制為 10 MB。 在標準層中，每個設定存放區最多可以使用 1 GB 的儲存空間。
- **金鑰歷程記錄**：應用程式組態儲存對金鑰所做之所有變更的歷程記錄。 在免費層中，此歷程記錄會儲存7天。 在標準層中，此歷程記錄會儲存30天。
- **每日要求**數：免費層存放區受限於每日1000個要求。 一旦存放區到達1000要求，它會傳回 HTTP 狀態碼429給所有要求，直到 UTC 午夜。

    針對標準層存放區，每日費用會包含前200000個要求。 其他要求則會以超額部分計費。

- **服務等級協定**：標準層的 SLA 為99.9% 的可用性。 免費層不提供 SLA。
- **安全性功能**：兩個層級都包含基本的安全性功能，包括使用 Microsoft 管理的金鑰進行加密、透過 HMAC 或 Azure Active Directory 的驗證、RBAC 支援和受控識別。 標準層提供更先進的安全性功能，包括私人連結支援，以及使用客戶管理的金鑰進行加密。
- **成本**：標準層商店具有每日使用量費用。 對於過去每日配置的要求，也會收取超額費用。 使用免費層存放區不會產生任何費用。

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>我可以將商店從免費層升級至標準層嗎？ 我可以將存放區從標準層降級為免費層嗎？

您可以隨時從免費層升級至標準層。

您無法將存放區從標準層降級至免費層。 您可以在免費層中建立新的存放區，然後將設定[資料匯入該存放區](howto-import-export-data.md)。

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>對應用程式組態提出的要求數目是否有任何限制？

免費層中的設定存放區每天限制為1000個要求。 當要求速率超過每小時20000個要求時，標準層中的設定存放區可能會遇到暫時節流。

當存放區達到其限制時，會針對所有提出的要求傳回 HTTP 狀態碼429，直到時間週期到期為止。 `retry-after-ms`回應中的標頭會提供建議的等候時間（以毫秒為單位），然後再重試要求。

如果您的應用程式經常遇到 HTTP 狀態碼429回應，請考慮將它重新設計，以減少提出的要求數目。 如需詳細資訊，請參閱[減少對應用程式組態提出的要求](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>我的應用程式收到 HTTP 狀態碼429回應。 為什麼？

在這些情況下，您會收到 HTTP 狀態碼429回應：

* 超過免費層中存放區的每日要求限制。
* 暫時節流，因為標準層中存放區的要求率過高。
* 頻寬使用量過高。
* 在超過儲存報價時嘗試建立或修改金鑰。

如需要求失敗的特定原因，請檢查429回應的主體。

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>如何接收新版本的公告和與應用程式組態相關的其他資訊？

訂閱我們的[GitHub 公告](https://github.com/Azure/AppConfiguration-Announcements)存放庫。

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>如何報告問題或提供建議？

您可以直接在[GitHub](https://github.com/Azure/AppConfiguration/issues)上聯繫我們。

## <a name="next-steps"></a>後續步驟

* [關於 Azure 應用程式組態](./overview.md)
