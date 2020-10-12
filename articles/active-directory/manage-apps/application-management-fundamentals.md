---
title: 應用程式管理：最佳作法和建議 |Microsoft Docs
description: 瞭解在 Azure Active Directory 中管理應用程式的最佳作法和建議。 瞭解如何使用自動布建，以及如何使用應用程式 Proxy 發佈內部部署應用程式。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: d32728c1f388e9013b922d1f60d30e65d350bbc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88642430"
---
# <a name="application-management-best-practices"></a>應用程式管理的最佳做法

本文包含在 Azure Active Directory (Azure AD) 、使用自動布建，以及使用應用程式 Proxy 發佈內部部署應用程式之應用程式的建議和最佳作法。

## <a name="cloud-app-and-single-sign-on-recommendations"></a>雲端應用程式和單一登入建議
| 建議 | 註解 |
| --- | --- |
| 檢查應用程式的 Azure AD 應用程式資源庫  | Azure AD 有一個資源庫，其中包含數千個預先整合的應用程式，這些應用程式會使用企業單一登入 (SSO) 來啟用。 如需應用程式特定的安裝指引，請參閱 [SaaS 應用程式教學課程清單](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)。  | 
| 使用同盟 SAML 型 SSO  | 當應用程式支援時，請搭配 Azure AD 使用同盟的 SAML 型 SSO，而不是以密碼為基礎的 SSO 和 ADFS。  | 
| 使用 SHA-256 進行憑證簽署  | Azure AD 預設會使用 SHA-256 演算法來簽署 SAML 回應。 除非應用程式需要 SHA-1 (請參閱 [憑證簽章選項](certificate-signing-options.md) 和 [應用程式登入問題](application-sign-in-problem-application-error.md)，否則請使用 sha-256。 )   | 
| 需要使用者指派  | 依預設，使用者可以存取您的企業應用程式，而不需指派給他們。 但是，如果應用程式公開角色，或您想要讓應用程式出現在使用者的我的應用程式上，則需要使用者指派。  (請參閱 [整合應用程式的開發人員指引](developer-guidance-for-integrating-applications.md)。 )   | 
| 將我的應用程式部署至您的使用者 | [我的應用程式](end-user-experiences.md) `https://myapps.microsoft.com` 是以網頁為基礎的入口網站，可為使用者提供指派的雲端應用程式的單一進入點。 新增群組管理和自助式密碼重設等其他功能時，使用者可以在我的應用程式中找到它們。 請參閱 [規劃我的應用程式部署](access-panel-deployment-plan.md)。
| 使用群組指派  | 如果包含在您的訂用帳戶中，請將群組指派給應用程式，讓您可以將進行中的存取管理委派給群組擁有者。  (請參閱 [整合應用程式的開發人員指引](developer-guidance-for-integrating-applications.md)。 )    | 
| 建立管理憑證的流程 | 簽署憑證的最長存留期為三年。 若要避免或最小化由於憑證過期所造成的中斷情況，請使用角色和電子郵件通訊群組清單，以確保會嚴密監視與憑證相關的變更通知。 |

