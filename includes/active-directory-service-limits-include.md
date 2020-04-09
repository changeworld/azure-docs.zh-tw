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
ms.openlocfilehash: d906a3dd072770a05b818fd3ca8de359b8427728
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986715"
---
以下是 Azure Active Directory (Azure AD) 服務的使用條件約束和其他服務限制。

| 類別 | 限制 |
| --- | --- |
| 目錄 | 單一使用者能以成員或來賓的身分，隸屬於最多 500 個 Azure AD 目錄。<br/>單個用戶可以創建最多 200 個目錄。 |
| 網域 | 您可以新增 900 個以內的受控網域名稱。 如果您要設定所有網域與內部部署 Active Directory 建立同盟，則可以在每個目錄中新增 450 個以內的網域名稱。 |
|資源 |<ul><li>默認情況下,Azure 活動目錄的免費版本的用戶可以在單個目錄中創建最多 50,000 個 Azure AD 資源。 如果至少有一個已驗證的域,則組織的預設 Azure AD 服務配額將擴展到 300,000 個 Azure AD 資源。 此服務限制與 Azure AD 定價頁上 500,000 個資源的定價層限制無關。 要超出預設配額,必須聯繫 Microsoft 支援部門。</li><li>非管理員用戶可以創建不超過 250 個 Azure AD 資源。 可用於還原此配額的活動資源和已刪除的資源都計數。 只有不到 30 天前刪除的已刪除的 Azure AD 資源才能還原。 已刪除的 Azure AD 資源,這些資源不再可用於以 30 天四分之一的值還原此配額。 如果開發人員在日常工作中可能重複超過此配額,則可以[創建和分配自定義角色](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md),並有權創建無限數量的應用註冊。</li></ul> |
| 架構延伸模組 |<ul><li>字串類型延伸模組最多可以包含 256 個字元。 </li><li>二進位類型延伸模組受限於 256 個位元組。</li><li>只能將 100 個擴充值(跨*所有**類型和所有應用程式*)寫入任何單個 Azure AD 資源。</li><li>只能使用「字串」類型或「二進位」類型單一值屬性來擴充 User、Group、TenantDetail、Device、Application 和 ServicePrincipal 實體。</li><li>結構描述延伸模組僅適用於圖形 API 版本 1.21 預覽。 應用程式必須取得寫入權限才能登錄延伸模組。</li></ul> |
| 應用程式 |最多 100 個使用者可以成為單一應用程式的擁有者。 |
|應用程式資訊清單 |應用程式清單中最多可以添加 1200 個條目。 |
| 群組 |<ul><li>用戶可以在 Azure AD 組織中創建最多 250 個組。</li><li>Azure AD 組織最多可以有 5000 個動態組。<li>最多 100 個使用者可以成為單一群組的擁有者。</li><li>任意數量的 Azure AD 資源都可以是單個組的成員。</li><li>使用者可以是任意數目群組的成員。</li><li>可以使用 Azure AD Connect 從您的內部部署 Active Directory 同步至 Azure Active Directory 群組中的成員數目限制為 5 萬個成員。</li><li>並非所有機制中都支援 Azure AD 內嵌套組</li></ul><br/> 此時,以下是支援具有嵌套組的方案。<ul><li> 可以將一個組添加為另一個組的成員,並且可以實現組嵌套。</li><li> 群組成員聲明(當應用程式設定為在權杖中接收組成員聲明時,包括登入使用者是其成員的嵌套組)</li><li>條件存取(將條件存取策略範圍界定到群組時)</li><li>限制對自助服務密碼重置的存取</li><li>限制哪些使用者可以執行 Azure AD 加入和裝置註冊</li></ul><br/>以下機制不支援嵌 sock:<ul><li> 應用角色分配(支援將組分配給應用,但嵌套在直接分配的組中的組將無法存取),用於存取和預配</li><li>以群組的授權(自動將授權指定群組的成員)</li><li>辦公室 365 組。</li></ul> |
| 應用程式 Proxy | <ul><li>每個應用程式為每秒最多 500 個事務</li><li>Azure AD 組織每秒最多 750 個事務</li></ul><br/>事務定義為唯一資源的單個 HTTP 請求和回應。 當限制時,用戶端將收到 429 回應(請求太多)。 |
| 存取面板 |<ul><li>每位使用者在存取面板中可以看到的應用程式數目沒有限制。 這適用於已指派 Azure AD Premium 或 Enterprise Mobility Suite 授權的使用者。</li><li>每位使用者可在存取面板中看到最多 10 個應用程式圖格。 此限制適用於為 Azure AD 免費許可證計畫分配許可證的使用者。 應用程式圖格範例包括 Box、Salesforce 或 Dropbox。 此限制不適用於系統管理員帳戶。</li></ul> |
| 報表 | 在任何報告中，最多可以檢視或下載 1000 個資料列。 任何其他資料會遭到截斷。 |
| 管理單位 | Azure AD 資源可以是不超過 30 個管理單位的成員。 |
| 系統管理員角色與權限 | <ul><li>無法新增群組[到擁有者](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)。</li><li>無法將群組配置給[角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。</li><li>使用者讀取其他使用者目錄資訊的能力不能限制在 Azure AD 組織範圍交換機之外,以禁用所有非管理員使用者對所有目錄資訊的訪問(不建議)。 有關預設權[限的詳細資訊,此處](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)為 。</li><li>在管理員角色添加和吊銷生效之前,可能需要長達 15 分鐘或註銷/登錄。</li></ul> |
