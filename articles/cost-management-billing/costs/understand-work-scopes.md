---
title: 了解及使用 Azure 成本管理範圍
description: 本文可協助您了解 Azure 中可用的計費和資源管理範圍，以及如何使用成本管理和 API 中的範圍。
author: bandersmsft
ms.author: banders
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 31ec2e75f9bc1bd02d097af9076c9356598a9499
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167567"
---
# <a name="understand-and-work-with-scopes"></a>了解並使用範圍

本文可協助您了解 Azure 中可用的計費和資源管理範圍，以及如何使用成本管理和 API 中的範圍。

## <a name="scopes"></a>範圍

「範圍」是 Azure 資源階層中 Azure AD 使用者存取和管理服務的節點。 大部分的 Azure 資源都會建立並部署到資源群組中，其為訂用帳戶的一部分。 Microsoft 也在 Azure 訂用帳戶之上提供兩個階層，其具有專門用來管理計費資料的角色：
- 計費資料，例如付款和發票
- 雲端服務，例如成本和原則治理

在範圍中，您可管理計費資料、指定付款專屬角色、檢視發票，以及進行一般帳戶管理。 計費和帳戶角色會與用於資源管理的角色 (其使用 [Azure RBAC](../../role-based-access-control/overview.md)) 分開管理。 為了清楚區分不同範圍的意圖 (包括存取控制差異)，這些範圍分別稱為「計費範圍」和「RBAC 範圍」。

