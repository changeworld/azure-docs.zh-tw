---
title: 包含檔案
description: 包含檔案
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 01/22/2020
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 2e06a6c8dd7eb58769f504db9f96e0303c3e9f4c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748959"
---
以下是 Azure Active Directory (Azure AD) 服務的使用條件約束和其他服務限制。

| 類別 | 限制 |
| --- | --- |
| 目錄 | 單一使用者能以成員或來賓的身分，隸屬於最多 500 個 Azure AD 目錄。<br/>單一使用者最多可以建立 20 個目錄。 |
| 網域 | 您可以新增 900 個以內的受控網域名稱。 如果您要設定所有網域與內部部署 Active Directory 建立同盟，則可以在每個目錄中新增 450 個以內的網域名稱。 |
|資源 |<ul><li>預設 Azure Active Directory 免費版的使用者，可以在單一目錄中建立最多 50000 Azure AD 資源。 如果您至少有一個已驗證的網域，Azure AD 中的預設目錄服務配額會延伸到 300000 Azure AD 資源。 </li><li>非系統管理員使用者可以建立的 Azure AD 資源不超過250個。 作用中資源和已刪除的資源，可供還原計入此配額。 只有刪除了少於30天前刪除的 Azure AD 資源可供還原。 已刪除已無法再供還原使用的 Azure AD 資源計數，其值為一季30天。 如果您的開發人員可能會在其定期責任的過程中重複超出此配額，您可以[建立並指派自訂角色](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md)，以建立無限數量的應用程式註冊許可權。</li></ul> |
| 架構延伸模組 |<ul><li>字串類型延伸模組最多可以包含 256 個字元。 </li><li>二進位類型延伸模組受限於 256 個位元組。</li><li>只有100延伸模組值（在*所有*類型和*所有*應用程式中）可以寫入任何單一 Azure AD 資源。</li><li>只能使用「字串」類型或「二進位」類型單一值屬性來擴充 User、Group、TenantDetail、Device、Application 和 ServicePrincipal 實體。</li><li>結構描述延伸模組僅適用於圖形 API 版本 1.21 預覽。 應用程式必須取得寫入權限才能登錄延伸模組。</li></ul> |
| 應用程式 |最多 100 個使用者可以成為單一應用程式的擁有者。 |
|應用程式資訊清單 |最多可以在應用程式資訊清單中新增1200個專案。 |
| 群組 |<ul><li>使用者最多可以在 Azure AD 組織中建立250個群組。</li><li>Azure AD 的組織最多可以有5000個動態群組。<li>最多 100 個使用者可以成為單一群組的擁有者。</li><li>任何數目的 Azure AD 資源都可以是單一群組的成員。</li><li>使用者可以是任意數目群組的成員。</li><li>可以使用 Azure AD Connect 從您的內部部署 Active Directory 同步至 Azure Active Directory 群組中的成員數目限制為 5 萬個成員。</li><li>在所有案例中都不支援 Azure AD 中的嵌套群組</li></ul><br/> 目前，以下是支援的嵌套群組案例。<ul><li> 一個群組可以新增為另一個群組的成員，而且您可以達成群組的嵌套。</li><li> 群組成員資格宣告（當應用程式設定為在權杖中接收群組成員資格宣告時，已登入使用者所屬的嵌套組會包含在內）</li><li>條件式存取（將條件式存取原則的範圍設定為群組）</li><li>限制對自助式密碼重設的存取</li><li>限制哪些使用者可以執行 Azure AD 加入和裝置註冊</li></ul><br/>下列案例不支援的嵌套群組：<ul><li> 應用程式角色指派（支援將群組指派給應用程式，但在直接指派的群組內的群組不會有存取權），可用於存取和布建</li><li>以群組為基礎的授權（將授權自動指派給群組的所有成員）</li><li>Office 365 群組。</li></ul> |
| 應用程式 Proxy | <ul><li>每個應用程式 Proxy 應用程式最多每秒500筆交易</li><li>Azure AD 組織的每秒最多750筆交易</li></ul><br/>交易會定義為單一 HTTP 要求和唯一資源的回應。 當節流時，用戶端會收到429回應（要求太多）。 |
| 存取面板 |<ul><li>每位使用者在存取面板中可以看到的應用程式數目沒有限制。 這適用於已指派 Azure AD Premium 或 Enterprise Mobility Suite 授權的使用者。</li><li>每位使用者可在存取面板中看到最多 10 個應用程式圖格。 此限制適用于獲指派 Azure AD Free 授權方案授權的使用者。 應用程式圖格範例包括 Box、Salesforce 或 Dropbox。 此限制不適用於系統管理員帳戶。</li></ul> |
| 報告 | 在任何報告中，最多可以檢視或下載 1000 個資料列。 任何其他資料會遭到截斷。 |
| 管理單位 | Azure AD 的資源可以是不超過30個管理單位的成員。 |
| 系統管理員角色與權限 | <ul><li>無法將群組新增為[擁有](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)者。</li><li>無法將群組指派給[角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。</li><li>使用者讀取其他使用者的目錄資訊的能力，無法在 Azure AD 全公司的交換器外部受到限制，以停用所有非系統管理員的使用者存取所有目錄資訊（不建議）。 如需預設許可權的詳細資訊，請參閱[這裡](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)。</li><li>最多可能需要15分鐘的時間，或登出/登入，系統管理員角色成員資格新增和撤銷才會生效。</li></ul> |
