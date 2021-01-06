---
title: 如何在 Azure Marketplace 上建立受控服務供應專案的方案
description: 瞭解如何使用 Microsoft 合作夥伴中心在 Azure Marketplace 上建立受控服務供應專案的方案。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 624a3a194b451dc1f498a0ded9d68f641b304eab
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918168"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>如何建立受控服務供應專案的方案

透過 Microsoft 商業 marketplace 銷售的受控服務供應專案必須至少有一個方案。 您可以在相同的供應專案內使用不同的選項來建立各種不同的方案。 這些方案 (有時稱為 Sku) 在版本、營收或服務層級方面可能會有所不同。 如需方案的詳細指引，請參閱 [商業 marketplace 供應專案的方案和定價](./plans-pricing.md)。

## <a name="create-a-plan"></a>建立方案

1. 在合作夥伴中心中您供應專案的 [ **計畫總覽** ] 索引標籤上，選取 [ **+ 建立新方案**]。
2. 在出現的對話方塊中，于 [ **計畫識別碼**] 下，輸入唯一的方案識別碼。 最多可使用50個小寫英數位元、連字號或底線。 選取 [ **建立**] 之後，就無法修改方案識別碼。 您的客戶會看到此識別碼。
3. 在 [ **方案名稱** ] 方塊中，輸入此方案的唯一名稱。 最多使用50個字元。 您的客戶會看到此名稱。
4. 選取 [建立]  。

## <a name="define-the-plan-listing"></a>定義計畫清單

在 [ **計畫清單** ] 索引標籤上，定義方案名稱和描述，以便讓它們出現在商業 marketplace 中。

1. [ **方案名稱** ] 方塊會顯示您稍早為此方案提供的名稱。 您可以隨時變更。 此名稱會在商業 marketplace 中顯示為供應專案方案的標題。
2. 在 [ **方案摘要** ] 方塊中，提供方案的簡短描述，可用於 marketplace 搜尋結果中。
3. 在 [ **計畫描述** ] 方塊中，說明這項計畫的獨特之處，以及供應專案內的其他方案有何不同。
4. 選取 [ **儲存草稿** ]，然後繼續進行下一個索引標籤。

## <a name="define-pricing-and-availability"></a>定義定價和可用性

受管理的服務供應專案唯一可用的定價模式是 **(BYOL) 提供您自己的授權**。 這表示您會直接向客戶收取與此供應專案相關的費用，Microsoft 不會對您收取任何費用。

您可以將每個方案設定為每個人都可以看見 (公用) 或僅 (私用) 的特定物件。

> [!NOTE]
> 透過雲端解決方案提供者 (CSP) 計畫的轉銷商所建立的訂用帳戶不支援私人方案。

> [!IMPORTANT]
> 當您將某個方案發佈為公用之後，便無法再將其變更為私人。 若要控制哪些客戶可以接受您的供應項目和委派資源，請使用私人方案。 有了公用方案，您無法將可用性限制為僅供某個客戶甚至是特定數目的客戶使用，不過您可以選擇完全停止銷售該方案。 客戶接受供應項目後，您可以移除委託的存取權 (僅在您發布該供應項目時包含 [角色定義] 設定為受控服務註冊分配刪除角色的 [授權])。 您也可以與客戶聯繫，並要求他們移除您的存取權。

## <a name="make-your-plan-public"></a>將您的方案設為公用

1. 在 [ **計畫可見度**] 下，選取 [ **公用**]。
2. 選取 [儲存草稿]。 若要返回 [計畫總覽] 索引標籤，請選取左上方的 **[計畫總覽** ]。
3. 若要為此供應專案建立另一個方案，請在 [**方案總覽**] 索引標籤中選取 [ **+ 建立新方案**]

## <a name="make-your-plan-private"></a>將方案設為私用

您可以使用 Azure 訂用帳戶識別碼來授與私人方案的存取權。 您可以使用手動新增最多10個訂用帳戶識別碼或最多10000個訂用帳戶識別碼。CSV 檔案。

若要手動新增最多10個訂用帳戶識別碼：

