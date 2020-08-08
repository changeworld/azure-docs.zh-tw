---
title: 包含檔案
description: 包含檔案
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/28/2020
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: ba0791108878dada5088428ce40d1af672d1892a
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010969"
---
以下是 Azure Active Directory (Azure AD) 服務的使用條件約束和其他服務限制。

| 類別 | 限制 |
| --- | --- |
| 目錄 | 單一使用者能以成員或來賓的身分，隸屬於最多 500 個 Azure AD 目錄。<br/>單一使用者最多可以建立 200 個目錄。 |
| 網域 | 您可以新增 900 個以內的受控網域名稱。 如果您要設定所有網域與內部部署 Active Directory 建立同盟，則可以在每個目錄中新增 450 個以內的網域名稱。 |
|資源 |<ul><li>根據預設，在免費版的 Azure Active Directory 中，最多可以在單一目錄中建立 50000 個 Azure AD 資源。 如果您至少有一個已驗證的網域，則貴組織的預設 Azure AD 服務配額會擴充至 300000 個 Azure AD 資源。 此服務限制與 Azure AD 定價頁面上的定價層限制 500000 個資源無關。 若要超越預設配額，您必須連絡 Microsoft 支援服務。</li><li>非系統管理員的使用者最多可以建立 250 個 Azure AD 資源。 此配額可還原的計數包括作用中的資源和已刪除的資源。 只可還原 30 天內刪除的已刪除 Azure AD 資源。 此配額無法還原的已刪除 Azure AD 資源計數值為 30 天的四分之一。 如果您的開發人員可能會在其一般職責的過程中重複超出此配額，您可以[建立及指派自訂角色](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md)，讓該角色具有建立無限數量應用程式註冊的權限。</li></ul> |
| 架構延伸模組 |<ul><li>字串類型延伸模組最多可以包含 256 個字元。 </li><li>二進位類型延伸模組受限於 256 個位元組。</li><li>任何單一 Azure AD 資源均可寫入 100 個延伸模組值，包括所有類型和所有應用程式皆可。</li><li>只能使用「字串」類型或「二進位」類型單一值屬性來擴充 User、Group、TenantDetail、Device、Application 和 ServicePrincipal 實體。</li><li>結構描述延伸模組僅適用於圖形 API 版本 1.21 預覽。 應用程式必須取得寫入權限才能登錄延伸模組。</li></ul> |
| 應用程式 |最多 100 個使用者可以成為單一應用程式的擁有者。 |
|應用程式資訊清單 |最多可以在應用程式資訊清單中新增 1200 個項目。 |
| 群組 |<ul><li>使用者最多可以在 Azure AD 組織中建立 250 個群組。</li><li>Azure AD 組織最多可以有 5000 個動態群組。<li>最多 100 個使用者可以成為單一群組的擁有者。</li><li>任何數目的 Azure AD 資源都可以是單一群組的成員。</li><li>使用者可以是任意數目群組的成員。</li><li>根據預設，可以使用 Azure AD Connect 從您的內部部署 Active Directory 同步至 Azure Active Directory 群組中的成員數目限制為 5 萬個成員。 如果您需要同步處理超過此限制的群組成員資格，您必須將 [Azure AD Connect Sync V2 端點 API](../articles/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2.md) 上線。</li><li>在所有案例中都不支援 Azure AD 中的巢狀群組</li></ul><br/> 以下是目前支援的巢狀群組案例。<ul><li> 一個群組可以新增為另一個群組的成員，藉以達成群組的巢狀。</li><li> 群組成員資格宣告 (當應用程式設定為在權杖中接收群組成員資格宣告時，已登入使用者所屬的巢狀組會包含在內)</li><li>條件式存取 (將條件式存取原則的範圍設定為群組時)</li><li>限制對自助式密碼重設的存取</li><li>限制哪些使用者可以執行 Azure AD 加入和裝置註冊</li></ul><br/>下列案例不支援巢狀群組︰<ul><li> 應用程式角色指派 (支援將群組指派給應用程式，但是在直接指派群組內成為巢狀的群組沒有存取權)，可用於存取和佈建</li><li>以群組為基礎的授權 (將授權自動指派給群組的所有成員)</li><li>Office 365 群組。</li></ul> |
| 應用程式 Proxy | <ul><li>每個應用程式 Proxy 應用程式每秒最多 500 筆交易</li><li>Azure AD 組織每秒最多 750 筆交易</li></ul><br/>交易會定義為唯一資源的單一 HTTP 要求和回應。 當節流時，用戶端會收到 429 回應 (要求太多)。 |
| 存取面板 |無論指派的授權有多少，每位使用者在存取面板中可以看到的應用程式數目沒有限制。  |
| 報表 | 在任何報告中，最多可以檢視或下載 1000 個資料列。 任何其他資料會遭到截斷。 |
| 管理單位 | Azure AD 資源可以是有不超過 30 個管理單位的成員。 |
| 系統管理員角色與權限 | <ul><li>群組無法新增為[擁有者](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)。</li><li>使用者讀取其他使用者目錄資訊的能力，無法在 Azure AD 全組織的交換器外部受到限制，以停用所有目錄資訊的所有非系統管理員使用者存取權 (不建議)。 如需預設權限的詳細資訊，請參閱[這裡](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)。</li><li>最多可能需要 15 分鐘的時間或者先登出/登入，系統管理員角色成員資格新增和撤銷才會生效。</li></ul> |
