---
title: Azure Active Directory B2C 的計費模型
description: 瞭解 Azure AD B2c 每月活躍使用者 (MAU) 計費模型、如何將 Azure AD B2C 租使用者連結至 Azure 訂用帳戶，以及如何選取適當的進階層定價。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: edff5354d0565bd32cd0332b4aa0f215c2980d73
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949780"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的計費模型

Azure Active Directory B2C (Azure AD B2C) 定價是根據每月作用中使用者 (MAU) ，也就是在日曆月份中具有驗證活動的唯一使用者計數。 此計費模型適用于 Azure AD B2C 租使用者，以及 [Azure AD 來賓使用者共同作業 (B2B) ](../active-directory/external-identities/external-identities-pricing.md)。 MAU 計費可提供免費層和彈性且可預測的定價，協助您降低成本。 在本文中，您將瞭解 MAU 計費、將 Azure AD B2C 租使用者連結至訂用帳戶，以及變更您的定價層。

> [!IMPORTANT]
> 本文不包含定價詳細資料。 如需使用方式計費和定價的最新資訊，請參閱 [Azure Active Directory B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。

## <a name="what-do-i-need-to-do"></a>我需要做些什麼？

若要利用 MAU 計費，您的 Azure AD B2C 租使用者必須連結至 Azure 訂用帳戶。 如果您想要使用 Azure AD B2C Premium P2 功能（例如以風險為基礎的條件式存取），您可能也需要將 Azure AD B2C 租使用者切換至另一個定價層。

|如果您的租使用者為：  |您必須：  |
|---------|---------|
| Azure AD B2C 的租使用者已依據每個 MAU 計費     | 不執行任何動作。 當使用者向您的 Azure AD B2C 租使用者進行驗證時，您會使用以 MAU 為基礎的計費模型自動向您收費。        |
| 尚未連結至訂用帳戶的 Azure AD B2C 租使用者     |  將[您的 Azure AD B2C 租使用者連結至訂用](#link-an-azure-ad-b2c-tenant-to-a-subscription)帳戶，以啟用 MAU 計費。     |
| 在2019年11月1日前連結至訂用帳戶的 Azure AD B2C 租使用者    | [切換至 MAU 帳單 (建議的) ](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)，或停留在每個驗證的計費模型。     |
| Azure AD B2C 的租使用者，而且您想要使用 premium 功能 (例如以風險為基礎的條件式存取)     | 變更為支援您要使用之功能的[Azure AD 定價層](#change-your-azure-ad-pricing-tier)。        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>關於每月活躍的使用者 (MAU) 計費模型

MAU 帳單將于 **2019 年11月1日** Azure AD B2C 租使用者生效。 您在該日期或之後建立並連結至訂用帳戶的任何 Azure AD B2C 租使用者，都是以每個 MAU 為基礎來計費。 如果您有未連結至訂用帳戶的 Azure AD B2C 租使用者，您現在必須這樣做。 如果您有在2019年11月1日之前連結至訂用帳戶的現有 Azure AD B2C 租使用者，建議您升級為每月作用中使用者 (MAU) 計費模型，或隨時掌握每一驗證計費模型。
  
您的 Azure AD B2C 租使用者也必須根據您想要使用的功能，連結至適當的 Azure 定價層。 Premium 功能需要 Azure AD B2C [Premium P1 或 P2 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。 當您使用新功能時，可能需要升級定價層。 例如，條件式存取，您必須為您的租使用者選取 Azure AD B2C Premium P2 定價層。

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>將 Azure AD B2C 租使用者連結至訂用帳戶

Azure Active Directory B2C (Azure AD B2C) 的使用費用會向 Azure 訂用帳戶計費。 您必須在目標 Azure 訂用帳戶內建立 Azure AD B2C *資源* ，以明確地將 Azure AD B2C 租使用者連結至 Azure 訂用帳戶。 您可以在單一 Azure 訂用帳戶中建立數個 Azure AD B2C 資源，以及其他 Azure 資源，例如虛擬機器、儲存體帳戶和 Logic Apps。 您可以前往與訂用帳戶相關聯的 Azure Active Directory (Azure AD) 租使用者，查看訂用帳戶內的所有資源。

連結至 Azure AD B2C 租使用者的訂用帳戶可用來計費 Azure AD B2C 使用量或其他 Azure 資源，包括額外的 Azure AD B2C 資源。 它無法用來在 Azure AD B2C 租使用者中新增其他以 Azure 授權為基礎的服務或 Office 365 授權。

### <a name="prerequisites"></a>必要條件

* [Azure 訂用帳戶](https://azure.microsoft.com/free/)
* 您要連結至訂用帳戶的[Azure AD B2C 租](tutorial-create-tenant.md)使用者
  * 您必須是租使用者系統管理員
  * 租使用者還不得連結至訂用帳戶

### <a name="create-the-link"></a>建立連結

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頂端功能表中選取 [ **目錄 + 訂** 用帳戶] 篩選，然後選取包含您想要使用之 Azure 訂用帳戶的目錄， (*不* 是包含 Azure AD B2C 租使用者) 的目錄。
3. 選取 [ **建立資源**]， `Active Directory B2C` 在 [ **搜尋 Marketplace** ] 欄位中輸入，然後選取 [ **Azure Active Directory B2C**]。
4. 選取 [建立]。
5. 選取 [將現有的 Azure AD B2C 租用戶連結至我的 Azure 訂用帳戶]。
6. 從下拉式清單中選取 **Azure AD B2C 的租** 使用者。 只會顯示您是全域管理員且尚未連結至訂用帳戶的租使用者。 **Azure AD B2C 資源名稱**] 欄位會填入您所選取 Azure AD B2C 租使用者的功能變數名稱。
7. 選取您是系統管理員的有效 Azure **訂** 用帳戶。
8. 在 [ **資源群組**] 底下，選取 [ **建立新** 的]，然後指定 **資源群組的位置**。 此處的資源群組設定不會影響您的 Azure AD B2C 租使用者位置、效能或計費狀態。
9. 選取 [建立]。

    ![Azure 入口網站中的 Azure AD B2C 資源建立頁面](./media/billing/portal-01-create-b2c-resource-page.png)

針對 Azure AD B2C 租使用者完成這些步驟之後，您的 Azure 訂用帳戶會根據您的 Azure Direct 或 Enterprise 合約詳細資料（如果適用）計費。

## <a name="change-your-azure-ad-pricing-tier"></a>變更您的 Azure AD 定價層

租使用者必須根據您想要搭配 Azure AD B2C 租使用者使用的功能，連結至適當的 Azure 定價層。 Premium 功能需要 Azure AD B2C Premium P1 或 P2，如 [Azure Active Directory B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)所述。 在某些情況下，您將需要在使用新功能時升級定價層。 例如，如果您想要使用身分識別保護、以風險為基礎的條件式存取，以及任何未來的 Premium P2 功能搭配 Azure AD B2C，您必須為您的租使用者選取 Azure AD B2C Premium P2 定價層。

若要變更定價層，請遵循下列步驟。

1. 登入 Azure 入口網站。

2. 在頂端功能表中選取 [ **目錄 + 訂** 用帳戶] 篩選，然後選取包含您的 azure B2C 租使用者所連結之 azure 訂用帳戶的目錄， (*不要* 選取 Azure AD B2C 租使用者本身) 。

3. 在入口網站頂端的 [搜尋] 方塊中，輸入您 Azure AD B2C 租使用者的名稱。 然後，在 [ **資源**] 底下的搜尋結果中選取租使用者。

4. 在 [資源 **總覽** ] 頁面的 [ **定價層**] 底下，選取 [ **變更**]。

   ![變更定價層](media/billing/change-pricing-tier.png)
 
5. 選取包含您想要啟用之功能的定價層。

   ![選取定價層](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>切換至 MAU 帳單 (2019 年11月前 Azure AD B2C 租使用者) 

如果您在 **2019 年11月 1** 日前將 Azure AD B2C 租使用者連結至訂用帳戶，則會使用先前的每一驗證計費模型。 建議您升級為每月作用中的使用者 (MAU) 計費模型。 您 Azure AD B2C 資源中會設定計費選項。

切換至每月作用中的使用者 (MAU) 帳單無法 **復原**。 將 Azure AD B2C 資源轉換為 MAU 型計費模型之後，您就無法將該資源還原為每個驗證的計費模型。

以下說明如何針對現有的 Azure AD B2C 資源，將此參數設為 MAU 帳單：

1. 以具有 Azure AD B2C 資源之系統管理存取權的訂用帳戶擁有者身分登入 [Azure 入口網站](https://portal.azure.com) 。

2. 在頂端功能表中選取 [ **目錄 + 訂** 用帳戶] 篩選，然後選取要升級為 MAU 帳單的 Azure AD B2C 目錄。<br/>

    ![Azure 入口網站中的目錄和訂用帳戶篩選](./media/billing/portal-mau-01-select-b2c-directory.png)

3. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。

4. 在 Azure AD B2C 租使用者的 [ **總覽** ] 頁面中，選取 [ **資源名稱**] 底下的連結。 系統會將您導向 Azure AD 租使用者中的 Azure AD B2C 資源。<br/>

    ![Azure AD B2C 在 Azure 入口網站中醒目提示的資源連結](./media/billing/portal-mau-02-b2c-resource-link.png)

5. 在 Azure AD B2C 資源的 [ **總覽** ] 頁面的 [可 **計費單位**] 下，選取 [ **每次驗證 (變更為 MAU)** 連結。<br/>

    ![變更為 Azure 入口網站中醒目提示的 MAU 連結](./media/billing/portal-mau-03-change-to-mau-link.png)

6. 選取 [ **確認** ] 以完成升級至 MAU 計費。<br/>

    ![Azure 入口網站中以 MAU 為基礎的帳單確認對話方塊](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>當您從每個驗證帳單轉換至 MAU 計費時的預期結果

當您（訂用帳戶/資源擁有者）確認變更時，就會立即啟用以 MAU 為基礎的計量。 您的每月帳單將會反映在變更前計費的驗證單位，以及從變更開始的新 MAU 單位。

在轉換月份期間，不會對使用者進行雙重計算。 在變更前進行驗證的唯一作用中使用者，會以日曆月份的每一驗證費率計費。 這些相同的使用者不會包含在其他訂用帳戶計費週期的 MAU 計算中。 例如：

* Contoso B2C 租使用者有1000使用者。 250使用者在任何指定的月份都處於使用中狀態。 訂用帳戶管理員會從每個驗證變更為每月作用中的使用者 (MAU) 在每月10日。
* 第 1-10 的計費會使用每一驗證模型來計費。
  * 如果100使用者在這段期間內登入 (第-10) ，則會將這些使用者標記為 *月份的付費*。
* 從第10個 (計費的有效時間) 會以 MAU 費率計費。
  * 如果在這段期間內有額外的150使用者登入 (10-30) ，則只會收取額外的150。
  * 第一個100使用者的繼續活動不會影響日曆月份其餘部分的計費。

在轉換的計費期間，訂用帳戶擁有者可能會看到兩種方法的專案 (每次驗證，而每個 MAU) 會出現在其 Azure 訂用帳戶帳單聲明中：

* 使用的專案會在變更的日期/時間之後反映每次驗證。
* 反映每月作用中使用者的變更之後的使用專案 (MAU) 。

如需 Azure AD B2C 使用計費和定價的最新資訊，請參閱 [Azure Active Directory B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>管理 Azure AD B2C 租用戶資源

在 Azure 訂用帳戶中建立 Azure AD B2C 資源之後，您應該會看到「B2C 租使用者」類型的新資源與其他 Azure 資源一起出現。

您可以使用此資源來：

* 流覽至訂用帳戶以查看帳單資訊
* 取得 Azure AD B2C 租使用者的租使用者識別碼（GUID 格式）
* 移至您的 Azure AD B2C 租用戶
* 提交支援要求
* 將您的 Azure AD B2C 租使用者資源移至另一個 Azure 訂用帳戶或資源群組

### <a name="regional-restrictions"></a>區域限制

如果您已在訂用帳戶中建立 Azure 資源建立的區域限制，該限制可能會讓您無法建立 Azure AD B2C 資源。

若要緩解此問題，請放寬您的區域限制。

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure 雲端解決方案提供者 (CSP) 訂用帳戶

Azure AD B2C 支援 Azure 雲端解決方案提供者 (CSP) 訂用帳戶。 針對 Azure AD B2C 及所有 Azure 資源使用 API 或 Azure 入口網站時，都會提供此功能。 CSP 訂用帳戶系統管理員可以連結、移動及刪除與其他 Azure 資源相同的 Azure AD B2C 的關聯性。

使用角色型存取控制進行的 Azure AD B2C 管理並不受 Azure AD B2C 租用戶與 Azure CSP 訂用帳戶之間關聯性的影響。 角色型存取控制是使用以租使用者為基礎的角色，而非以訂用帳戶為基礎的角色來達成。

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>變更 Azure AD B2C 租使用者計費訂用帳戶

### <a name="move-using-azure-resource-manager"></a>使用 Azure Resource Manager 移動

如果來源和目的地訂用帳戶存在於相同的 Azure Active Directory 租使用者中，則可以使用 Azure Resource Manager 將 Azure AD B2C 租使用者移至另一個訂用帳戶。

若要瞭解如何將 Azure 資源（例如 Azure AD B2C 租使用者）移至另一個訂用帳戶，請參閱 [將資源移至新的資源群組或訂](../azure-resource-manager/management/move-resource-group-and-subscription.md)用帳戶

開始移動之前，請務必閱讀整篇文章，以充分瞭解這類移動的限制和需求。 除了移動資源的指示之外，還包含重要的資訊，例如預先移動的檢查清單，以及驗證移動作業的方式。

### <a name="move-by-unlinking-and-relinking"></a>透過取消連結和重新連結來移動

如果來源和目的地訂用帳戶與不同的 Azure Active Directory 租使用者相關聯，您就無法執行透過 Azure Resource Manager 的移動，如上面所述。 不過，您仍然可以從來源訂用帳戶取消連結 Azure AD B2C 租使用者，然後將它重新連結至目的地訂用帳戶，以達到相同的結果。 此方法是安全的，因為您唯一刪除的物件是 *帳單連結*，而不是 Azure AD B2C 租使用者本身。 使用者、應用程式、使用者流程等都不會受到影響。

1. 在 Azure AD B2C 目錄本身中，從目的地 Azure 訂用帳戶連結到的目的地 Azure AD 租使用者中， [邀請來賓使用者](user-overview.md#guest-user) () ，並確定此使用者具有 Azure AD B2C 中的 **全域管理員** 角色。
1. 流覽至代表來源 Azure 訂用帳戶中 Azure AD B2C 的 *Azure 資源* ，如上面的 [管理您的 Azure AD B2C 租使用者資源](#manage-your-azure-ad-b2c-tenant-resources) 一節中所述。 請勿切換至實際的 Azure AD B2C 租使用者。
1. 選取 [**總覽**] 頁面上的 [**刪除**] 按鈕。 這不 *會刪除相關* 的 Azure AD B2C 租使用者的使用者或應用程式。 它只會從來源訂用帳戶移除帳單連結。
1. 使用在步驟 1 Azure AD B2C 中新增為系統管理員的使用者帳戶登入 Azure 入口網站。 然後流覽至目的地 Azure 訂用帳戶，該訂用帳戶會連結到目的地 Azure Active Directory 租使用者。 
1. 遵循上述的「 [建立連結](#create-the-link) 」程式，在目的地訂用帳戶中重新建立帳單連結。
1. 您的 Azure AD B2C 資源現在已移至目的地 Azure 訂用帳戶 (連結到目標 Azure Active Directory) ，將從現在開始透過此訂用帳戶計費。

## <a name="next-steps"></a>後續步驟

如需最新的定價資訊，請參閱 [Azure Active Directory B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。