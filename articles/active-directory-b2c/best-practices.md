---
title: Azure AD B2C 的最佳做法
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C （Azure AD B2C）時要考慮的建議和最佳作法。
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: b16c60130836cf0e3b38092b894129f503ee6e83
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141675"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的建議和最佳作法

下列最佳做法和建議涵蓋將 Azure Active Directory （Azure AD） B2C 整合到現有或新的應用程式環境中的一些主要層面。

## <a name="fundamentals"></a>基礎

|  |  |
|--|--|
| 在大部分情況下選擇使用者流程 | Azure AD B2C 的 Identity Experience Framework 是服務的核心強度。 原則可完整描述身分識別體驗，例如註冊、登入或設定檔編輯。 為了協助您設定最常見的身分識別工作，Azure AD B2C 入口網站包含預先定義且可設定的原則，稱為使用者流程。 透過使用者流程，只要按幾下滑鼠，您就可以在幾分鐘內建立絕佳的使用者體驗。 [瞭解何時使用使用者流程和自訂原則](custom-policy-overview.md#comparing-user-flows-and-custom-policies)。|
| 應用程式註冊 | 每個受保護的應用程式（web、原生）和 API 都必須在 Azure AD B2C 中註冊。 如果應用程式同時具有 web 和原生版本的 iOS 和 Android，您可以使用相同的用戶端識別碼，在 Azure AD B2C 中將它們註冊為一個應用程式。 瞭解如何[註冊 OIDC、SAML、web 和原生應用程式](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications)。 深入瞭解[可在 Azure AD B2C 中使用的應用程式類型](https://docs.microsoft.com/azure/active-directory-b2c/application-types)。 |
| 移至每月作用中使用者計費 | Azure AD B2C 已從每月作用中的驗證移至每月作用中使用者（MAU）計費。 大部分的客戶都會發現這個模型符合成本效益。 [深入瞭解每月作用中使用者計費](https://azure.microsoft.com/updates/mau-billing/)。 |

## <a name="planning-and-design"></a>規劃與設計

定義您的應用程式和服務架構、清查目前的系統，並規劃您的 Azure AD B2C 遷移。

|  |  |
|--|--|
| 架構端對端解決方案 | 在規劃 Azure AD B2C 整合時，包含您所有應用程式的相依性。 請考慮您環境中目前或可能需要加入解決方案的所有服務和產品，例如 Azure Functions、客戶關係管理（CRM）系統、Azure API 管理閘道和儲存體服務。 請考慮所有服務的安全性和擴充性。 |
| 記錄您的使用者體驗 | 詳細說明您的客戶在您的應用程式中可以體驗的所有使用者旅程。 包含每個畫面，以及它們在與應用程式的身分識別和設定檔層面互動時可能會遇到的任何分支流程。 在您的規劃中包含可用性、協助工具和當地語系化。 |
| 選擇正確的驗證通訊協定 |  如需不同應用程式案例及其建議的驗證流程的明細，請參閱[案例和支援的驗證流程](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。 |
| 試驗概念證明（POC）端對端使用者體驗 | 從我們的[Microsoft 程式碼範例](code-samples.md)和[社區範例](https://github.com/azure-ad-b2c/samples)開始。 |
| 建立遷移計畫 |提早規劃可以更順利地進行遷移。 深入瞭解[使用者遷移](user-migration.md)。|
| 可用性與安全性的比較 | 您的解決方案必須在應用程式可用性和貴組織可接受的風險層級之間取得適當的平衡。 |
| 將內部部署相依性移至雲端 | 若要協助確保彈性的解決方案，請考慮將現有的應用程式相依性移至雲端。 |
| 將現有的應用程式遷移至 b2clogin.com | Login.microsoftonline.com 的淘汰將會在2020年12月4日生效，適用于所有 Azure AD B2C 租使用者。 [深入了解](b2clogin.md)。 |

## <a name="implementation"></a>實作

在執行階段中，請考慮下列建議。

|  |  |
|--|--|
| 使用 Visual Studio Code 的 Azure AD B2C 延伸模組編輯自訂原則 | [從 Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)下載 Visual Studio Code 和此社區建立的延伸模組。 雖然不是官方的 Microsoft 產品，Visual Studio Code 的 Azure AD B2C 延伸模組包含數項功能，可協助您更輕鬆地使用自訂原則。 |
| 瞭解如何疑難排解 Azure AD B2C | 瞭解如何在開發期間針對[自訂原則進行疑難排解](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications)。 瞭解一般驗證流程的樣子，並使用工具來探索異常和錯誤。 例如，使用[Application Insights](troubleshoot-with-application-insights.md)來審查使用者旅程的輸出記錄。 |
| 運用經證實的自訂原則模式程式庫 | 尋找數個增強 Azure AD B2C 客戶身分識別與存取管理（CIAM）使用者旅程的[範例](https://github.com/azure-ad-b2c/samples)。 |


## <a name="testing"></a>測試

測試 Azure AD B2C 的執行並將其自動化。

|  |  |
|--|--|
| 全域流量的帳戶 | 使用來自不同全域位址的流量來源來測試效能和當地語系化需求。 請確定所有 HTMLs、CSS 和相依性都能符合您的效能需求。 |
| 功能和 UI 測試 | 端對端測試使用者流程。 使用 Selenium、VS Web Test 等每隔幾分鐘加入綜合測試。 |
| 畫筆-測試 | 在繼續使用您的解決方案之前，請先執行滲透測試練習，確認所有元件都是安全的，包括任何協力廠商相依性。 請確認您已使用存取權杖來保護您的 Api，並在您的應用程式案例中使用正確的驗證通訊協定。 深入瞭解[滲透測試](https://docs.microsoft.com/azure/security/fundamentals/pen-testing)和 Engagement 的[Microsoft Cloud 整合滲透測試規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)。 |
| A/B 測試 | 將您的新功能與一組小型的隨機使用者一起飛行，再推出整個擴展。 在 Azure AD B2C 中啟用 JavaScript 之後，您就可以與 A/B 測試控管（例如 Optimizely、清楚明瞭）及其他專案整合。 |
| 負載測試 | Azure AD B2C 可以進行調整，但您的應用程式只有在其所有相依性都可以調整時，才可以進行調整。 負載測試您的 Api 和 CDN。 |
| 節流 |  如果在短時間內從相同來源傳送太多要求，Azure AD B2C 會節流流量。 在負載測試時使用數個流量來源，並`AADB2C90229`在您的應用程式中正常處理錯誤碼。 |
| 自動化 | 使用持續整合和傳遞（CI/CD）管線來自動化測試和部署，例如[Azure DevOps](deploy-custom-policies-devops.md)。 |

## <a name="operations"></a>作業

管理您的 Azure AD B2C 環境。

|  |  |
|--|--|
| 建立多個環境 | 為了更輕鬆地執行作業和部署，請為開發、測試、生產前和生產環境建立不同的環境。 為每個租使用者建立 Azure AD B2C 的租使用者。 |
| 針對您的自訂原則使用版本控制 | 針對您的 Azure AD B2C 自訂原則，請考慮使用 GitHub、Azure Repos 或其他雲端式版本控制系統。 |
| 使用 Microsoft Graph API 來自動化 B2C 租使用者的管理 | Microsoft Graph Api：<br/>管理[Identity Experience Framework](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) （自訂原則）<br/>[[索引鍵]](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[使用者流程](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| 與 Azure DevOps 整合 | [CI/CD 管線](deploy-custom-policies-devops.md)可讓您輕鬆地在不同環境之間移動程式碼，並確保隨時都能準備好生產環境。   |
| 與 Azure 監視器整合 | [Audit log 事件](view-audit-logs.md)只會保留七天。 [與 Azure 監視器整合](azure-monitor.md)以保留記錄以供長期使用，或與協力廠商安全性資訊和事件管理（SIEM）工具整合，以深入瞭解您的環境。 |
| 設定主動警示和監視 | 使用 Application Insights 追蹤 Azure AD B2C 中的[使用者行為](active-directory-b2c-custom-guide-eventlogger-appins.md)。 |


## <a name="support-and-status-updates"></a>支援和狀態更新

隨時掌握服務的狀態，並尋找支援選項。

|  |  |
|--|--|
| [服務更新](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Azure AD B2C 的產品更新和公告，隨時掌握最新消息。 |
| [Microsoft 支援服務](support-options.md) | 提出 Azure AD B2C 技術問題的支援要求。 計費及訂用帳戶管理支援均為免費提供。 |
| [Azure 狀態](https://status.azure.com/status) | 查看所有 Azure 服務的目前健全狀況狀態。 |
