---
title: 應用程式管理：最佳實踐和建議 |微軟文檔
description: 瞭解在 Azure 活動目錄中管理應用程式的最佳做法和建議。 瞭解如何使用自動預配和發佈具有應用程式代理的本地應用。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6534efb6fcd07ee3b9f3979cabf2feb77496a8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085287"
---
# <a name="application-management-best-practices"></a>應用程式管理最佳實踐
本文包含使用自動預配以及使用應用程式代理髮布本地應用在 Azure 活動目錄 （Azure AD） 中管理應用程式的建議和最佳實踐。

## <a name="cloud-app-and-single-sign-on-recommendations"></a>雲應用和單一登入建議
| 建議 | 註解 |
| --- | --- |
| 檢查 Azure AD 應用程式庫中的應用  | Azure AD 具有包含數千個通過企業單一登入 （SSO） 啟用的預集成應用程式。 有關特定于應用的設置指南，請參閱[SaaS 應用教程清單](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)。  | 
| 使用基於 SAML 的聯合 SSO  | 當應用程式支援它時，使用基於 Azure AD 的聯合基於 SAML 的 SSO，而不是基於密碼的 SSO 和 ADFS。  | 
| 使用 SHA-256 進行證書簽名  | 預設情況下，Azure AD 使用 SHA-256 演算法對 SAML 回應進行簽名。 使用 SHA-256，除非應用程式需要 SHA-1（請參閱[證書簽名選項](certificate-signing-options.md)和[應用程式登錄問題](application-sign-in-problem-application-error.md)。  | 
| 需要使用者分配  | 預設情況下，使用者可以訪問企業應用程式，而無需分配給它們。 但是，如果應用程式公開角色，或者希望應用程式顯示在使用者的訪問面板上，則需要使用者分配。 （請參閱[集成應用程式的開發人員指南](developer-guidance-for-integrating-applications.md)。  | 
| 將"我的應用"訪問面板部署到使用者 | `https://myapps.microsoft.com`訪問[面板](end-user-experiences.md)是一個基於 Web 的門戶，為使用者提供了為其分配的基於雲的應用程式提供單個進入點。 隨著添加組管理和自助服務密碼重設等其他功能，使用者可以在訪問面板中找到它們。 請參閱[規劃訪問面板部署](access-panel-deployment-plan.md)。
| 使用組分配  | 如果包含在訂閱中，請將組分配給應用程式，以便可以將正在進行的訪問管理委派給組擁有者。 （請參閱[集成應用程式的開發人員指南](developer-guidance-for-integrating-applications.md)。   | 
| 建立證書管理流程 | 簽章憑證的最大存留期為三年。 為了防止或儘量減少由於證書過期而導致的中斷，請使用角色和電子郵件通訊群組清單來確保密切監視與證書相關的更改通知。 |

## <a name="provisioning-recommendations"></a>預配建議
| 建議 | 註解 |
| --- | --- |
| 使用教程設置雲應用的預配 | 有關為要添加的庫應用配置的分步指南，請查看[SaaS 應用教程清單](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)。 |
| 使用預配日誌（預覽）監視狀態 | [預配日誌](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)提供有關預配服務執行的所有操作的詳細資訊，包括單個使用者的狀態。 |
| 將通訊組分配給預配通知電子郵件 | 要提高預配服務發送的重要警示的可見度，請將通訊組分配給通知電子郵件設定。 |


## <a name="application-proxy-recommendations"></a>應用程式代理建議
| 建議 | 註解 |
| --- | --- |
| 使用應用程式代理遠端存取內部資源 | 建議應用程式代理允許遠端使用者訪問內部資源，從而取代對 VPN 或反向代理的需求。 它不適合從公司網路內訪問資源，因為它可能會增加延遲。
| 使用自訂域 | 為應用程式設定自訂域（請參閱[配置自訂域](application-proxy-configure-custom-domain.md)），以便使用者和應用程式之間的 URL 將從網路內部或外部工作。 您還可以控制品牌並自訂 URL。  使用自訂功能變數名稱時，計畫從非 Microsoft 受信任的憑證授權單位獲取公共證書。 Azure 應用程式代理支援標準證書 （[萬用字元](application-proxy-wildcard.md)） 或基於 SAN 的證書。 （請參閱[應用程式代理規劃](application-proxy-deployment-plan.md)。） |
| 在部署應用程式代理之前同步使用者 | 在部署應用程式代理之前，請同步本地目錄中的使用者標識，或直接在 Azure AD 中創建它們。 身份同步允許 Azure AD 在授予使用者對應用代理髮布的應用程式的存取權限之前對使用者進行預身份驗證。 它還提供必要的使用者識別碼資訊來執行單一登入 （SSO）。 （請參閱[應用程式代理規劃](application-proxy-deployment-plan.md)。） |
| 遵循高可用性和負載平衡的提示 | 要瞭解流量如何在使用者、應用程式代理連接器和後端應用伺服器之間流動，並獲取優化性能和負載平衡的提示，請參閱[應用程式代理連接器和應用程式的高可用性和負載平衡](application-proxy-high-availability-load-balancing.md)。 |
| 使用多個連接器 | 使用兩個或多個應用程式代理連接器提高彈性、可用性和擴展率（請參閱[應用程式代理連接器](application-proxy-connectors.md)）。 創建連接器組並確保每個連接器組至少有兩個連接器（三個連接器是最佳的）。 |
| 查找靠近應用程式伺服器的連接器伺服器，並確保它們位於同一域中 | 要優化性能，請物理定位靠近應用程式伺服器的連接器伺服器（請參閱[網路拓撲注意事項](application-proxy-network-topology.md)）。 此外，連接器伺服器和 Web 應用程式伺服器應屬於同一活動目錄域，或者它們應跨越信任域。 具有集成 Windows 身份驗證 （IWA） 和 Kerberos 限制委派 （KCD） 的 SSO 需要此配置。 如果伺服器位於不同的域中，則需要使用基於資源的 SSO 委派（請參閱[KCD 對應用程式代理的單一登入](application-proxy-configure-single-sign-on-with-kcd.md)。 |
| 為連接器啟用自動更新 | 為連接器啟用自動更新，以進行最新功能和錯誤修復。 微軟提供最新的連接器版本和之前的一個版本的直接支援。 （請參閱[應用程式代理版本歷程記錄](application-proxy-release-version-history.md)。 |
| 繞過本地代理 | 為了便於維護，請將連接器配置為繞過本地代理，以便它直接連接到 Azure 服務。 （請參閱[應用程式代理連接器和代理伺服器](application-proxy-configure-connectors-with-proxy-servers.md)。 |
| 通過 Web 應用程式代理使用 Azure AD 應用程式代理 | 對於大多數本地方案，請使用 Azure AD 應用程式代理。 只有在需要 AD FS 代理伺服器且無法在 Azure 活動目錄中使用自訂域的情況下，才首選 Web 應用程式代理。 （請參閱[應用程式代理遷移](application-proxy-migration.md)。 |
