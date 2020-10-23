---
title: 大規模管理 Azure Sentinel 工作區
description: 瞭解如何有效管理委派客戶資源上的 Azure Sentinel。
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 91e2f875aa2fc067420c0c6eda4e7dd56bd2b088
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424092"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>大規模管理 Azure Sentinel 工作區

作為服務提供者，您可能已上線多個客戶租使用者來 [Azure Lighthouse](../overview.md)。 Azure Lighthouse 可讓服務提供者一次在多個 Azure Active Directory () Azure AD 之間執行大規模作業，讓管理工作更有效率。

Azure Sentinel 提供安全性分析和威脅情報，提供單一解決方案來偵測警示、威脅可見度、主動式搜尋和威脅回應。 您可以使用 Azure Lighthouse，大規模管理跨租使用者的多個 Azure Sentinel 工作區。 這可讓您在多個工作區執行查詢，或建立活頁簿來視覺化和監視來自您連線資料來源的資料，以深入瞭解。 例如查詢和腳本的 IP 會保留在您的管理租使用者中，但可以用來在客戶租使用者中執行安全性管理。

本主題概述如何以可調整的方式使用 [Azure Sentinel](../../sentinel/overview.md) ，以提供跨租使用者可見度和受控安全性服務。

> [!TIP]
> 雖然我們指的是本主題中的服務提供者和客戶，但本指南也適用于 [使用 Azure Lighthouse 來管理多個](../concepts/enterprise.md)租使用者的企業。

## <a name="architectural-considerations"></a>架構考慮

針對受管理的安全性服務提供者 (MSSP) 想要使用 Azure Sentinel 建立安全性即服務供應專案，可能需要單一安全性作業中心 (SOC) ，以集中監視、管理及設定多個部署在個別客戶租使用者中的 Azure Sentinel 工作區。 同樣地，具有多個 Azure AD 租使用者的企業可能會想要集中管理跨其租使用者部署的多個 Azure Sentinel 工作區。

這種集中式部署模型的優點如下：

- 每個受管理的租使用者都會保留資料的擁有權。
- 支援在地理界限內儲存資料的需求。
- 確保資料隔離，因為多個客戶的資料不會儲存在相同的工作區中。
- 防止資料從受管理的租使用者遭到外泄，以協助確保資料符合規範。
- 相關成本會對每個受管理的租使用者收費，而不是管理租使用者。
- 與 Azure Sentinel (（例如 Azure AD 活動記錄、Office 365 記錄或 Microsoft 威脅防護) 警示）整合的所有資料來源和資料連線器的資料，都會保留在每個客戶租使用者內。
- 減少網路延遲。
- 輕鬆地新增或移除新的子公司或客戶。