## <a name="provisioning-recommendations"></a>布建建議
| 建議 | 註解 |
| --- | --- |
| 使用教學課程設定雲端應用程式的布建 | 請參閱 [SaaS 應用程式教學課程清單](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) ，以取得針對您要新增的資源庫應用程式設定布建的逐步指引。 |
| 使用布建記錄 (預覽) 監視狀態 | 布建 [記錄](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 會提供布建服務所執行之所有動作的詳細資料，包括個別使用者的狀態。 |
| 將通訊群組指派給布建通知電子郵件 | 若要提高布建服務所傳送之重大警示的可見度，請將通訊群組指派給 [通知電子郵件] 設定。 |


## <a name="application-proxy-recommendations"></a>應用程式 Proxy 建議
| 建議 | 註解 |
| --- | --- |
| 使用應用程式 Proxy 來遠端存取內部資源 | 建議應用程式 Proxy 讓遠端使用者存取內部資源，以取代 VPN 或反向 Proxy 的需求。 它不是用來存取公司網路內的資源，因為它可能會增加延遲。
| 使用自訂網域 | 為您的應用程式設定自訂網域 (請參閱設定 [自訂網域](application-proxy-configure-custom-domain.md)) ，讓使用者和應用程式之間的 url 可以從網路內部或外部運作。 您也可以控制您的品牌和自訂 Url。  使用自訂功能變數名稱時，請規劃從非 Microsoft 受信任的憑證授權單位單位取得公開憑證。 Azure 應用程式 Proxy 支援標準、 ([萬用字元](application-proxy-wildcard.md)) 或以 SAN 為基礎的憑證。  (參閱 [應用程式 Proxy 規劃](application-proxy-deployment-plan.md)。 )  |
| 在部署應用程式 Proxy 之前同步處理使用者 | 在部署應用程式 proxy 之前，請從內部部署目錄同步處理使用者身分識別，或直接在 Azure AD 中建立它們。 身分識別同步處理可讓 Azure AD 在使用者授與應用程式 Proxy 發行應用程式的存取權之前，預先驗證使用者。 它也提供必要的使用者識別碼資訊，以 (SSO) 執行單一登入。  (參閱 [應用程式 Proxy 規劃](application-proxy-deployment-plan.md)。 )  |
| 遵循我們的高可用性和負載平衡秘訣 | 若要瞭解如何在使用者、應用程式 Proxy 連接器和後端應用程式伺服器之間流動流量，以及取得優化效能與負載平衡的秘訣，請參閱 [應用程式 Proxy 連接器和應用程式的高可用性和負載平衡](application-proxy-high-availability-load-balancing.md)。 |
| 使用多個連接器 | 使用兩個或多個應用程式 Proxy 連接器來提高復原能力、可用性和規模 (請參閱 [應用程式 proxy 連接器](application-proxy-connectors.md)) 。 建立連接器群組，並確定每個連接器群組都至少有兩個連接器 (三個連接器為最佳) 。 |
| 找出靠近應用程式伺服器的連接器伺服器，並確定它們位於相同的網域中 | 若要將效能優化，請實際找出靠近應用程式伺服器的連接器伺服器 (查看) 的 [網路拓撲考慮](application-proxy-network-topology.md) 。 此外，連接器伺服器和 web 應用程式伺服器應該屬於相同的 Active Directory 網域，或應該跨信任網域。 具有整合式 Windows 驗證 (IWA) 和 Kerberos 限制委派 (KCD) 的 SSO 需要此設定。 如果伺服器位於不同的網域，您將需要使用以資源為基礎的委派來進行 SSO (請參閱 [使用應用程式 Proxy 進行單一登入的 KCD](application-proxy-configure-single-sign-on-with-kcd.md)) 。 |
| 啟用連接器的自動更新 | 為您的連接器啟用自動更新，以取得最新功能和錯誤修正。 Microsoft 可直接支援最新的連接器版本和一個版本。  (請參閱 [應用程式 Proxy 發行版本歷程記錄](application-proxy-release-version-history.md)。 )  |
| 略過您的內部部署 proxy | 為了方便維護，請設定連接器以略過您的內部部署 proxy，讓它直接連線到 Azure 服務。  (參閱 [應用程式 Proxy 連接器和 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)。 )  |
| 透過 Web 應用程式 Proxy 使用 Azure AD 應用程式 Proxy | 在大部分的內部部署案例中，請使用 Azure AD 應用程式 Proxy。 只有在需要 AD FS Proxy 伺服器的情況下，以及在 Azure Active Directory 中無法使用自訂網域的情況下，才偏好使用 Web 應用程式 Proxy。  (參閱 [應用程式 Proxy 遷移](application-proxy-migration.md)。 )  |
