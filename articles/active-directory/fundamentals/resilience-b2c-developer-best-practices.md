---
title: 透過開發人員使用 Azure AD B2C 的最佳作法進行復原 |Microsoft Docs
description: 使用 Azure AD B2C 的客戶身分識別與存取管理中的開發人員最佳做法進行復原
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecde474abf3c814b7c3afa4ae18d044868785cf5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919464"
---
# <a name="resilience-through-developer-best-practices"></a>透過開發人員最佳作法的復原能力

在本文中，我們將分享一些以我們與大型客戶合作的經驗為基礎的學習。 您可以在服務的設計和實行中考慮這些建議。

![顯示開發人員體驗元件的影像](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>使用 Microsoft 驗證程式庫 (MSAL) 

[Microsoft 驗證程式庫 (MSAL) ](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) ，以及[適用于 ASP.NET 的 microsoft 身分識別 web 驗證程式庫](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries)，可簡化應用程式所需的權杖的取得、管理、快取和重新整理。 這些程式庫是專門為了支援 Microsoft 身分識別而優化的，包括改善應用程式復原能力的功能。

開發人員應該採用最新版本的 MSAL 並保持最新狀態。 瞭解如何在您的應用程式中 [提高驗證和授權的復原能力](resilience-app-development-overview.md) 。 可能的話，請避免執行您自己的驗證堆疊，並使用妥善建立的程式庫。

## <a name="optimize-directory-reads-and-writes"></a>優化目錄讀取和寫入

Microsoft Azure AD B2C 目錄服務一天支援數十億次驗證。 它是針對每秒讀取率的高比率所設計。 將您的寫入優化以將相依性降至最低並提高復原能力。

### <a name="how-to-optimize-directory-reads-and-writes"></a>如何優化目錄讀取和寫入

- 請避免在登入時將函 **式寫入目錄**：在您的自訂原則中) 不含前置條件 (if 子句時，絕對不會執行寫入。 需要在登入時寫入的一個使用案例，就是 [使用者密碼](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration)的即時移轉。 避免任何需要在每次登入時寫入的案例。

  - 使用者旅程圖中的[前置條件](https://docs.microsoft.com/azure/active-directory-b2c/userjourneys)如下所示：

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - 藉 [由與 CAPTCHA 系統整合](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration)，為 bot 驅動的註冊打造阻礙。

  - 使用 [負載測試範例](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing) 來模擬註冊和登入。 

- **瞭解節流**：目錄會同時執行應用程式和租使用者層級的節流規則。 讀取/取得、寫入/POST、更新/PUT 和刪除/刪除作業有更多的速率限制，而且每個作業都有不同的限制。

  - 登入時的寫入會落在新使用者的貼文下，或放入現有的使用者。

  - 在每次登入時建立或更新使用者的自訂原則，可能會達到應用層級的 PUT 或 POST 速率限制。 透過 Azure AD 或 Microsoft Graph 更新目錄物件時，適用相同的限制。 同樣地，檢查讀取以將每次登入的讀取次數維持在最小值。

  - 估計尖峰負載以預測目錄寫入的速率，並避免節流。 尖峰流量估計值應包括註冊、登入和多重要素驗證 (MFA) 等動作的估計值。 請務必同時測試 Azure AD B2C 系統和您的應用程式是否有尖峰流量。 當您的下游應用程式或服務不會進行節流處理時，Azure AD B2C 可能會處理負載。

  - 瞭解並規劃您的遷移時程表。 當您計畫使用 Microsoft Graph 將使用者遷移至 Azure AD B2C 時，請考慮使用應用程式和租使用者限制來計算完成使用者遷移所需的時間。 如果您使用兩個應用程式來分割使用者建立作業或腳本，您可以使用每個應用程式的限制。 它仍然需要保持低於每個租使用者閾值。

  - 瞭解您的遷移作業對其他應用程式的影響。 請考慮其他信賴應用程式所提供的即時流量，以確保您不會導致租使用者層級的節流，以及您的即時應用程式資源耗盡。 如需詳細資訊，請參閱 [Microsoft Graph 節流指導](https://docs.microsoft.com/graph/throttling)方針。
  
## <a name="extend-token-lifetimes"></a>擴充權杖存留期

在不太可能發生的情況下，當 Azure AD B2C authentication 服務無法完成新的註冊和登入時，您仍然可以為登入的使用者提供緩和措施。 使用 [設定時，](https://docs.microsoft.com/azure/active-directory-b2c/configure-tokens)您可以允許已登入的使用者繼續使用應用程式，而不會發生任何察覺中斷，直到使用者登出應用程式或 [會話](https://docs.microsoft.com/azure/active-directory-b2c/session-behavior) 超時，因為沒有活動。

您的商務需求和所需的終端使用者體驗，會決定您的權杖重新整理頻率， (Spa) 的 web 和單頁應用程式。

### <a name="how-to-extend-token-lifetimes"></a>如何擴充權杖存留期

- **Web 應用程式**：針對驗證權杖在登入開始時驗證的 web 應用程式，應用程式相依于會話 cookie 以繼續擴充會話的有效性。

  - 藉由執行輪流會話時間，讓使用者保持登入狀態，以根據使用者活動繼續更新會話。 如果有長期權杖發行中斷，這些會話時間可進一步增加為應用程式上的 onetime 設定。 將會話的存留期保持在允許的最大值。

- **Spa**： SPA 可能相依于存取權杖以呼叫 api。 SPA 通常使用不會產生重新整理權杖的隱含流程。 如果瀏覽器仍與 Azure AD B2C 使用中的會話，則 SPA 可以使用隱藏的 iframe 來對授權端點執行新的權杖要求。 針對 Spa，有幾個選項可用來允許使用者繼續使用應用程式。

  - 延伸存取權杖的有效期間，以符合您的商務需求。

  - 建立您的應用程式，以使用 API 閘道作為驗證 proxy。 在此設定中，SPA 會在沒有任何驗證的情況下載入，而且會對 API 閘道進行 API 呼叫。 API 閘道會使用以原則為基礎的 [授權碼授](https://oauth.net/2/grant-types/authorization-code/) 與來將使用者傳送至登入程式，並驗證使用者。 然後，API 閘道與用戶端之間的驗證會話會使用驗證 cookie 進行維護。 Api 是使用 API 閘道取得的權杖或其他直接驗證方法（例如憑證、用戶端認證或 API 金鑰），從 API 閘道服務。

  - 將[您的 SPA 從隱含授與授](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/)與[授權碼授與流程](https://docs.microsoft.com/azure/active-directory-b2c/implicit-flow-single-page-application)，以取得程式碼交換的證明金鑰 (PKCE) 和跨原始資源分享 (CORS) 支援。 將您的應用程式從 MSAL.js 1.x 遷移至 MSAL.js 2.x，以實現 Web 應用程式的復原能力。

  - 針對行動應用程式，建議您同時擴充重新整理和存取權杖存留期。

- **後端或微服務應用程式**：由於後端 (daemon) 應用程式不是互動式的，且不在使用者內容中，因此權杖遭竊的潛在客戶會大幅降低。 建議您在安全性和存留期之間取得平衡，並設定長的權杖存留期。

## <a name="configure-single-sign-on"></a>設定單一登入

使用 [單一登入 (SSO) ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)，使用者只要使用單一帳戶登入一次，就可以存取多個應用程式。 無論平臺或功能變數名稱為何，應用程式都可以是 web、行動或單一頁面應用程式 (SPA) 。 當使用者一開始登入應用程式時，Azure AD B2C 保存以 [cookie 為基礎的會話](https://docs.microsoft.com/azure/active-directory-b2c/session-overview)。

在後續的驗證要求中，Azure AD B2C 讀取並驗證以 cookie 為基礎的會話，並在不提示使用者再次登入的情況下發出存取權杖。 如果在原則或應用程式上設定了具有有限範圍的 SSO，稍後存取其他原則和應用程式時，將需要全新的驗證。

### <a name="how-to-configure-sso"></a>如何設定 SSO

[將 SSO 設定](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start) 為整個租使用者 (預設) ，以允許您租使用者中的多個應用程式和使用者流程共用同一個使用者會話。 整個租使用者的設定提供最高的全新驗證復原。  

## <a name="safe-deployment-practices"></a>安全部署實務

最常見的服務 disrupters 是程式碼和設定變更。 採用持續整合和持續傳遞 (CICD) 的程式和工具可協助大規模快速部署，並在測試和部署到生產環境時減少人為錯誤。 採用 CICD 來減少錯誤、提高效率及一致性。 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) 是 CICD 的範例。

## <a name="web-application-firewall"></a>Web 應用程式防火牆

保護您的應用程式免于遭受已知的弱點，例如分散式阻斷服務 (DDoS) 攻擊、SQL 插入、跨網站腳本、遠端程式碼執行，以及許多其他 [OWASP 前10個](https://owasp.org/www-project-top-ten/)檔中所述。  (WAF) 部署 Web 應用程式防火牆，可以抵禦常見的惡意探索和弱點。

- 使用 Azure [WAF](https://docs.microsoft.com/azure/web-application-firewall/overview)，可針對攻擊提供集中式保護。

- 使用 WAF 搭配 Azure AD [Identity Protection 和條件式存取，以在使用 Azure AD B2C 時提供多層保護](https://docs.microsoft.com/azure/active-directory-b2c/conditional-access-identity-protection-overview) 。  

## <a name="secrets-rotation"></a>祕密輪替

Azure AD B2C 使用應用程式、Api、原則和加密的秘密。 秘密安全驗證、外部互動和儲存。 美國國家標準與技術局 (NIST) 呼叫特定金鑰獲得授權可供合法實體 cryptoperiod 使用的時間範圍。 選擇正確的 [cryptoperiod](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) 長度，以符合您的商務需求。 開發人員需要手動設定到期日，並在到期前輪替秘密。

### <a name="how-to-implement-secret-rotation"></a>如何執行秘密輪替

- 針對支援的資源使用 [受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 識別，向支援 Azure AD authentication 的任何服務進行驗證。 當您使用受控識別時，可以自動管理資源，包括輪替認證。

- 請清查 Azure AD B2C 中設定的所有 [金鑰和憑證](https://docs.microsoft.com/azure/active-directory-b2c/policy-keys-overview) 。 這份清單可能包含用於自訂原則、 [api](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api)、簽署識別碼權杖及 SAML 憑證的金鑰。

- 使用 CICD，將即將在兩個月內到期的秘密，從預期的尖峰季節中旋轉。 與憑證相關聯之私密金鑰的建議最大 cryptoperiod 為一年。

- 主動監視和輪替 API 存取認證，例如密碼和憑證。

## <a name="test-rest-apis"></a>測試 REST Api

在恢復功能的環境中，REST Api 的測試必須包含驗證（HTTP 代碼、回應承載、標頭和效能）。 測試不應只包含滿意的路徑測試，也可以檢查 API 是否能正常處理問題案例。

### <a name="how-to-test-apis"></a>如何測試 Api

建議您的測試計劃包含 [完整的 API 測試](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing)。 如果您正在規劃即將推出的激增，因為促銷或假日的流量，您必須使用新的估價來修訂負載測試。 在開發人員環境中 (CDN) 進行 Api 和內容傳遞網路的負載測試，而不是在生產環境中執行。

## <a name="next-steps"></a>後續步驟

- [Azure AD B2C 開發人員的復原能力資源](resilience-b2c.md)
  - [復原的終端使用者體驗](resilient-end-user-experience.md)
  - [具有外部進程的復原介面](resilient-external-processes.md)
  - [透過監視及分析的復原能力](resilience-with-monitoring-alerting.md)
- [在您的驗證基礎結構中建立恢復功能](resilience-in-infrastructure.md)
- [提高應用程式中驗證和授權的復原能力](resilience-app-development-overview.md)
