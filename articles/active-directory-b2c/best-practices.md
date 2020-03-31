---
title: Azure AD B2C 的最佳實踐
titleSuffix: Azure AD B2C
description: 使用 Azure 活動目錄 B2C（Azure AD B2C）時要考慮的建議和最佳實踐。
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: a76852a6e3cc5ffcdfcac62ce29fe47c97af3df1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136154"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Azure 活動目錄 B2C 的建議和最佳實踐

以下最佳實踐和建議涵蓋了將 Azure 活動目錄 （Azure AD） B2C 集成到現有或新應用程式環境中的一些主要方面。

## <a name="fundamentals"></a>基礎

|  |  |
|--|--|
| 為大多數方案選擇使用者流 | Azure AD B2C 的標識體驗框架是服務的核心優勢。 原則可完整描述身分識別體驗，例如註冊、登入或設定檔編輯。 為了協助您設定最常見的身分識別工作，Azure AD B2C 入口網站包含預先定義且可設定的原則，稱為使用者流程。 使用使用者流，只需點擊幾下即可在幾分鐘內創建出色的使用者體驗。 [瞭解如何何時使用使用者流與自訂策略](custom-policy-overview.md#comparing-user-flows-and-custom-policies)。|
| 應用程式註冊 | 正在保護的每個應用程式（Web、本機）和 API 都必須在 Azure AD B2C 中註冊。 如果應用同時具有 Web 和本機版本的 iOS 和 Android，則可以將它們註冊為 Azure AD B2C 中的一個應用程式，並具有相同的用戶端 ID。 瞭解如何註冊[OIDC、SAML、Web 和本機應用](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications)。 詳細瞭解可在[Azure AD B2C 中使用的應用程式類型](https://docs.microsoft.com/azure/active-directory-b2c/application-types)。 |
| 移動到每月活動使用者計費 | Azure AD B2C 已經從每月活動身份驗證移動到每月活動使用者 （MAU） 計費。 大多數客戶會發現這種模式是具有成本效益的。 [瞭解有關每月活躍使用者計費的更多資訊](https://azure.microsoft.com/updates/mau-billing/)。 |

## <a name="planning-and-design"></a>規劃與設計

定義應用程式和服務體系結構、清點當前系統並計畫遷移到 Azure AD B2C。

|  |  |
|--|--|
| 構建端到端解決方案 | 在規劃 Azure AD B2C 集成時，包括應用程式的所有依賴項。 考慮當前環境中或可能需要添加到解決方案中的所有服務和產品，例如 Azure 函數、客戶關係管理 （CRM） 系統、Azure API 管理閘道和存儲服務。 考慮所有服務的安全性和可擴充性。 |
| 記錄使用者體驗 | 詳細說明您的客戶在應用程式中可以體驗的所有使用者旅程。 包括與應用程式的身份和設定檔方面交互時可能遇到的每個螢幕和任何分支流。 在規劃中包括可用性、可訪問性和當地語系化。 |
| 選擇正確的身份驗證協定 |  有關不同應用程式方案及其建議的身份驗證流的細目，請參閱[方案和支援的身份驗證流](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。 |
| 試用概念驗證 （POC） 端到端使用者體驗 | 從我們的[微軟代碼示例](code-samples.md)[和社區示例](https://github.com/azure-ad-b2c/samples)開始。 |
| 創建遷移計畫 |提前規劃可以使遷移更加順利。 瞭解有關[使用者遷移](user-migration.md)的更多內容。|
| 可用性與安全性 | 您的解決方案必須在應用程式可用性與組織的可接受風險級別之間取得適當的平衡。 |
| 將本地依賴項移動到雲 | 為了説明確保有彈性的解決方案，請考慮將現有應用程式依賴項移動到雲中。 |
| 將現有應用遷移到b2clogin.com | 2020 年 12 月 4 日，所有 Azure AD B2C 租戶的 login.microsoftonline.com棄用生效。 [深入了解](b2clogin.md)。 |

## <a name="implementation"></a>實作

在實施階段，請考慮以下建議。

|  |  |
|--|--|
| 使用 Azure AD B2C 擴展編輯視覺化工作室代碼的自訂策略 | [從視覺化工作室代碼市場](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)下載視覺化工作室代碼和這個社區構建的擴展。 雖然不是微軟的官方產品，但 Visual Studio Code 的 Azure AD B2C 擴展包括幾個功能，可説明更輕鬆地使用自訂策略。 |
| 瞭解如何對 Azure AD B2C 進行故障排除 | 瞭解如何在開發期間[對自訂策略進行故障排除](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications)。 瞭解正常身份驗證流的外觀，並使用工具發現異常和錯誤。 例如，使用[應用程式見解](troubleshoot-with-application-insights.md)查看使用者旅程的輸出日誌。 |
| 利用我們經過驗證的自訂策略模式庫 | 查找[多個](https://github.com/azure-ad-b2c/samples)增強的 Azure AD B2C 客戶標識和訪問管理 （CIAM） 使用者旅程的示例。 |


## <a name="testing"></a>測試

測試並自動執行 Azure AD B2C 實現。

|  |  |
|--|--|
| 全球流量帳戶 | 使用來自不同全域位址的流量源來測試性能和當地語系化要求。 確保所有 HTM、CSS 和依賴項都能滿足您的性能需求。 |
| 功能和 UI 測試 | 端到端測試使用者流。 使用硒、VS Web 測試等每隔幾分鐘添加一次合成測試。 |
| 筆測試 | 在使用解決方案之前，請執行滲透測試練習，以驗證所有元件是否安全，包括任何協力廠商依賴關係。 驗證您是否已使用訪問權杖保護 API，並為應用程式方案使用正確的身份驗證協定。 瞭解有關[滲透測試](https://docs.microsoft.com/azure/security/fundamentals/pen-testing)和[微軟雲統一滲透測試參與規則的更多。](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) |
| A/B 測試 | 使用少量隨機使用者飛行新功能，然後再向整個總體推出。 在 Azure AD B2C 中啟用了 JavaScript 後，您可以與 A/B 測試控管（如優化、清晰度等）集成。 |
| 負載測試 | Azure AD B2C 可以縮放，但應用程式只有在其所有依賴項都可以縮放時才能擴展。 載入測試 API 和 CDN。 |
| 節流 |  如果在短時間內從同一源發送的請求過多，Azure AD B2C 會限制流量。 在負載測試時使用多個流量源，並在應用程式中`AADB2C90229`正常處理錯誤代碼。 |
| 自動化 | 使用連續集成和交付 （CI/CD） 管道來自動執行測試和部署，例如[Azure DevOps](deploy-custom-policies-devops.md)。 |

## <a name="operations"></a>作業

管理 Azure AD B2C 環境。

|  |  |
|--|--|
| 創建多個環境 | 為了更輕鬆地操作和部署部署部署，請為開發、測試、預生產和生產創建單獨的環境。 為每個租戶創建 Azure AD B2C 租戶。 |
| 對自訂策略使用版本控制 | 請考慮為 Azure AD B2C 自訂策略使用 GitHub、Azure 存儲庫或其他基於雲的版本控制系統。 |
| 使用 Microsoft 圖形 API 自動管理 B2C 租戶 | 微軟圖形 API：<br/>管理[身份體驗框架](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta)（自訂策略）<br/>[鑰匙](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[使用者流程](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| 與 Azure DevOps 集成 | [CI/CD 管道](deploy-custom-policies-devops.md)使在不同環境之間移動代碼變得容易，並確保隨時做好生產準備。   |
| 與 Azure 監視器整合 | [稽核記錄事件](view-audit-logs.md)僅保留七天。 [與 Azure 監視器集成](azure-monitor.md)以保留日誌以長期使用，或與協力廠商安全資訊和事件管理 （SIEM） 工具集成，以深入瞭解您的環境。 |
| 設置活動警報和監視 | 使用應用程式見解跟蹤 Azure AD B2C 中的[使用者行為](active-directory-b2c-custom-guide-eventlogger-appins.md)。 |


## <a name="support-and-status-updates"></a>支援和狀態更新

隨時瞭解服務狀態並查找支援選項。

|  |  |
|--|--|
| [服務更新](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  隨時瞭解 Azure AD B2C 產品更新和公告。 |
| [Microsoft 支援服務](support-options.md) | 為 Azure AD B2C 技術問題提交支援請求。 計費及訂用帳戶管理支援均為免費提供。 |
| [Azure 狀態](https://status.azure.com/status) | 查看所有 Azure 服務的當前運行狀況狀態。 |