若要深入了解範圍，請觀看[成本管理設定階層](https://www.youtube.com/watch?v=n3TLRaYJ1NY)影片。 若要觀看其他影片，請造訪[成本管理 YouTube 頻道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

## <a name="how-cost-management-uses-scopes"></a>成本管理使用範圍的方式

成本管理適用於資源之上的所有範圍，可讓組織在其擁有存取權的層級管理成本 (無論是整個計費帳戶還是單一資源群組)。 雖然計費範圍會根據您的 Microsoft 合約 (訂用帳戶類型) 而有所不同，但 RBAC 範圍不會。

## <a name="azure-rbac-scopes"></a>Azure RBAC 範圍

Azure 支援三個範圍進行資源管理。 每個範圍都支援管理存取和治理，包括但不限於成本管理。

- [**管理群組**](../../governance/management-groups/overview.md) - 階層式容器 (最多八個層級) 可組織 Azure 訂用帳戶。

    資源類型：[Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **訂用帳戶** - Azure 資源的主要容器。

    資源類型：[Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**資源群組**](../../azure-resource-manager/management/overview.md#resource-groups) - 相關資源的邏輯分組，適用於共用相同生命週期的 Azure 解決方案。 例如，一起部署和刪除的資源。

    資源類型：[Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

管理群組可讓您將訂用帳戶組織成階層。 例如，您可以使用管理群組來建立邏輯組織階層。 然後，將生產和開發/測試工作負載的訂用帳戶提供給小組。 然後在訂用帳戶中建立資源群組，以管理每個子系統或元件。

建立組織階層可以有條理的方式進行成本和原則合規性彙總。 然後，每個領導者都可以檢視和分析其目前的成本。 接麼，其可建立預算來控管不良的消費模式，並使用最低層級的 Advisor 建議將成本最佳化。

使用 Azure RBAC 在治理範圍上執行存取權授與，以檢視成本並選擇性地管理成本配置 (例如預算和匯出)。 您可以使用 Azure RBAC 來授與 Azure AD 使用者和群組存取權，以執行一組預先定義的動作，這組動作是定義於特定範圍的角色中。 例如，指派給管理群組範圍的角色也會將相同權限授予巢狀訂用帳戶和資源群組。

成本管理支援下列每個範圍的下列內建角色：

- [**擁有者**](../../role-based-access-control/built-in-roles.md#owner) – 可以檢視成本及管理所有項目，包括成本配置。
- [**參與者**](../../role-based-access-control/built-in-roles.md#contributor) – 可以檢視成本及管理所有項目，包括成本配置，但不包括存取控制。
- [**讀者**](../../role-based-access-control/built-in-roles.md#reader) – 可以檢視所有項目，包括成本資料和設定，但無法進行任何變更。
- [**成本管理參與者**](../../role-based-access-control/built-in-roles.md#cost-management-contributor) – 可以檢視成本、管理成本配置及檢視建議。
- [**成本管理讀者**](../../role-based-access-control/built-in-roles.md#cost-management-reader) – 可以檢視成本資料、成本配置及檢視建議。

成本管理參與者是建議的最低權限角色。 此角色允許人員建立及管理預算和匯出，以更有效率地監視和報告成本。 成本管理參與者也可能需要額外的角色，以支援複雜的成本管理案例。 請考慮下列案例：

- **報告資源使用量** – Azure 成本管理會在 Azure 入口網站中顯示成本； 其中包含使用量，因為此資訊與完整使用模式的成本有關。 此報告也會顯示 API 和下載費用，但是建議您深入了解 Azure 監視器中的詳細使用量計量，以取得更深入的了解。 請考慮在您需要的任何範圍上授與[監視讀取器](../../role-based-access-control/built-in-roles.md#monitoring-reader)權限，同時也可報告詳細的使用計量。
- **在超出預算時採取行動** – 成本管理參與者也需要存取權來建立及管理動作群組，以便自動回應超額部分。 請考慮將[監視參與者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)授予資源群組，其中包含在超出預算限額時所要使用的動作群組。 需要所用特定服務 (例如自動化和 Azure Functions) 的其他角色，才能自動執行特定動作。
- **排程成本資料匯出** – 成本管理參與者也需要存取權來管理儲存體帳戶，以排程將資料複製到儲存體帳戶中的匯出。 請考慮將[儲存體帳戶參與者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)授予資源群組，其中包含成本資料匯出所在的儲存體帳戶。
- **檢視節省成本建議** – 成本管理讀者和成本管理參與者預設都有存取來「檢視」成本建議。 不過，需要個別資源的存取權，才具備對成本建議採取行動的存取權。 如果您想要對以成本為基礎的建議採取行動，請考慮授與[服務專屬角色](../../role-based-access-control/built-in-roles.md#all)。

只有當管理群組包含 Enterprise 合約 (EA)、隨用隨付 (PAYG) 或 Microsoft 內部訂用帳戶時，才會受到支援。 具有任何其他訂用帳戶類型 (例如 Microsoft 客戶合約或 Azure Active Directory 訂用帳戶) 的管理群組無法訂閱成本。 如果您有混合的訂用帳戶，請將不支援的訂用帳戶分別移至管理群組階層的各個部分，以啟用支援的訂用帳戶成本管理。 例如，在根管理群組下建立兩個管理群組：**Azure AD** 和**我的組織**。將您的 Azure AD 訂用帳戶移至 **Azure AD** 管理群組，然後使用**我的組織**管理群組來檢視和管理成本。

## <a name="enterprise-agreement-scopes"></a>Enterprise 合約範圍

Enterprise 合約 (EA) 計費帳戶 (也稱為註冊) 具有下列範圍：

- [**計費帳戶**](../manage/view-all-accounts.md) - 代表 EA 註冊。 發票會在此範圍產生。 不是以使用量為基礎的購買 (例如 Marketplace 和保留) 僅適用於此範圍。 這些購買不會呈現在部門或註冊帳戶中。 保留使用量和所有其他使用量會套用至個別資源。 使用量會匯總至計費帳戶內的訂用帳戶。 若要查看細分至每個資源的保留成本，請切換以檢視成本分析中的**分攤成本**。

    資源類型：`Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **部門** - 註冊帳戶的選擇性群組。

    資源類型：`Billing/billingAccounts/departments`

- **註冊帳戶** - 代表單一帳戶擁有者。 不支援將存取權授予多個人。

    資源類型：`Microsoft.Billing/billingAccounts/enrollmentAccounts`

雖然治理範圍會繫結至單一目錄，但 EA 計費範圍不會。 EA 計費帳戶可能有橫跨任意多個 Azure AD 目錄的訂用帳戶。

EA 計費範圍支援下列角色：

- **企業系統管理員** – 可以管理計費帳戶設定和存取、可以檢視所有成本，以及可管理成本配置。 例如，預算和匯出。 在函式中，EA 計費範圍與[成本管理參與者 Azure 角色](../../role-based-access-control/built-in-roles.md#cost-management-contributor)相同。
- **企業唯讀使用者** – 可以檢視計費帳戶設定、成本資料和成本配置。 例如，預算和匯出。 在函式中，EA 計費範圍與[成本管理讀者 Azure 角色](../../role-based-access-control/built-in-roles.md#cost-management-reader)相同。
- **部門系統管理員** – 可以管理部門設定 (例如成本中心)，而且可以存取、檢視所有成本，以及管理成本配置。 例如，預算和匯出。  必須為部門系統管理員和唯讀使用者啟用 [DA 檢視費用] 計費帳戶設定，才能查看成本。 如果已停用 [DA 檢視費用] 選項，部門使用者就無法查看任何層級的成本，即使他們是帳戶或訂用帳戶擁有者也一樣。
- **部門唯讀使用者** – 可以檢視部門設定、成本資料和成本配置。 例如，預算和匯出。 如果已停用 [DA 檢視費用] 選項，部門使用者就無法查看任何層級的成本，即使他們是帳戶或訂用帳戶擁有者也一樣。
- **帳戶擁有者** – 可以管理註冊帳戶設定 (例如成本中心)、檢視所有成本，以及管理註冊帳戶的成本配置 (例如預算和匯出)。 必須為帳戶擁有者和 RBAC 使用者啟用 [AO 檢視費用] 計費帳戶設定，才能查看成本。

EA 計費帳戶使用者沒有發票的直接存取權。 您可以從外部大量授權系統取得發票。

Azure 訂用帳戶會嵌套在註冊帳戶之下。 計費使用者可以存取其各自範圍之下訂用帳戶和資源群組的成本資料。 他們沒有存取權可在 Azure 入口網站中查看或管理資源。 使用者可藉由瀏覽至 Azure 入口網站服務清單中的 [成本管理 + 計費] 來檢視成本。 然後，可以針對需要報告的特定訂用帳戶和資源群組，篩選成本。

計費使用者沒有管理群組的存取權，因為管理群組不會明確地落在特定計費帳戶之下。 必須明確地授與管理群組的存取權。 管理群組會彙總來自所有巢狀訂用帳戶的成本。 不過，只包含以使用量為基礎的購買。 不包含保留和第三方 Marketplace 供應項目等購買。 若要檢視這些成本，請使用 EA 計費帳戶。

## <a name="individual-agreement-scopes"></a>個別合約範圍

從個別供應項目 (例如隨用隨付) 和相關類型 (像是免費試用和開發/測試供應項目) 建立的 Azure 訂用帳戶，沒有明確的計費帳戶範圍。 然而，每個訂用帳戶都有帳戶擁有者或帳戶管理員，例如 EA 帳戶擁有者。

- [**計費帳戶**](../manage/view-all-accounts.md) - 代表一或多個 Azure 訂用帳戶的單一帳戶擁有者。 目前不支援將存取權授予給多個人或授與彙總成本檢視的存取權。

    資源類型：不適用

個別 Azure 訂用帳戶管理員可以從 [Azure 帳戶中心](https://account.azure.com/subscriptions)檢視和管理計費資料，例如發票和付款。 不過，他們無法在 Azure 入口網站中檢視成本資料或管理資源。 若要授與帳戶管理員的存取權，請使用先前所述的成本管理角色。

不同於 EA，個別 Azure 訂用帳戶管理員可以在 Azure 入口網站中查看其發票。 請記住，成本管理讀者和成本管理參與者角色不會提供發票的存取權。 如需詳細資訊，請參閱[如何授與發票的存取權](../manage/manage-billing-access.md#give-read-only-access-to-billing)。

## <a name="microsoft-customer-agreement-scopes"></a>Microsoft 客戶合約範圍

Microsoft 客戶合約計費帳戶具有下列範圍：

- **計費帳戶** - 表示適用於多項 Microsoft 產品和服務的客戶合約。 客戶合約計費帳戶的功能與 EA 註冊不相同。 EA 註冊與帳單設定檔更加相近。

    資源類型：`Microsoft.Billing/billingAccounts (accountType = Organization)`

- **帳單設定檔** - 定義發票中包含的訂用帳戶。 帳單設定檔是 EA 註冊的對等功能，因為這是發票的產生範圍。 同樣地，不是以使用量為基礎的購買 (例如 Marketplace 和保留) 僅適用於此範圍。 這類購買不會包含在發票區段中。

    資源類型：`Microsoft.Billing/billingAccounts/billingProfiles`

- **發票區段** - 代表發票或帳單設定檔中的一組訂用帳戶。 發票區段就像部門，多個人可以存取發票區段。

    資源類型：`Microsoft.Billing/billingAccounts/invoiceSections`

- **客戶** - 代表一組訂用帳戶，這些訂用帳戶與由合作夥伴納入 Microsoft 客戶合約的特定客戶相關聯。 此範圍是雲端解決方案提供者 (CSP) 所特有。

與 EA 計費範圍不同，客戶合約計費帳戶「會」繫結至單一目錄，而且不能有橫跨多個 Azure AD 目錄的訂用帳戶。

客戶合約計費範圍不適用於合作夥伴。 合作夥伴角色和權限都會記載於[指派使用者角色和權限](/partner-center/permissions-overview)。

客戶合約計費範圍支援下列角色：

- **擁有者** – 可以管理計費設定和存取權、檢視所有成本，以及管理成本配置。 例如，預算和匯出。 在函式中，此客戶合約計費範圍與[成本管理參與者 Azure 角色](../../role-based-access-control/built-in-roles.md#cost-management-contributor)相同。
- **餐與者** – 可以管理計費設定 (存取權除外)、檢視所有成本，以及管理成本配置。 例如，預算和匯出。 在函式中，此客戶合約計費範圍與[成本管理參與者 Azure 角色](../../role-based-access-control/built-in-roles.md#cost-management-contributor)相同。
- **讀者** – 可以檢視計費設定、成本資料和成本配置。 例如，預算和匯出。 在函式中，此客戶合約計費範圍與[成本管理讀者 Azure 角色](../../role-based-access-control/built-in-roles.md#cost-management-reader)相同。
- **發票管理員** – 可以檢視和支付發票費用，並可檢視成本資料和配置。 例如，預算和匯出。 在函式中，此客戶合約計費範圍與[成本管理讀者 Azure 角色](../../role-based-access-control/built-in-roles.md#cost-management-reader)相同。
- **Azure 訂用帳戶建立者** –可以建立 Azure 訂用帳戶、檢視成本，以及管理成本配置。 例如，預算和匯出。 在函式中，此客戶合約計費範圍與 EA 註冊帳戶擁有者角色相同。

Azure 訂用帳戶會嵌套在 [發票] 區段之下，如同其位於 EA 註冊帳戶之下的方式。 計費使用者可以存取其各自範圍之下訂用帳戶和資源群組的成本資料。 不過，他們沒有存取權可在 Azure 入口網站中查看或管理資源。 計費使用者可藉由瀏覽至 Azure 入口網站服務清單中的 [成本管理 + 計費] 來檢視成本。 然後，可以針對需要報告的特定訂用帳戶和資源群組，篩選成本。

計費使用者沒有管理群組的存取權，因為管理群組不會明確地落在計費帳戶之下。 不過，為組織啟用管理群組時，所有訂用帳戶成本都會彙總到計費帳戶和根管理群組，因為兩者都受限於單一目錄。 管理群組只包含以使用量為基礎的購買。 保留和第三方 Marketplace 供應項目等購買不會包含在管理群組中。 因此，計費帳戶和根管理群組可能會報告不同的總計。 若要檢視這些成本，請使用計費帳戶或個別的帳單設定檔。

## <a name="aws-scopes"></a>AWS 範圍

完成 AWS 整合之後，請參閱[設定 AWS 整合](aws-integration-set-up-configure.md)。 可以使用下列範圍：

- **外部計費帳戶** - 代表與第三方廠商簽訂的客戶合約。 這類似於 EA 計費帳戶。

    資源類型：`Microsoft.CostManagement/externalBillingAccounts`

- **外部訂用帳戶** - 代表與第三方廠商簽訂的客戶營運帳戶。 這類似於 Azure 訂用帳戶。

    資源類型：`Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>雲端解決方案提供者 (CSP) 範圍

Microsoft 客戶合約上的 CSP 與客戶支援下列範圍：

- **計費帳戶** - 表示適用於多項 Microsoft 產品和服務的客戶合約。 客戶合約計費帳戶的功能與 EA 註冊不相同。 EA 註冊與帳單設定檔更加相近。

    資源類型：`Microsoft.Billing/billingAccounts (accountType = Organization)`

- **帳單設定檔** - 定義發票中包含的訂用帳戶。 帳單設定檔是 EA 註冊的對等功能，因為這是發票的產生範圍。 同樣地，不是以使用量為基礎的購買 (例如 Marketplace 和保留) 僅適用於此範圍。

    資源類型：`Microsoft.Billing/billingAccounts/billingProfiles`

- **客戶** - 代表一組訂用帳戶，這些訂用帳戶與由合作夥伴納入 Microsoft 客戶合約的特定客戶相關聯。

只有具備「全域系統管理員」和「管理員代理人」角色的使用者，才能直接在合作夥伴的 Azure 租用戶中管理及檢視計費帳戶、帳單設定檔和客戶的成本。 如需合作夥伴中心角色的詳細資訊，請參閱[指派使用者角色和權限](/partner-center/permissions-overview)。

只有在客戶擁有 Microsoft 客戶合約時，Azure 成本管理才支援 CSP 合作夥伴客戶。 如需尚未列於 Microsoft 客戶合約的 CSP 支援客戶，請參閱[合作夥伴中心](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)。

成本管理不支援 CSP 範圍中的管理群組。 如果您有 CSP 訂用帳戶，並在成本分析中將範圍設定為管理群組，您會看到類似以下的錯誤：

`Management group <ManagementGroupName> does not have any valid subscriptions`

## <a name="switch-between-scopes-in-cost-management"></a>在成本管理中的範圍之間切換

Azure 入口網站中的所有成本管理檢視都會在檢視的左上方包含 [範圍] 選取膠囊。 使用該膠囊可快速變更範圍。 選取 [範圍] 膠囊按鈕可開啟範圍選擇器。 其會顯示計費帳戶、根管理群組，以及任何未嵌套在根管理群組之下的訂用帳戶。 若要選取範圍，請選取背景將其反白顯示，然後選取底部的 [選取]。 若要向內切入到巢狀範圍 (例如訂用帳戶中的資源群組)，請選取範圍名稱連結。 若要選取位於任何巢狀層級的父範圍，請選取範圍選擇器頂端的 [選取此 &lt;scope&gt;]。

## <a name="identify-the-resource-id-for-a-scope"></a>識別範圍的資源識別碼

使用成本管理 API 時，一定要知道範圍。 使用下列資訊來為成本管理 API 建置適當的範圍 URI。

### <a name="billing-accounts"></a>計費帳戶

1. 開啟 Azure 入口網站，然後瀏覽至服務清單中的 [成本管理 + 計費]。
2. 在計費帳戶功能表中選取 [屬性]。
3. 複製計費帳戶識別碼。
4. 您的範圍是：`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>帳單設定檔

1. 開啟 Azure 入口網站，然後瀏覽至服務清單中的 [成本管理 + 計費]。
2. 在計費帳戶功能表中選取 [帳單設定檔]。
3. 選取帳單設定檔名稱。
4. 在帳單設定檔功能表中選取 [屬性]。
5. 複製計費帳戶和帳單設定檔識別碼。
6. 您的範圍是：`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>發票區段

1. 開啟 Azure 入口網站，然後瀏覽至服務清單中的 [成本管理 + 計費]。
2. 在計費帳戶功能表中選取 [發票區段]。
3. 選取發票區段的名稱。
4. 在發票區段功能表中選取 [屬性]。
5. 複製計費帳戶和發票區段識別碼。
6. 您的範圍是：`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA 部門

1. 開啟 Azure 入口網站，然後瀏覽至服務清單中的 [成本管理 + 計費]。
2. 在計費帳戶功能表中選取 [部門]。
3. 選取部門名稱。
4. 在部門功能表中選取 [屬性]。
5. 複製計費帳戶和部門識別碼。
6. 您的範圍是：`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA 註冊帳戶

1. 開啟 Azure 入口網站，然後瀏覽至服務清單中的 [成本管理 + 計費]。
2. 在計費帳戶功能表中，選取 [註冊帳戶]。
3. 選取註冊帳戶的名稱。
4. 在註冊帳戶功能表中選取 [屬性]。
5. 複製計費帳戶和註冊帳戶識別碼。
6. 您的範圍是：`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>管理群組

1. 開啟 Azure 入口網站，然後瀏覽至服務清單中的 [管理群組]。
2. 瀏覽至管理群組。
3. 複製資料表中的管理群組識別碼。
4. 您的範圍是：`"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>訂用帳戶

1. 開啟 Azure 入口網站，然後瀏覽至服務清單中的 [訂用帳戶]。
2. 複製資料表中的訂用帳戶識別碼。
3. 您的範圍是：`"/subscriptions/{id}"`

### <a name="resource-groups"></a>資源群組

1. 開啟 Azure 入口網站，然後瀏覽至服務清單中的 [資源群組]。
2. 選取資源群組名稱。
3. 在資源群組功能表中選取 [屬性]。
4. 複製資源識別碼欄位值。
5. 您的範圍是：`"/subscriptions/{id}/resourceGroups/{name}"`

[Azure Global](https://management.azure.com) 和 [Azure Government](https://management.usgovcloudapi.net) 目前都支援成本管理。 如需 Azure Government 的詳細資訊，請參閱 [Azure Global 和 Government API 端點](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping)。

## <a name="next-steps"></a>後續步驟

- 如果您尚未完成成本管理的第一個快速入門，請在[開始分析成本](quick-acm-cost-analysis.md)閱讀它。
