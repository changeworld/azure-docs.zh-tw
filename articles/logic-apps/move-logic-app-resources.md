---
title: 跨訂用帳戶、資源群組或區域遷移邏輯應用程式
description: 將邏輯應用程式或整合帳戶遷移至其他 Azure 訂用帳戶、資源群組或位置（區域）
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 065bbc62d65d7e91728b10cd9f95b2e73ea03abc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878726"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>將邏輯應用程式資源移到其他 Azure 資源群組、區域或訂用帳戶

若要將邏輯應用程式或相關資源遷移至另一個 Azure 資源群組、區域或訂用帳戶，您可以使用各種方式來完成這些工作，例如 Azure 入口網站、Azure PowerShell、Azure CLI 和 REST API。 在移動資源之前，請先參閱下列考慮： 

* 您只能在 Azure 資源群組或訂用帳戶之間移動[特定的邏輯應用程式資源類型](../azure-resource-manager/management/move-support-resources.md#microsoftlogic)。

* 檢查您的 Azure 訂用帳戶和每個 Azure 區域中可擁有的邏輯應用程式資源數目[限制](../logic-apps/logic-apps-limits-and-config.md)。 這些限制會影響當區域在訂用帳戶或資源群組之間保持不變時，是否可以移動特定的資源類型。 例如，每個 Azure 訂用帳戶中的每個 Azure 區域只能有一個免費層整合帳戶。

* 當您移動資源時，Azure 會建立新的資源識別碼。 因此，請務必改為使用新的識別碼，並更新與已移動資源相關聯的任何腳本或工具。

* 在訂用帳戶、資源群組或區域之間遷移邏輯應用程式之後，您必須重新建立或重新授權任何需要開放驗證（OAuth）的連線。

* 您只能將[整合服務環境（ISE）](connect-virtual-network-vnet-isolated-environment-overview.md)移至存在於相同 Azure 區域或 Azure 訂用帳戶中的另一個資源群組。 您無法將 ISE 移至位於不同 Azure 區域或 Azure 訂用帳戶中的資源群組。 此外，在這類移動之後，您必須在邏輯應用程式工作流程、整合帳戶、連接等中更新 ISE 的所有參考。

## <a name="prerequisites"></a>Prerequisites

* 相同的 Azure 訂用帳戶，用來建立您要移動的邏輯應用程式或整合帳戶

* 資源擁有者許可權，可移動和設定您想要的資源。 深入瞭解[角色型存取控制（RBAC）](../role-based-access-control/built-in-roles.md#owner)。

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>在訂用帳戶之間移動資源

若要將資源（例如邏輯應用程式或整合帳戶）移至另一個 Azure 訂用帳戶，您可以使用 [Azure 入口網站]、[Azure PowerShell]、[Azure CLI] 或 [REST API]。 這些步驟涵蓋 Azure 入口網站，您可以在資源的區域保持不變時使用。 如需其他步驟和一般準備，請參閱[將資源移至新的資源群組或訂用](../azure-resource-manager/management/move-resource-group-and-subscription.md)帳戶。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，尋找並選取您想要移動的邏輯應用程式資源。

1. 在資源的 **[總覽**] 頁面上，選取 [**訂**用帳戶] 旁的 [**變更**] 連結。

1. 在 [**移動資源**] 頁面上，選取邏輯應用程式資源和您想要移動的任何相關資源。

1. 從 [**訂**用帳戶] 清單中，選取目的地訂用帳戶。

1. 從 [**資源群組**] 清單中，選取目的地資源群組。 或者，若要建立不同的資源群組，請選取 [**建立新群組**]。

1. 若要確認您是否瞭解任何與移動的資源相關聯的腳本或工具都將無法使用，直到您使用新的資源識別碼更新它們為止，請選取 [確認] 方塊，然後選取 **[確定]**。

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>在資源群組之間移動資源

若要將資源（例如邏輯應用程式、整合帳戶或[整合服務環境（ISE））](connect-virtual-network-vnet-isolated-environment-overview.md)移至另一個 Azure 資源群組，您可以使用 [Azure 入口網站]、[Azure PowerShell]、[Azure CLI] 或 [REST API]。 這些步驟涵蓋 Azure 入口網站，您可以在資源的區域保持不變時使用。 如需其他步驟和一般準備，請參閱[將資源移至新的資源群組或訂用](../azure-resource-manager/management/move-resource-group-and-subscription.md)帳戶。

在實際移動群組之間的資源之前，您可以測試是否可以成功地將資源移至另一個群組。 如需詳細資訊，請參閱[驗證您的移動](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move)。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，尋找並選取您想要移動的邏輯應用程式資源。

1. 在資源的 **[總覽**] 頁面上，于 [**資源群組**] 旁選取 [**變更**] 連結。

1. 在 [**移動資源**] 頁面上，選取邏輯應用程式資源和您想要移動的任何相關資源。

1. 從 [**資源群組**] 清單中，選取目的地資源群組。 或者，若要建立不同的資源群組，請選取 [**建立新群組**]。

1. 若要確認您是否瞭解任何與移動的資源相關聯的腳本或工具都將無法使用，直到您使用新的資源識別碼更新它們為止，請選取 [確認] 方塊，然後選取 **[確定]**。

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>在區域之間移動資源

當您想要將邏輯應用程式移到不同的區域時，您的選項取決於您建立邏輯應用程式的方式。 根據您選擇的選項，您必須在邏輯應用程式中重新建立或重新授權連線。

* 在 Azure 入口網站中，在新的區域中重新建立邏輯應用程式，並重新設定工作流程設定。 若要節省時間，您可以將基礎工作流程定義和來自來源應用程式的連線複製到目的地應用程式。 若要查看邏輯應用程式後方的「程式碼」，請在邏輯應用程式設計工具工具列上選取 [程式**代碼視圖**]。

* 藉由使用 Visual Studio 和適用于 Visual Studio 的 Azure Logic Apps 工具，您可以從 Azure 入口網站中[開啟並下載邏輯應用程式](../logic-apps/manage-logic-apps-with-visual-studio.md)，以作為[Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。 此範本大多準備好進行部署，並包含邏輯應用程式的資源定義，包括工作流程本身和連線。 此範本也會針對要在部署時使用的值宣告參數。 如此一來，您就可以根據自己的需求，更輕鬆地變更部署邏輯應用程式的位置和方式。 若要指定部署的位置和其他必要資訊，您可以使用不同的參數檔案。

* 如果您使用持續整合（CI）和持續傳遞（CD）工具（例如 Azure DevOps 中的 Azure Pipelines）來建立和部署邏輯應用程式，您可以使用這些工具將應用程式部署到另一個區域。

如需邏輯應用程式部署範本的詳細資訊，請參閱下列主題：

* [總覽：使用 Azure Resource Manager 範本自動部署 Azure Logic Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [尋找、開啟並從 Azure 入口網站下載邏輯應用程式至 Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [建立 Azure Logic Apps 的 Azure Resource Manager 範本](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [部署 Azure Logic Apps 的 Azure Resource Manager 範本](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>相關資源

某些 Azure 資源（例如 Azure 中的內部部署資料閘道資源）可能存在於不同于使用這些資源之邏輯應用程式的區域中。 不過，其他 Azure 資源（例如連結的整合帳戶）必須存在於與邏輯應用程式相同的區域中。 根據您的案例，請確定您的邏輯應用程式可以存取您的應用程式預期存在於相同區域中的資源。

例如，若要將邏輯應用程式連結至整合帳戶，這兩個資源必須存在於相同的區域中。 在嚴重損壞修復的情況下，您通常會想要具有相同設定和構件的整合帳戶。 在其他案例中，您可能需要具有不同設定和成品的整合帳戶。

連接器的作者和具有相同 Azure 訂用帳戶和相同 Azure Active Directory 租使用者的使用者可以看到 Azure Logic Apps 中的自訂連接器。 這些連接器可在邏輯應用程式部署所在的相同區域中使用。 如需詳細資訊，請參閱[在貴組織中共用自訂連接器](https://docs.microsoft.com/connectors/custom-connectors/share)。

您從 Visual Studio 取得的範本只會包含邏輯應用程式及其連線的資源定義。 因此，如果您的邏輯應用程式使用其他資源，例如整合帳戶和 B2B 成品（例如合作夥伴、協定和架構），您就必須使用 Azure 入口網站來匯出該整合帳戶的範本。 此範本包含整合帳戶和構件的資源定義。 不過，此範本並未完全參數化。 因此，您必須手動將您想要用於部署的值參數化。

### <a name="export-templates-for-integration-accounts"></a>匯出整合帳戶的範本

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的整合帳戶。

1. 在整合帳戶功能表的 [**設定**] 底下，選取 [**匯出範本**]。

1. 在工具列上，選取 [**下載**]，然後儲存範本。

1. 開啟並編輯範本，將部署的必要值參數化。

## <a name="next-steps"></a>後續步驟

[將 Azure 資源移至新的資源群組或訂用帳戶](../azure-resource-manager/management/move-resource-group-and-subscription.md)
