---
title: Azure 應用程式組態常見問題
description: 閱讀常見問題的解答 (常見問題) 有關 Azure 應用程式組態的詳細資訊，例如它與 Azure Key Vault 有何不同。
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: ef4633953f7ac03737608124309d94e436913794
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715441"
---
# <a name="azure-app-configuration-faq"></a>Azure 應用程式組態常見問題

本文將回答有關 Azure 應用程式組態的常見問題。

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>應用程式設定與 Azure Key Vault 有何不同？

應用程式設定可協助開發人員管理應用程式設定及控制功能的可用性。 它的目的是要簡化許多使用複雜設定資料的工作。

應用程式設定支援：

- 階層命名空間
- 標記
- 大量查詢
- 批次擷取
- 特殊化的管理作業
- 功能管理使用者介面

應用程式設定補充了 Key Vault，而這兩個應用程式應該在大部分的應用程式部署中並行使用。

## <a name="should-i-store-secrets-in-app-configuration"></a>我應該將秘密儲存在應用程式設定嗎？

雖然應用程式設定提供強化的安全性，但 Key Vault 仍是儲存應用程式秘密的最佳位置。 Key Vault 提供硬體層級的加密、細微的存取原則，以及像是憑證輪替的管理作業。

您可以建立參考儲存在 Key Vault 中之秘密的應用程式設定值。 如需詳細資訊，請參閱 [在 ASP.NET Core 應用程式中使用 Key Vault 參考](./use-key-vault-references-dotnet-core.md)。

## <a name="does-app-configuration-encrypt-my-data"></a>應用程式設定是否會將我的資料加密？

是。 應用程式設定會加密其保存的所有金鑰值，並將網路通訊加密。 索引鍵名稱和標籤是用來作為抓取設定資料的索引，且不會加密。

## <a name="where-does-data-stored-in-app-configuration-reside"></a>應用程式設定中儲存的資料位於何處？ 

