---
title: 大規模管理 Azure Sentinel 的工作區
description: 瞭解如何有效率地管理委派客戶資源上的 Azure Sentinel。
ms.date: 06/17/2020
ms.topic: how-to
ms.openlocfilehash: 70810ca54c62e73d5dec76495eb5179b84db60fe
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105263"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>大規模管理 Azure Sentinel 的工作區

身為服務提供者，您可能已將多個客戶租使用者上架至[Azure 燈塔](../overview.md)。 Azure 燈塔可讓服務提供者一次對數個 Azure Active Directory （Azure AD）租使用者執行大規模作業，讓管理工作更有效率。

Azure Sentinel 提供安全性分析和威脅情報，提供警示偵測、威脅可見度、主動式搜尋和威脅回應的單一解決方案。 透過 Azure 燈塔，您可以大規模管理跨租使用者的多個 Azure Sentinel 工作區。 這可讓您在多個工作區執行查詢，或建立活頁簿以視覺化和監視連接資料來源中的資料，以取得深入資訊。 例如查詢和腳本的 IP 會保留在您的管理租使用者中，但可用於在客戶租使用者中執行安全性管理。

本主題概要說明如何以可調整的方式，使用[Azure Sentinel](../../sentinel/overview.md)跨租使用者可見度和受控安全性服務。

## <a name="architectural-considerations"></a>架構考慮

對於想要使用 Azure Sentinel 建立安全性即服務供應專案的受控安全性服務提供者（MSSP），可能需要單一安全性作業中心（SOC），以集中監視、管理及設定在個別客戶租使用者中部署的多個 Azure Sentinel 工作區。 同樣地，具有多個 Azure AD 租使用者的企業，可能會想要集中管理跨租使用者部署的多個 Azure Sentinel 工作區。

這個集中式部署模型具有下列優點：

- 資料的擁有權會保留在每個受管理的租使用者中。
- 支援在地理界限內儲存資料的需求。
- 確保資料隔離，因為多個客戶的資料不會儲存在相同的工作區中。 
- 防止從受管理的租使用者外泄資料，以協助確保資料合規性。
- 相關成本會收取給每個受管理的租使用者，而不是管理租使用者。
- 所有資料來源的資料和與 Azure Sentinel 整合的資料連線器（例如 Azure AD 活動記錄、Office 365 記錄或 Microsoft 威脅防護警示）都會保留在每個客戶租使用者內。
- 減少網路延遲。
- 輕鬆新增或移除新的子公司或客戶。

## <a name="granular-role-based-access-control-rbac"></a>細微的角色型存取控制（RBAC）

MSSP 將管理的每個客戶訂用帳戶都必須[上架至 Azure 燈塔](onboard-customer.md)。 這可讓管理租使用者中的指定使用者存取和執行部署在客戶租使用者中 Azure Sentinel 工作區上的管理作業。

建立授權時，您可以將 Azure Sentinel 內建角色指派給您管理的租使用者中的使用者、群組或服務主體：

- [Azure Sentinel 讀取器](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel 回應程式](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel 參與者](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

您可能也會想要指派額外的內建角色來執行其他功能。 如需可與 Azure Sentinel 搭配使用之特定角色的詳細資訊，請參閱[Azure Sentinel 中的許可權](../../sentinel/roles.md)。

一旦您上架客戶，指定的使用者就可以登入您的管理租使用者，並使用已指派的角色[直接存取客戶的 Azure Sentinel 工作區](../../sentinel/multiple-tenants-service-providers.md)。

## <a name="view-and-manage-incidents-across-workspaces"></a>跨工作區查看和管理事件

如果您要管理多個客戶的 Azure Sentinel 資源，您可以同時在多個租使用者的多個工作區中，同時查看和管理事件。 如需詳細資訊，請參閱[一次處理多個工作區中的事件](../../sentinel/multiple-workspace-view.md)，並在[工作區和租使用者之間擴充 Azure Sentinel](../../sentinel/extend-sentinel-across-workspaces-tenants.md)。

> [!NOTE]
> 請確定已在管理的租使用者中，對受管理的所有工作區指派讀取和寫入權限。 如果使用者只擁有部分工作區的讀取權限，則在選取這些工作區中的事件時，可能會顯示警告訊息，而且使用者將無法修改這些事件，或任何您已選取的事件（即使您對其他人有許可權）。

## <a name="configure-playbooks-for-mitigation"></a>設定風險降低的腳本

當警示觸發時，[您可以使用](../../sentinel/tutorial-respond-threats-playbook.md)腳本自動降低風險。 這些腳本可以手動執行，也可以在觸發特定警示時自動執行。 您可以在管理租使用者或客戶租使用者中部署操作手冊，並根據租使用者的使用者需要採取動作來回應安全性威脅，來設定回應程式。

## <a name="create-cross-tenant-workbooks"></a>建立跨租使用者的活頁簿

[Azure Sentinel 中的 Azure 監視器活頁簿](../../sentinel/overview.md#workbooks)可協助您從連接的資料來源將資料視覺化並加以監視，以取得深入資訊。 您可以使用 Azure Sentinel 中的內建活頁簿範本，或為您的案例建立自訂活頁簿。

您可以在管理的租使用者中部署活頁簿，並建立大規模的儀表板，以監視及查詢跨客戶租使用者的資料。 如需詳細資訊，請參閱[跨工作區監視](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks)。 請注意，[在多個工作區中不支援](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces)某些功能。

您也可以將活頁簿直接部署在您針對該客戶特定案例所管理的個別租使用者中。

## <a name="run-queries-across-azure-sentinel-workspaces"></a>跨 Azure Sentinel 工作區執行查詢

您可以在管理租使用者中集中建立並儲存用於威脅偵測的 Log Analytics 查詢。 然後您可以使用 Union 運算子和 workspace （）運算式，在所有客戶的 Azure Sentinel 工作區中執行這些查詢。 如需詳細資訊，請參閱[跨工作區查詢](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying)。

## <a name="use-automation-for-cross-workspace-management"></a>使用自動化進行跨工作區管理

您可以使用自動化來管理多個 Azure Sentinel 工作區，以及設定[搜尋查詢](../../sentinel/hunting.md)、腳本和活頁簿。 如需詳細資訊，請參閱[使用自動化的跨工作區管理](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation)。

請注意，某些功能[目前不支援跨多個工作區](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces)。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure Sentinel](../../sentinel/overview.md)。
- 請參閱[Azure Sentinel 定價頁面](https://azure.microsoft.com/pricing/details/azure-sentinel/)。
- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。