1. 在 [ **計畫可見度**] 下，選取 [ **私** 用]。
2. 輸入您想要授與存取權之物件的 Azure 訂用帳戶識別碼。
3. （選擇性）在 [ **描述** ] 方塊中輸入此物件的描述。
4. 若要新增另一個識別碼，請選取 [ **新增識別碼 (最大 10)**。
5. 當您完成新增識別碼時，請選取 [ **儲存草稿**]。

使用新增最多10000個訂用帳戶識別碼。CSV 檔案：

1. 在 [ **計畫可見度**] 下，選取 [ **私** 用]。
2. 選取 [ **匯出物件 (csv])** 連結。 這會下載。CSV 檔案。
3. 開啟。CSV 檔案。 在 [ **識別碼** ] 欄中，輸入您想要授與存取權的 Azure 訂用帳戶識別碼。
4. 在 [ **描述**]   欄中，您可以選擇新增每個專案的描述。
5. 在 [ **類型**] 資料   行中，將 [ **SubscriptionId**] 加入   至每個具有識別碼的資料列。
6. 將檔案另存為。CSV 檔案。
7. 在合作夥伴中心中，選取 [匯 **入物件] (csv)** 連結。
8. 在 [ **確認**   ] 對話方塊中，選取 [ **是]**，然後上傳。CSV 檔案。
9. 選取 [ **儲存草稿**]。

## <a name="technical-configuration"></a>技術設定

本節會建立資訊清單，其中包含用來管理客戶資源的授權資訊。 需要此資訊才能啟用 [Azure 委派的資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)。

[請參閱 Azure Lighthouse 案例中的租使用者、角色和使用者](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)，以瞭解支援哪些角色和定義您的授權的最佳做法。

> [!NOTE]
> 授權專案中的使用者和角色會套用至每個啟用方案的客戶。 如果您想要限制對特定客戶的存取，則必須發佈私人方案以供其專屬使用。

### <a name="manifest"></a>資訊清單

1. 在 [ **資訊清單**] 下，提供資訊清單的 **版本** 。 使用的格式為 n.n.n (例如，1.2.5)。
2. 輸入您的 **租使用者識別碼**。 此 GUID 與您組織 (亦即您將會在其中存取客戶資源的管理租用戶) 的 Azure Active Directory (Azure AD) 租用戶識別碼相關聯。 如果您目前沒有此資訊，您可將滑鼠指標暫留在 Azure 入口網站右上角的帳戶名稱上，或選取 [切換目錄] 就能看到它。

如果您發佈新版本的供應項目，而且需要建立已更新的資訊清單，請選取 [+ 新增資訊清單]。 請務必增加前一個資訊清單版本的版本號碼。

### <a name="authorizations"></a>授權

授權會定義管理租用戶中哪些實體能存取購買方案客戶才能存取的資源與訂用帳戶。 這些實體都會被指派一個內建角色，以授與特定層級的存取權。

您最多可以為每個方案建立20個授權。

> [!TIP]
> 在多數情況下，建議您指派角色給 Azure AD 使用者群組或服務主體，而不是指派給一系列個別使用者帳戶。 如此一來，當您的存取需求變更時，就可以新增或移除個別使用者的存取權，而不需要更新並重新發佈方案。 將角色指派給 Azure AD 群組時， [群組類型應該是安全性，而不是 Office 365](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 如需其他建議，請參閱 [Azure Lighthouse 案例中的租用戶、角色和使用者](../lighthouse/concepts/tenants-users-roles.md)。

針對每個授權，您將需要提供下列資訊。 接著，您要新增多少使用者和角色定義，就視需要選取多少次 [+ 新增授權]。

* **AAD 物件** 識別碼：將授與特定許可權的使用者、使用者群組或應用程式 Azure AD 識別碼 (如角色定義) 至客戶資源的定義。
* **AAD 物件顯示名稱**：可協助客戶瞭解此授權用途的易記名稱。 客戶會在委派資源時看到此名稱。
* **角色定義**：從清單中選取其中一個可用的 Azure AD 內建角色。 此角色將會決定 [ **主體識別碼** ] 欄位中的使用者將會在客戶資源上擁有的許可權。 如需這些角色的說明，請參閱 Azure Lighthouse 的 [內建角色](../role-based-access-control/built-in-roles.md) 和 [角色支援](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse)。

> [!NOTE]
> 當您將適用的新內建角色新增至 Azure 時，就會在這裡提供。 可能會有一些延遲出現。

* 可 **指派的角色**：只有當您已在此授權的 **角色定義** 中選取 [使用者存取系統管理員] 時，才會顯示此選項。 若是如此，您必須在此新增一或多個可指派的角色。 **Azure AD 物件識別碼**] 欄位中的使用者將能夠將這些角色指派給受控識別，以便 [部署可補救的原則](../lighthouse/how-to/deploy-policy-remediation.md)。 通常與「使用者存取系統管理員」角色相關聯的其他權限都不會套用至此使用者。

> [!TIP]
> 若要確保您可以[移除委派的存取權](../lighthouse/how-to/remove-delegation.md) (如有需要)，請包含 [角色定義] 設定為[受控服務註冊指派刪除角色](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的 [授權]。 如果未指派此角色，則只有客戶租用戶中的使用者可以移除委派的資源。

完成方案的所有區段之後，您可以選取 [ **+ 建立新方案** ] 來建立其他方案。 當您完成時，請選取 [ **儲存草稿** ]，然後再繼續。

## <a name="next-steps"></a>後續步驟

* [檢閱並發佈](review-publish-offer.md)