儲存在應用程式設定中的客戶資料位於客戶的應用程式設定存放區建立所在的區域。 應用程式設定可能會將資料複寫到 [配對的區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) 以進行資料恢復，但不會複寫或移動其地理位置以外的客戶資料，如 [Azure 中的資料](https://azure.microsoft.com/global-infrastructure/data-residency/)落地所定義。 客戶與終端使用者可以從全球任何地點移動、複製或存取其客戶資料。

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>應用程式設定與 Azure App Service 設定有何不同？

Azure App Service 可讓您定義每個 App Service 實例的應用程式設定。 這些設定會以環境變數的形式傳遞至應用程式程式碼。 您可以視需要將設定關聯至特定的部署位置。 如需詳細資訊，請參閱 [設定應用程式設定](/azure/app-service/configure-common#configure-app-settings)。

相反地，Azure 應用程式組態可讓您定義可在多個應用程式之間共用的設定。 這包括在 App Service 及其他平臺中執行的應用程式。 您的應用程式程式碼會透過適用于 .NET 和 JAVA 的設定提供者、透過 Azure SDK，或直接透過 REST Api 來存取這些設定。

您也可以在 App Service 和應用程式設定之間匯入和匯出設定。 這項功能可讓您根據現有的 App Service 設定，快速設定新的應用程式設定存放區。 您也可以與依賴 App Service 設定的現有應用程式共用設定。

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>在應用程式設定中儲存的金鑰和值是否有任何大小限制？

單一索引鍵/值專案有 10 KB 的限制。

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>如何將多個環境的設定儲存 (測試、預備、生產等) ？

您可以控制誰可以存取每個商店層級的應用程式設定。 針對每個需要不同許可權的環境使用不同的存放區。 這種方法可提供最佳的安全性隔離。

如果您不需要環境之間的安全性隔離，則可以使用標籤來區分設定值。 [使用標籤來啟用不同環境的不同](./howto-labels-aspnet-core.md) 設定會提供完整的範例。

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>使用應用程式設定的建議方法為何？

請參閱 [最佳做法](./howto-best-practices.md)。

## <a name="how-much-does-app-configuration-cost"></a>應用程式設定的成本有多高？

有兩個定價層：

- 免費層
- 標準層。

如果您在引進標準層之前建立了存放區，它會在正式推出時自動移至免費層。 您可以選擇升級至標準層，或保留在免費層。

您無法將標準層的存放區降級至免費層。 您可以在免費層中建立新的存放區，然後將設定資料匯入該存放區。

## <a name="which-app-configuration-tier-should-i-use"></a>我應該使用哪一種應用程式佈建層？

這兩個應用程式設定層都提供核心功能，包括配置設定、功能旗標、Key Vault 參考、基本管理作業、計量和記錄。

以下是選擇階層的考慮。

- **每一訂**用帳戶的資源：資源是由單一設定存放區所組成。 每個訂用帳戶僅限免費層中的一個設定存放區。 訂用帳戶在標準層中可以有無限數量的設定存放區。
- **每個資源的儲存體**：在免費層中，每個設定存放區的儲存空間限制為 10 MB。 在標準層中，每個設定存放區最多可使用 1 GB 的儲存體。
- **金鑰歷程記錄**：應用程式設定會儲存對金鑰所做之所有變更的歷程記錄。 在免費層中，此歷程記錄會儲存七天。 在標準層中，此歷程記錄會儲存30天。
- **每日要求**數：免費層存放區受限於每日1000個要求。 一旦商店抵達1000要求，它就會傳回所有要求的 HTTP 狀態碼429，直到 UTC 午夜為止。

    針對標準層商店，每日的前200000要求都會包含在每日費用中。 額外的要求會以超額部分的方式計費。

- **服務等級協定**：標準層具有99.9% 可用性的 SLA。 免費層沒有 SLA。
- **安全性功能**：這兩個層級都包含基本安全性功能，包括使用 Microsoft 管理的金鑰進行加密、透過 HMAC 或 Azure Active Directory 進行驗證、Azure RBAC 支援和受控識別。 標準層提供更先進的安全性功能，包括使用客戶管理的金鑰來 Private Link 支援和加密。
- **成本**：標準層商店具有每日使用量費用。 每日配置過去的要求也會收取超額費用。 使用免費層存放區不會產生任何費用。

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>是否可以將商店從免費層升級至標準層？ 我可以將存放區從標準層降級至免費層嗎？

您可以隨時從免費層升級至標準層。

您無法將標準層的存放區降級至免費層。 您可以在免費層中建立新的存放區，然後將設定 [資料匯入該存放區](howto-import-export-data.md)。

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>對應用程式設定所提出的要求數目是否有任何限制？

免費層中的設定存放區限制為每天1000個要求。 當要求速率超過每小時20000個要求時，標準層中的設定存放區可能會遇到暫時節流。

當存放區達到限制時，它會針對所有要求傳回 HTTP 狀態碼429，直到時間週期到期為止。 `retry-after-ms`回應中的標頭會提供建議的等候時間，以毫秒為) 單位來重試要求 (。

如果您的應用程式會定期遇到 HTTP 狀態碼429回應，請考慮重新設計，以減少提出的要求數目。 如需詳細資訊，請參閱 [減少對應用程式設定所提出的要求](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>我的應用程式收到 HTTP 狀態碼429回應。 為何會這樣？

在這些情況下，您將會收到 HTTP 狀態碼429回應：

* 超過免費層中的商店每日要求限制。
* 因為標準層中的存放區要求率很高，所以暫時節流。
* 過度的頻寬使用量。
* 當超過儲存體報價時嘗試建立或修改金鑰。

檢查429回應的本文，找出要求失敗的特定原因。

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>如何接收新版本的公告以及與應用程式設定相關的其他資訊？

訂閱我們的 [GitHub 公告](https://github.com/Azure/AppConfiguration-Announcements)存放庫。

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>如何報告問題或提供建議？

您可以直接在 [GitHub](https://github.com/Azure/AppConfiguration/issues)上聯繫我們。

## <a name="next-steps"></a>後續步驟

* [關於 Azure 應用程式組態](./overview.md)
