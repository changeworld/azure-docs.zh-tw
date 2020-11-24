---
title: 包含檔案
description: 包含檔案
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 09/10/2020
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 42dfa499e0a44d0b468d3a234e9dc88904c361b0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555092"
---
以下是 Azure Active Directory (Azure AD) 服務的使用條件約束和其他服務限制。

| 類別 | 限制 |
| --- | --- |
| 租用戶 | 單一使用者能以成員或來賓的身分，隸屬於最多 500 個 Azure AD 租用戶。<br/>單一使用者最多可以建立 200 個目錄。 |
| 網域 | 您可以新增 900 個以內的受控網域名稱。 如果您要設定所有網域與內部部署 Active Directory 建立同盟，則可以在每個租用戶中新增 450 個以內的網域名稱。 |
|資源 |<ul><li>根據預設，在免費版的 Azure Active Directory 中，最多可以在單一租用戶中建立 50000 個 Azure AD 資源。 如果您至少有一個已驗證的網域，則貴組織的預設 Azure AD 服務配額會擴充至 300000 個 Azure AD 資源。 即使在執行內部系統管理員接管並將組織轉換為至少有一個已驗證網域的受控租用戶之後，自助式註冊為組織建立的 Azure AD 服務配額仍會維持在 50,000 個 Azure AD 資源。 此服務限制與 Azure AD 定價頁面上的定價層限制 500000 個資源無關。 若要超越預設配額，您必須連絡 Microsoft 支援服務。</li><li>非系統管理員的使用者最多可以建立 250 個 Azure AD 資源。 此配額可還原的計數包括作用中的資源和已刪除的資源。 只可還原 30 天內刪除的已刪除 Azure AD 資源。 此配額無法還原的已刪除 Azure AD 資源計數值為 30 天的四分之一。 如果您的開發人員可能會在其一般職責的過程中重複超出此配額，您可以[建立及指派自訂角色](../articles/active-directory/roles/quickstart-app-registration-limits.md)，讓該角色具有建立無限數量應用程式註冊的權限。</li></ul> |
| 架構延伸模組 |<ul><li>字串類型延伸模組最多可以包含 256 個字元。 </li><li>二進位類型延伸模組受限於 256 個位元組。</li><li>任何單一 Azure AD 資源均可寫入 100 個延伸模組值，包括所有類型和所有應用程式皆可。</li><li>只能使用「字串」類型或「二進位」類型單一值屬性來擴充 User、Group、TenantDetail、Device、Application 和 ServicePrincipal 實體。</li><li>結構描述延伸模組僅適用於圖形 API 版本 1.21 預覽。 應用程式必須取得寫入權限才能登錄延伸模組。</li></ul> |
| 應用程式 |最多 100 個使用者可以成為單一應用程式的擁有者。 |
|應用程式資訊清單 |最多可以在應用程式資訊清單中新增 1200 個項目。 |
| 群組 |<ul><li>非系統管理員使用者最多可以在 Azure AD 組織中建立 250 個群組。 任何可以管理組織中群組的 Azure AD 系統管理員，也可以建立不限數目的群組 (最高可達 Azure AD 物件限制)。 如果您指派角色以移除使用者的限制，請將其指派給權限較低的內建角色，例如使用者系統管理員或群組系統管理員。</li><li>Azure AD 組織最多可以有 5000 個動態群組。<li>最多 100 個使用者可以成為單一群組的擁有者。</li><li>任何數目的 Azure AD 資源都可以是單一群組的成員。</li><li>使用者可以是任意數目群組的成員。</li><li>根據預設，可以使用 Azure AD Connect 從您的內部部署 Active Directory 同步至 Azure Active Directory 群組中的成員數目限制為 5 萬個成員。 如果您需要同步處理超過此限制的群組成員資格，您必須將 [Azure AD Connect Sync V2 端點 API](../articles/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2.md) 上線。</li><li>在所有案例中都不支援 Azure AD 中的巢狀群組</li></ul><br/> 以下是目前支援的巢狀群組案例。<ul><li> 一個群組可以新增為另一個群組的成員，藉以達成群組的巢狀。</li><li> 群組成員資格宣告 (當應用程式設定為在權杖中接收群組成員資格宣告時，已登入使用者所屬的巢狀組會包含在內)</li><li>條件式存取 (當條件式存取原則具有群組範圍時)</li><li>限制對自助式密碼重設的存取</li><li>限制哪些使用者可以執行 Azure AD 加入和裝置註冊</li></ul><br/>下列案例不支援巢狀群組︰<ul><li> 應用程式角色指派 (支援將群組指派給應用程式，但是在直接指派群組內成為巢狀的群組沒有存取權)，可用於存取和佈建</li><li>以群組為基礎的授權 (將授權自動指派給群組的所有成員)</li><li>Microsoft 365 群組。</li></ul> |
| 應用程式 Proxy | <ul><li>每個應用程式 Proxy 應用程式每秒最多 500 筆交易</li><li>Azure AD 組織每秒最多 750 筆交易</li></ul><br/>交易會定義為唯一資源的單一 HTTP 要求和回應。 當節流時，用戶端會收到 429 回應 (要求太多)。 |
| 存取面板 |無論指派的授權有多少，每位使用者在存取面板中可以看到的應用程式數目沒有限制。  |
| 報表 | 在任何報告中，最多可以檢視或下載 1000 個資料列。 任何其他資料會遭到截斷。 |
| 管理單位 | Azure AD 資源可以是有不超過 30 個管理單位的成員。 |
| Azure AD 角色和權限 | <ul><li>可以在 Azure AD 組織中建立最多 30 個 [Azure AD 自訂角色](/azure/active-directory//users-groups-roles/roles-custom-overview?context=azure%252factive-directory%252fusers-groups-roles%252fcontext%252fugr-context)。</li><li>無法將群組新增為[群組擁有者](../articles/active-directory/fundamentals/users-default-permissions.md?context=azure%252factive-directory%252fusers-groups-roles%252fcontext%252fugr-context#object-ownership)。</li><li>使用者讀取其他使用者租用戶資訊的能力，只能由 Azure AD 全組織的交換器進行限制，以停用所有租用戶資訊的所有非系統管理員使用者存取權 (不建議)。 如需詳細資訊，請參閱[限制成員使用者的預設權限](../articles/active-directory/fundamentals/users-default-permissions.md?context=azure%252factive-directory%252fusers-groups-roles%252fcontext%252fugr-context#restrict-member-users-default-permissions)。</li><li>最多可能需要 15 分鐘的時間或者先登出/登入，系統管理員角色成員資格新增和撤銷才會生效。</li></ul> |