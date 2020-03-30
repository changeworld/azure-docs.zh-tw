---
title: Azure 活動目錄 B2C 的計費模型
description: 瞭解 Azure AD B2C 的每月活動使用者 （MAU） 計費模型以及如何為特定的 Azure 訂閱啟用計費。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190002"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Azure 活動目錄 B2C 的計費模型

Azure 活動目錄 B2C（Azure AD B2C） 使用方式向連結的 Azure 訂閱計費，並使用每月活動使用者 （MAU） 計費模型。 瞭解如何將 Azure AD B2C 資源連結到訂閱，以及 MAU 計費模型在以下部分中的工作方式。

> [!IMPORTANT]
> 本文不包含定價資訊。 有關使用方式計費和定價的最新資訊，請參閱[Azure 活動目錄 B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。

## <a name="monthly-active-users-mau-billing"></a>每月活動使用者 （MAU） 計費

Azure AD B2C 計費按在日曆月內具有身份驗證活動的唯一使用者計數（稱為每月活動使用者 （MAU） 計費）進行計量。

從**2019 年 11 月 1**日起，所有新創建的 Azure AD B2C 租戶每月按月向活動使用者 （MAU） 計費。 在 2019 年 11 月 1 日或之後[連結到訂閱](#link-an-azure-ad-b2c-tenant-to-a-subscription)的現有租戶將按月活動使用者 （MAU） 計費。

如果現有 Azure AD B2C 租戶在 2019 年 11 月 1 日之前連結到訂閱，則可以選擇執行以下操作之一：

* 升級到每月活動使用者 （MAU） 計費模型，或
* 保持每個身份驗證計費模型

### <a name="upgrade-to-monthly-active-users-billing-model"></a>升級到每月活動使用者計費模型

對 Azure AD B2C 資源具有管理存取權限的 Azure 訂閱擁有者可以切換到 MAU 計費模型。 計費選項在 Azure AD B2C 資源中配置。

切換到每月活動使用者 （MAU） 計費是**不可逆的**。 將 Azure AD B2C 資源轉換為基於 MAU 的計費模型後，無法將該資源還原到每個身份驗證計費模型。

下面瞭解如何為現有 Azure AD B2C 資源切換到 MAU 計費：

1. 以訂閱擁有者身份登錄到[Azure 門戶](https://portal.azure.com)。
1. 選擇頂部功能表中的**目錄 + 訂閱**篩選器，然後選擇要升級到 MAU 計費的 Azure AD B2C 目錄。<br/>
    ![Azure 門戶中的目錄和訂閱篩選器](./media/billing/portal-mau-01-select-b2c-directory.png)
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 Azure AD B2C 租戶的 **"概述"** 頁上，選擇**資源名稱**下的連結。 您被定向到 Azure AD 租戶中的 Azure AD B2C 資源。<br/>
    ![Azure AD B2C 資源連結在 Azure 門戶中突出顯示](./media/billing/portal-mau-02-b2c-resource-link.png)
1. 在 Azure AD B2C 資源的 **"概述"** 頁上，在**計費單位**下，選擇 **"每次身份驗證（更改為 MAU）"** 連結。<br/>
    ![更改為 Azure 門戶中突出顯示的 MAU 連結](./media/billing/portal-mau-03-change-to-mau-link.png)
1. 選擇 **"確認**"以完成對 MAU 計費的升級。<br/>
    ![Azure 門戶中基於 MAU 的計費確認對話方塊](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>從每個身份驗證計費過渡到 MAU 計費時，應期待什麼

訂閱/資源擁有者確認更改後，將立即啟用基於 MAU 的計量。 每月帳單將反映在更改之前計費的身份驗證單位，以及從更改開始的新 MAU 單位。

在過渡月份，使用者不會重複計算。 更改前進行身份驗證的唯一活動使用者在日曆月內按身份驗證費率收費。 這些相同的使用者不包括在訂閱計費週期的剩餘部分的 MAU 計算中。 例如：

* Contoso B2C 租戶有 1，000 個使用者。 在任意給定月份中，250 個使用者處於活動狀態。 訂閱管理員在每月 10 日從每個身份驗證更改為每月活動使用者 （MAU）。
* 1-10 的計費使用每個身份驗證模型計費。
  * 如果在此期間（1-10 日）有 100 個使用者登錄，則這些使用者將標記為*該月已付費*。
* 從第 10 個計費（過渡的有效時間）以 MAU 費率計費。
  * 如果在此期間（10-30 日）有另外 150 個使用者登錄，則僅向另外 150 個使用者收費。
  * 前 100 個使用者的繼續活動不會影響日曆月剩餘時間的計費。

在轉換的計費期間，訂閱擁有者可能會在其 Azure 訂閱計費語句中看到兩種方法（每個身份驗證和每個 MAU）的條目：

* 使用條目，直到反映每個身份驗證的更改日期/時間。
* 反映每月活動使用者 （MAU） 的更改後使用方式的條目。

有關 Azure AD B2C 的使用計費和定價的最新資訊，請參閱[Azure 活動目錄 B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>將 Azure AD B2C 租戶連結到訂閱

Azure 活動目錄 B2C（Azure AD B2C）的使用費由 Azure 訂閱計費。 建立 Azure AD B2C 租用戶後，租用戶系統管理員必須明確地將 Azure AD B2C 租用戶連結至 Azure 訂用帳戶。

訂閱連結是通過在目標 Azure 訂閱中創建 Azure AD B2C*資源*來實現的。 可以在單個 Azure 訂閱中創建多個 Azure AD B2C 資源，以及其他 Azure 資源（如虛擬機器、存儲帳戶和邏輯應用）。 通過訪問訂閱關聯的 Azure 活動目錄 （Azure AD） 租戶，可以查看訂閱中的所有資源。

連結到 Azure AD B2C 租戶的訂閱可用於計費 Azure AD B2C 使用方式或其他 Azure 資源，包括其他 Azure AD B2C 資源。 此訂用帳戶無法用來在 Azure AD B2C 租用戶內新增其他 Azure 授權型服務或 Office 365 授權。

### <a name="prerequisites"></a>Prerequisites

* [Azure 訂閱](https://azure.microsoft.com/free/)
* 要連結到訂閱的[Azure AD B2C 租戶](tutorial-create-tenant.md)
  * 您必須是租戶管理員
  * 租戶必須尚未連結到訂閱

### <a name="create-the-link"></a>創建連結

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在頂部功能表中選擇**目錄 + 訂閱**篩選器，然後選擇包含要使用的 Azure 訂閱的目錄（*不包括*包含 Azure AD B2C 租戶的目錄）。
1. 選擇 **"創建資源**"，`Active Directory B2C`在 **"搜索市場"** 欄位中輸入，然後選擇**Azure 活動目錄 B2C**。
1. 選擇 **"創建"**
1. 選擇**將現有的 Azure AD B2C 租戶連結到我的 Azure 訂閱**。
1. 從下拉清單中選擇**Azure AD B2C 租戶**。 僅顯示您是全域管理員且尚未連結到訂閱的租戶。 **Azure AD B2C 資源名稱**欄位用您選擇的 Azure AD B2C 租戶的功能變數名稱填充。
1. 選擇您作為管理員的活動 Azure**訂閱**。
1. 在 **"資源組**"下，選擇 **"創建新**"，然後指定**資源組位置**。 此處的資源組設置不會影響 Azure AD B2C 租戶位置、性能或計費狀態。
1. 選取 [建立]****。
    ![Azure 門戶中的 Azure AD B2C 資源創建頁](./media/billing/portal-01-create-b2c-resource-page.png)

完成 Azure AD B2C 租戶的這些步驟後，Azure 訂閱將根據 Azure 直接或企業協定詳細資訊（如果適用）計費。

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>管理 Azure AD B2C 租用戶資源

在 Azure 訂閱中創建 Azure AD B2C 資源後，應會看到與其他 Azure 資源一起顯示類型為"B2C 租戶"的新資源。

您可以使用此資源來：

* 導航到訂閱以查看計費資訊
* 以 GUID 格式獲取 Azure AD B2C 租戶的租戶 ID
* 移至您的 Azure AD B2C 租用戶
* 提交支援要求
* 將 Azure AD B2C 租戶資源移動到其他 Azure 訂閱或資源組

![Azure 門戶中的 B2C 資源設置頁](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>區域限制

如果在訂閱中為 Azure 資源創建建立了區域限制，則此限制可能會阻止您創建 Azure AD B2C 資源。

要緩解此問題，請放寬區域限制。

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure 雲解決方案提供程式 （CSP） 訂閱

Azure AD B2C 支援 Azure 雲端解決方案提供者 (CSP) 訂用帳戶。 針對 Azure AD B2C 及所有 Azure 資源使用 API 或 Azure 入口網站時，都會提供此功能。 CSP 訂閱管理員可以與其他 Azure 資源一樣，與 Azure AD B2C 連結、移動和刪除關係。

使用角色型存取控制進行的 Azure AD B2C 管理並不受 Azure AD B2C 租用戶與 Azure CSP 訂用帳戶之間關聯性的影響。 基於角色的存取控制是通過使用基於租戶的角色而不是基於訂閱的角色來實現的。

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>更改 Azure AD B2C 租戶計費訂閱

如果源和目標訂閱存在於同一 Azure 活動目錄租戶中，則可以將 Azure AD B2C 租戶移動到另一個訂閱。

要瞭解如何將 Azure 資源（如 Azure AD B2C 租戶）移動到其他訂閱，請參閱[將資源移動到新資源組或訂閱](../azure-resource-manager/management/move-resource-group-and-subscription.md)。

在開始移動之前，請務必閱讀整個文章，以充分理解此類移動的局限性和要求。 除了移動資源的說明外，它還包括關鍵資訊，如移動前檢查表以及如何驗證移動操作。

## <a name="next-steps"></a>後續步驟

有關最新的定價資訊，請參閱[Azure 活動目錄 B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。
