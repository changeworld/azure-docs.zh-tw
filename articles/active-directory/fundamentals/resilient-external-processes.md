---
title: 使用 Azure AD B2C 的外部進程的彈性介面 |Microsoft Docs
description: 使用外部進程建立彈性介面的方法
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
ms.openlocfilehash: 58ef522f5b048db0ef120625d9e894c8e14c070e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724402"
---
# <a name="resilient-interfaces-with-external-processes"></a>具有外部進程的復原介面

在本文中，我們將為您提供指導方針，說明如何在使用者旅程圖中規劃及實施 RESTFul Api，並讓您的應用程式更能因應 API 失敗的彈性。

![影像顯示具有外部進程元件的介面](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>確定 Api 的位置正確

身分識別體驗架構 (IEF) 原則可讓您使用 [RESTFUL API 技術設定檔](../../active-directory-b2c/restful-technical-profile.md)來呼叫外部系統。 外部系統不受 IEF 執行時間環境的控制，而且是潛在的失敗點。

### <a name="how-to-manage-external-systems-using-apis"></a>如何使用 Api 管理外部系統

- 呼叫介面以存取某些資料時，請檢查資料是否要驅動驗證決策。 評估此資訊是否對應用程式的核心功能而言是不可或缺的。 例如，電子商務與次要功能（例如系統管理）的比較。 如果驗證不需要此資訊，而且只在次要案例中需要，則請考慮將呼叫移至應用程式邏輯。

- 如果驗證所需的資料相當靜態且較小，而且沒有其他商務理由要從目錄中外部化，則請考慮將它放在目錄中。

- 盡可能從預先驗證的路徑移除 API 呼叫。 如果您無法這樣做，則必須針對阻絕服務 (DoS) 和分散式阻絕服務 (DDoS) 攻擊，在您的 Api 之前放置嚴格保護。 攻擊者可以載入登入頁面，並嘗試使用 DoS 攻擊來氾濫您的 API，並讓您的應用程式癱瘓。 例如，在登入中使用 CAPTCHA，註冊流程可以提供協助。

- 在使用身分識別提供者登入或建立使用者之前，請盡可能使用內 [建註冊使用者流程的 API 連接器](../../active-directory-b2c/api-connectors-overview.md) ，以與 web api 整合。 由於使用者流程已經過廣泛的測試，因此您可能不需要執行使用者流程層級的功能、效能或規模測試。 您仍然需要測試應用程式的功能、效能和規模。

- Azure AD RESTFul API [技術設定檔](../../active-directory-b2c/restful-technical-profile.md) 不會提供任何快取行為。 相反地，RESTFul API 設定檔會執行重試邏輯和原則內建的超時時間。

- 對於需要寫入資料的 Api，請將工作排入佇列，讓背景工作角色執行這類工作。 您可以使用 [Azure 佇列](../../storage/queues/storage-queues-introduction.md) 之類的服務。 這會讓 API 傳回有效率地提高原則執行效能。  

## <a name="api-error-handling"></a>API 錯誤處理

當 Api 存留在 Azure AD B2C 系統之外時，技術設定檔中必須有適當的錯誤處理。 請確定使用者有適當的通知，且應用程式可以正常地處理失敗。

### <a name="how-to-gracefully-handle-api-errors"></a>如何正常處理 API 錯誤

- API 可能會因各種原因而失敗，讓您的應用程式能夠復原這類失敗。 如果 API 無法完成要求，則傳回[HTTP 4xx 錯誤訊息](../../active-directory-b2c/restful-technical-profile.md#returning-validation-error-message)。 在 Azure AD B2C 原則中，請嘗試正常處理 API 無法使用的情況，而且可能會呈現較少的體驗。

- [正常處理暫時性錯誤](../../active-directory-b2c/restful-technical-profile.md#error-handling)。 RESTFul API 設定檔可讓您設定各種 [斷路](/azure/architecture/patterns/circuit-breaker)器的錯誤訊息。

- 主動監視並使用持續整合/持續傳遞 (CICD) ，輪替 API 存取認證，例如 [技術設定檔引擎](../../active-directory-b2c/restful-technical-profile.md)所使用的密碼和憑證。

## <a name="api-management---best-practices"></a>API 管理-最佳作法

當您部署 REST Api 並設定 RESTful 技術設定檔時，遵循建議的最佳作法可協助您避免常見的錯誤和忽略的問題。

### <a name="how-to-manage-apis"></a>如何管理 Api

- API 管理 (APIM) 發佈、管理及分析您的 Api。 APIM 也會處理驗證，以提供後端服務和微服務的安全存取。 使用 API 閘道來向外擴充 API 部署、快取和負載平衡。

- 建議您在使用者旅程圖的開頭取得正確的權杖，而不是針對每個 API 呼叫多次，並 [保護 Azure APIM API](../../active-directory-b2c/secure-api-management.md?tabs=app-reg-ga)。

## <a name="next-steps"></a>後續步驟

- [Azure AD B2C 開發人員的復原能力資源](resilience-b2c.md)
  - [復原的終端使用者體驗](resilient-end-user-experience.md)
  - [透過開發人員最佳作法的復原能力](resilience-b2c-developer-best-practices.md)
  - [透過監視及分析的復原能力](resilience-with-monitoring-alerting.md)
- [在您的驗證基礎結構中建立恢復功能](resilience-in-infrastructure.md)
- [提高應用程式中驗證和授權的復原能力](resilience-app-development-overview.md)