> [!NOTE]
> 您可以管理位於不同 [區域](../../availability-zones/az-overview.md#regions)的委派資源。 但是，不支援跨 [全國雲端](../../active-directory/develop/authentication-national-cloud.md) 、Azure 公用雲端或跨兩個不同國家雲端的訂用帳戶委派。

## <a name="granular-azure-role-based-access-control-azure-rbac"></a> (Azure RBAC) 的細微 Azure 角色型存取控制

MSSP 將管理的每個客戶訂用帳戶都必須 [上線至 Azure Lighthouse](onboard-customer.md)。 這可讓管理租使用者中指定的使用者在客戶租使用者中部署的 Azure Sentinel 工作區上存取和執行管理作業。

建立您的授權時，您可以將 Azure Sentinel 內建角色指派給管理租使用者中的使用者、群組或服務主體：

- [Azure Sentinel 讀者](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel 回應程式](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel 參與者](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

您也可能想要指派其他內建角色來執行其他功能。 如需可搭配 Azure Sentinel 使用之特定角色的詳細資訊，請參閱 [Azure Sentinel 中的許可權](../../sentinel/roles.md)。

上線客戶之後，指定的使用者可以登入您的管理租使用者，並使用已指派的角色 [直接存取客戶的 Azure Sentinel 工作區](../../sentinel/multiple-tenants-service-providers.md) 。

## <a name="view-and-manage-incidents-across-workspaces"></a>跨工作區查看和管理事件

如果您要管理多個客戶的 Azure Sentinel 資源，您可以同時在多個租使用者之間查看和管理多個工作區中的事件。 如需詳細資訊，請參閱 [一次處理許多工作區中的事件](../../sentinel/multiple-workspace-view.md) ，並在 [工作區和租使用者之間擴充 Azure Sentinel](../../sentinel/extend-sentinel-across-workspaces-tenants.md)。

> [!NOTE]
> 請確定您管理租使用者中的使用者已獲指派所有受管理工作區的讀取和寫入權限。 如果使用者只有部分工作區的讀取權限，在這些工作區中選取事件時，可能會顯示警告訊息，且使用者將無法修改這些事件或您使用這些 (選取的任何其他事件，即使您有其他) 的許可權也一樣。

## <a name="configure-playbooks-for-mitigation"></a>設定風險降低的手冊

當觸發警示時[，可以使用腳本來自動](../../sentinel/tutorial-respond-threats-playbook.md)緩和。 這些腳本可以手動執行，也可以在觸發特定警示時自動執行。 您可以在管理租使用者或客戶租使用者中部署操作手冊，並根據租使用者的使用者需要採取動作來回應安全性威脅，來設定回應程式。

## <a name="create-cross-tenant-workbooks"></a>建立跨租使用者的活頁簿

[Azure Sentinel 中的 Azure 監視器活頁簿](../../sentinel/overview.md#workbooks) 可協助您將資料從連接的資料來源視覺化和監視，以深入瞭解。 您可以在 Azure Sentinel 中使用內建的活頁簿範本，也可以為您的案例建立自訂的活頁簿。

您可以在管理租使用者中部署活頁簿，並建立大規模儀表板來監視和查詢客戶租使用者之間的資料。 如需詳細資訊，請參閱 [跨工作區監視](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks)。 

您也可以將活頁簿直接部署在您針對該客戶特定案例管理的個別租使用者中。

## <a name="run-log-analytics-and-hunting-queries-across-azure-sentinel-workspaces"></a>在 Azure Sentinel 工作區中執行 Log Analytics 和搜尋查詢

在管理租使用者（包括 [搜尋查詢](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting)）集中，建立並儲存威脅偵測的 Log Analytics 查詢。 然後，您可以使用 Union 運算子和工作區 ( # A1 運算式，在所有客戶的 Azure Sentinel 工作區上執行這些查詢。 如需詳細資訊，請參閱 [跨工作區查詢](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying)。

## <a name="use-automation-for-cross-workspace-management"></a>使用自動化進行跨工作區管理

您可以使用自動化來管理多個 Azure Sentinel 工作區，以及設定 [搜尋查詢](../../sentinel/hunting.md)、工作手冊和活頁簿。 如需詳細資訊，請參閱 [使用自動化的跨工作區管理](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation)。

## <a name="manage-security-of-office-365-environments"></a>管理 Office 365 環境的安全性

使用 Azure Lighthouse 搭配 Azure Sentinel 來管理跨租使用者的 Office 365 環境安全性。 首先， [受管理的租使用者中必須啟用現成的 Office 365 資料連線器](../../sentinel/connect-office-365.md) ，才能將 Exchange 和 SharePoint (中的使用者和系統管理活動的相關資訊（包括 OneDrive) ）內嵌至受管理租使用者內的 Azure Sentinel 工作區。 這包括有關動作的詳細資料，例如檔案下載、傳送的存取要求、群組事件的變更，以及信箱作業，以及執行動作之使用者的相關資訊。 [Office 365 DLP 警示](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820) 也支援作為內建 Office 365 連接器的一部分。

您可以啟用 [Microsoft Cloud App Security (MCAS) 連接器](../../sentinel/connect-cloud-app-security.md) ，以將警示和 Cloud Discovery 記錄串流至 Azure Sentinel。 這可讓您深入瞭解雲端應用程式、取得精密的分析，以識別並對抗網路威脅，以及控制資料的傳輸方式。 您可以 [使用 (CEF) 的常見事件格式 ](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849)來取用 MCAS 的活動記錄。

設定 Office 365 資料連線器之後，您可以使用跨租使用者 Azure Sentinel 功能，例如在活頁簿中查看和分析資料、使用查詢建立自訂警示，以及設定腳本來回應威脅。

## <a name="next-steps"></a>後續步驟

- 瞭解 [Azure Sentinel](../../sentinel/overview.md)。
- 請參閱 [Azure Sentinel 定價頁面](https://azure.microsoft.com/pricing/details/azure-sentinel/)。
- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。

