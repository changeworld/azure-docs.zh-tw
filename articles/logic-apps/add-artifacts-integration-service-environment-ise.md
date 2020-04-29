---
title: 將資源新增到整合服務環境
description: 將邏輯應用程式、整合帳戶、自訂連接器和受控連接器新增至您的整合服務環境（ISE）
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79164874"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>在 Azure Logic Apps 中將資源新增至您的整合服務環境（ISE）

在您建立[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)之後，請新增邏輯應用程式、整合帳戶和連接器等資源，讓它們可以存取您 Azure 虛擬網路中的資源。 例如，您在建立 ISE 之後變成可用的受控 ISE 連接器，並不會自動出現在邏輯應用程式設計工具中。 在您可以使用這些 ISE 連接器之前，您必須手動[新增這些連接器，並將它們部署到 ISE](#add-ise-connectors-environment) ，使其出現在邏輯應用程式設計工具中。

> [!IMPORTANT]
> 若要讓邏輯應用程式和整合帳戶在 ISE 中一起工作，兩者都必須使用與其位置*相同的 ISE* 。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您建立用來執行邏輯應用程式的 ISE。 如果您沒有 ISE，請[先建立 ise](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

* 若要建立、新增或更新部署至 ISE 的資源，您必須在該 ISE 上指派擁有者或參與者角色，或是您透過與 ISE 相關聯的 Azure 訂用帳戶或 Azure 資源群組來繼承許可權。 對於沒有擁有者、參與者或繼承許可權的人員，他們可以獲指派「整合服務環境參與者」角色或「整合服務環境開發人員」角色。 如需角色型存取控制（RBAC）的詳細資訊，請參閱[什麼是適用于 Azure 資源的角色型存取控制（rbac）](../role-based-access-control/overview.md)？

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>建立邏輯應用程式

若要建立在整合服務環境（ISE）中執行的邏輯應用程式，請遵循下列步驟：

1. 尋找並開啟您的 ISE （如果尚未開啟）。 從 ISE 功能表的 [**設定**] 底下，選取 [**邏輯應用程式** > ] [**新增**]。

   ![將新的邏輯應用程式新增至 ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. 提供您想要建立之邏輯應用程式的相關資訊，例如：

   ![選取整合服務環境](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 要建立之邏輯應用程式的名稱 |
   | **訂用帳戶** | 是 | 要使用的 Azure 訂用帳戶的名稱 |
   | **資源群組** | 是 | 要使用的 Azure 資源群組（新的或現有的）名稱 |
   | **位置** | 是 | 在 [**整合服務環境**] 底下，選取要使用的 ISE （如果尚未選取）。 <p><p> **重要**：若要將邏輯應用程式與整合帳戶搭配使用，兩者都必須使用相同的 ISE。 |
   ||||

1. 當您完成時，選取 [建立]  。

1. 繼續[以一般方式建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

   如需觸發程式和動作的工作方式，以及當您使用 ISE 與多租使用者 Logic Apps 服務時的標記方式差異，請參閱[ISE 總覽中的隔離與多租](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)使用者。

1. 若要管理 ISE 中的邏輯應用程式和 API 連線，請參閱[管理您的整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>建立整合帳戶

根據在建立時選取的[ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) ，您的 ISE 會包含特定的整合帳戶使用方式，而不需額外費用。 存在於整合服務環境（ISE）中的邏輯應用程式，只能參考存在於相同 ISE 中的整合帳戶。 因此，若要讓整合帳戶在 ISE 中使用邏輯應用程式，整合帳戶和邏輯應用程式都必須使用與其位置*相同的環境*。 如需整合帳戶和 Ise 的詳細資訊，請參閱[整合帳戶與 ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)。

若要建立使用 ISE 的整合帳戶，請遵循下列步驟：

1. 尋找並開啟您的 ISE （如果尚未開啟）。 從 ISE 功能表的 [**設定**] 底下，選取 [**整合帳戶** > ] [**新增**]。

   ![將新的整合帳戶新增至 ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. 提供您想要建立之邏輯應用程式的相關資訊，例如：

   ![選取整合服務環境](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 您想要建立之整合帳戶的名稱 |
   | **訂用帳戶** | 是 | 您想要使用之 Azure 訂用帳戶的名稱 |
   | **資源群組** | 是 | 要使用的 Azure 資源群組（新的或現有的）名稱 |
   | **定價層** | 是 | 要用於整合帳戶的定價層 |
   | **位置** | 是 | 在 [**整合服務環境**] 底下，選取您的邏輯應用程式所使用的相同 ISE （如果尚未選取）。 <p><p> **重要**：若要將整合帳戶與邏輯應用程式搭配使用，兩者都必須使用相同的 ISE。 |
   ||||

1. 當您完成時，選取 [建立]  。

1. [以一般方式將邏輯應用程式連結到您的整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。

1. 繼續將資源新增至您的整合帳戶，例如[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)和[協定](../logic-apps/logic-apps-enterprise-integration-agreements.md)。

1. 若要管理 ISE 中的整合帳戶，請參閱[管理您的整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>新增 ISE 連接器

當您建立 ISE 之後，Microsoft 管理的連接器不會自動顯示在邏輯應用程式設計工具的連接器選擇器中。 在您可以使用這些 ISE 連接器之前，您必須手動新增這些連接器，並將其部署至 ISE，使其出現在邏輯應用程式設計工具中。

1. 在您的 ISE 功能表的 [**設定**] 底下，選取 [**受控連接器**]。 在工具列上，選取 [新增]****。

   ![查看受管理的連接器](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. 在 [新增**受管理的連接器**] 窗格中，開啟 [**尋找連接器**] 清單。 選取您想要使用但尚未部署在 ISE 中的 ISE 連接器。 選取 [建立]  。

   ![選取您想要在 ISE 中部署的 ISE 連接器](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   只有符合資格但尚未部署到 ISE 的 ISE 連接器才會顯示供您選取。 您的 ISE 中已部署的連接器似乎無法選取。

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>建立自訂連接器

若要在 ISE 中使用自訂連接器，請直接在 ISE 內建立這些自訂連接器。

1. 尋找並開啟您的 ISE （如果尚未開啟）。 從 ISE 功能表的 [**設定**] 底下，選取 [**自訂連接器** > ] [**新增**]。

   ![建立自訂連接器](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. 提供名稱、Azure 訂用帳戶和 Azure 資源群組（新的或現有的），以用於您的自訂連接器。

1. 從 [**位置**] 清單的 [**整合服務環境**] 區段底下，選取您的邏輯應用程式所使用的相同 ISE，然後選取 [**建立**]，例如：

   ![選取整合服務環境](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. 選取新的自訂連接器，然後選取 [**編輯**]，例如：

   ![選取並編輯自訂連接器](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. 繼續以一般的方式從[OpenAPI 定義](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition)或[SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)建立連接器。

1. 若要管理 ISE 中的自訂連接器，請參閱[管理您的整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)。

## <a name="next-steps"></a>後續步驟

* [管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)
