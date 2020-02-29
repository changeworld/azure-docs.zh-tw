---
title: Azure Active Directory B2C 的計費模型
description: 瞭解 Azure AD B2c 每月作用中使用者（MAU）計費模型，以及如何啟用特定 Azure 訂用帳戶的帳單。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30ecb1e6faa29482a8d69dd1d08e4f127f515596
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190002"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的計費模型

Azure Active Directory B2C （Azure AD B2C）使用量會以連結的 Azure 訂用帳戶計費，並使用每月作用中使用者（MAU）計費模型。 瞭解如何將 Azure AD B2C 資源連結至訂用帳戶，以及如何在下列各節中使用 MAU 計費模型。

> [!IMPORTANT]
> 本文不包含定價資訊。 如需有關使用計費和定價的最新資訊，請參閱[Azure Active Directory B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。

## <a name="monthly-active-users-mau-billing"></a>每月作用中使用者（MAU）計費

Azure AD B2C 計費會以日曆月份內具有驗證活動的唯一使用者計數計量，稱為每月作用中使用者（MAU）計費。

從**2019 年11月 1**日開始，所有新建立的 Azure AD B2C 租使用者都會依每月作用中使用者（MAU）計費。 在2019年11月1日或之後[連結到訂用](#link-an-azure-ad-b2c-tenant-to-a-subscription)帳戶的現有租使用者，將依每月作用中使用者（MAU）計費。

如果您在2019年11月1日前連結到訂用帳戶的現有 Azure AD B2C 租使用者，您可以選擇執行下列其中一項動作：

* 升級為每月作用中使用者（MAU）計費模型，或
* 保持在每個驗證計費模型上

### <a name="upgrade-to-monthly-active-users-billing-model"></a>升級為每月作用中使用者計費模型

具有 Azure AD B2C 資源之系統管理存取權的 Azure 訂用帳戶擁有者可以切換到 MAU 計費模型。 計費選項會在您的 Azure AD B2C 資源中設定。

切換為每月作用中使用者（MAU）計費是無法**復原**的。 將 Azure AD B2C 資源轉換成以 MAU 為基礎的計費模型之後，就無法將該資源還原為每個驗證計費模型。

以下說明如何讓切換 MAU 現有 Azure AD B2C 資源的計費：

1. 以訂用帳戶擁有者身分登入[Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選取您要升級為 MAU 計費的 Azure AD B2C 目錄。<br/>
    Azure 入口網站](./media/billing/portal-mau-01-select-b2c-directory.png) 中的 ![目錄和訂用帳戶篩選
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 Azure AD B2C 租使用者的 [**總覽**] 頁面上，選取 [**資源名稱**] 底下的連結。 系統會將您導向至 Azure AD 租使用者中的 Azure AD B2C 資源。<br/>
    Azure 入口網站](./media/billing/portal-mau-02-b2c-resource-link.png) 中反白顯示 ![Azure AD B2C 資源連結
1. 在 Azure AD B2C 資源的 [**總覽**] 頁面上，選取 [可**計費單位**] 底下的 [**每個驗證（變更為 MAU）** ] 連結。<br/>
    ![變更為 Azure 入口網站中反白顯示的 MAU 連結](./media/billing/portal-mau-03-change-to-mau-link.png)
1. 選取 [**確認**] 以完成 MAU 計費的升級。<br/>
    Azure 入口網站](./media/billing/portal-mau-04-confirm-change-to-mau.png) 中以 MAU 為基礎的計費確認對話方塊 ![

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>當您從每個驗證計費轉換到 MAU 計費時的預期事項

當您訂用帳戶/資源擁有者確認變更時，就會立即啟用以 MAU 為基礎的計量。 您的每月帳單會反映在變更之前計費的驗證單位，以及從變更開始算起的新 MAU 單位。

轉換月份期間不會雙重計算使用者。 在變更前進行驗證的唯一作用中使用者，會依日曆月份的每一驗證費率收費。 這些相同的使用者不會包含在訂用帳戶計費週期其餘部分的 MAU 計算中。 例如，

* Contoso B2C 租使用者有1000個使用者。 250使用者在任何指定的月份都處於作用中狀態。 訂用帳戶管理員會在當月10日，從每個驗證變更為每月作用中使用者（MAU）。
* 第 1-10 的計費是使用每一驗證模型來計費。
  * 如果100使用者在這段期間登入（第 1-10 個），則會將該月份的使用者標記為 *[付費*]。
* 從10日（轉換的有效時間）計費是以 MAU 費率計費。
  * 如果在這段期間內有其他150使用者登入（10-30），則只會收取額外的150費用。
  * 前100位使用者的繼續活動並不會影響日曆月份其餘部分的帳單。

在轉換的計費期間，訂用帳戶擁有者可能會看到這兩個方法的專案（每個驗證和每個 MAU）都出現在其 Azure 訂用帳戶帳單聲明中：

* 使用方式的專案，直到反映每次驗證的變更日期/時間為止。
* 反映每月作用中使用者（MAU）的變更之後的使用專案。

如需 Azure AD B2C 使用計費和定價的最新資訊，請參閱[Azure Active Directory B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>將 Azure AD B2C 租使用者連結至訂用帳戶

Azure Active Directory B2C （Azure AD B2C）的使用費用會依 Azure 訂用帳戶計費。 建立 Azure AD B2C 租用戶後，租用戶系統管理員必須明確地將 Azure AD B2C 租用戶連結至 Azure 訂用帳戶。

訂用帳戶連結是藉由在目標 Azure 訂用帳戶內建立 Azure AD B2C*資源*來達成。 您可以在單一 Azure 訂用帳戶中建立數個 Azure AD B2C 資源，以及其他 Azure 資源（例如虛擬機器、儲存體帳戶和 Logic Apps）。 您可以前往與訂用帳戶相關聯的 Azure Active Directory （Azure AD）租使用者，查看訂用帳戶內的所有資源。

連結至 Azure AD B2C 租使用者的訂用帳戶可用於 Azure AD B2C 使用量或其他 Azure 資源的計費，包括額外的 Azure AD B2C 資源。 此訂用帳戶無法用來在 Azure AD B2C 租用戶內新增其他 Azure 授權型服務或 Office 365 授權。

### <a name="prerequisites"></a>必要條件

* [Azure 訂用帳戶](https://azure.microsoft.com/free/)
* 您想要連結至訂用帳戶的[Azure AD B2C 租](tutorial-create-tenant.md)使用者
  * 您必須是租使用者系統管理員
  * 租使用者必須尚未連結至訂用帳戶

### <a name="create-the-link"></a>建立連結

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選取包含您想要使用之 Azure 訂用帳戶的目錄（*不*是包含 Azure AD B2C 租使用者的目錄）。
1. 選取 [**建立資源**]，在 [**搜尋 Marketplace** ] 欄位中輸入 `Active Directory B2C`，然後選取 [ **Azure Active Directory B2C**]。
1. 選取 [建立]
1. 選取 [將**現有的 Azure AD B2C 租使用者連結至我的 Azure 訂**用帳戶]。
1. 從下拉式清單中選取 [ **Azure AD B2C 租**使用者]。 只會顯示您是全域管理員且尚未連結至訂用帳戶的租使用者。 [ **Azure AD B2C 資源名稱**] 欄位會填入您選取之 Azure AD B2C 租使用者的功能變數名稱。
1. 選取您是系統管理員的有效 Azure**訂**用帳戶。
1. 在 [**資源群組**] 下，選取 [**建立新**的]，然後指定**資源群組位置**。 此處的資源群組設定不會影響您的 Azure AD B2C 租使用者位置、效能或計費狀態。
1. 選取 [建立]。
    ![Azure 入口網站中的 [Azure AD B2C 資源建立] 頁面](./media/billing/portal-01-create-b2c-resource-page.png)

當您針對 Azure AD B2C 租使用者完成這些步驟之後，您的 Azure 訂用帳戶會根據您的 Azure Direct 或 Enterprise 合約詳細資料（如果適用）來計費。

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>管理 Azure AD B2C 租用戶資源

在 Azure 訂用帳戶中建立 Azure AD B2C 資源之後，您應該會看到「B2C 租使用者」類型的新資源與您的其他 Azure 資源一起出現。

您可以使用此資源來：

* 流覽至訂用帳戶以審查帳單資訊
* 以 GUID 格式取得 Azure AD B2C 租使用者的租使用者識別碼
* 移至您的 Azure AD B2C 租用戶
* 提交支援要求
* 將您的 Azure AD B2C 租使用者資源移至另一個 Azure 訂用帳戶或資源群組

![Azure 入口網站中的 B2C 資源設定頁面](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>區域限制

如果您已在訂用帳戶中建立 Azure 資源建立的地區限制，這項限制可能會讓您無法建立 Azure AD B2C 資源。

若要降低此問題，請放寬您的區域限制。

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure 雲端解決方案提供者（CSP）訂用帳戶

Azure AD B2C 支援 Azure 雲端解決方案提供者 (CSP) 訂用帳戶。 針對 Azure AD B2C 及所有 Azure 資源使用 API 或 Azure 入口網站時，都會提供此功能。 CSP 訂用帳戶系統管理員可以使用與其他 Azure 資源相同的 Azure AD B2C 來連結、移動和刪除關聯性。

使用角色型存取控制進行的 Azure AD B2C 管理並不受 Azure AD B2C 租用戶與 Azure CSP 訂用帳戶之間關聯性的影響。 角色型存取控制是使用以租使用者為基礎的角色來達成，而不是以訂用帳戶為基礎的角色。

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>變更 Azure AD B2C 租使用者計費訂用帳戶

如果來源和目的地訂用帳戶存在於相同的 Azure Active Directory 租使用者中，則可以將 Azure AD B2C 的租使用者移至另一個訂用帳戶。

若要瞭解如何將 Azure 資源（例如 Azure AD B2C 租使用者）移至其他訂用帳戶，請參閱[將資源移至新的資源群組或訂](../azure-resource-manager/management/move-resource-group-and-subscription.md)用帳戶。

開始移動之前，請務必閱讀整篇文章，以充分瞭解這類移動的限制和需求。 除了移動資源的指示之外，它還包含重要的資訊，像是前置移動檢查清單，以及如何驗證移動作業。

## <a name="next-steps"></a>後續步驟

如需最新的定價資訊，請參閱[Azure Active Directory B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。
