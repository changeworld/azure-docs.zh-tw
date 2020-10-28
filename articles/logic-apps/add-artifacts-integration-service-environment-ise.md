---
title: 將資源新增到整合服務環境
description: '將邏輯應用程式、整合帳戶、自訂連接器和受控連接器新增至您的整合服務環境 (ISE) '
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 147247c663311cfb3e05a986c6fb2bffbb41158b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675200"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>在 Azure Logic Apps 中將資源新增至您的整合服務環境 (ISE) 

在您建立 [整合服務環境 (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)之後，請新增邏輯應用程式、整合帳戶和連接器等資源，讓它們可以存取 Azure 虛擬網路中的資源。 例如，在您建立 ISE 之後變成可用的受控 ISE 連接器，並不會自動出現在邏輯應用程式設計工具中。 在您可以使用這些 ISE 連接器之前，您必須手動 [新增這些連接器，並將它們部署至 ISE](#add-ise-connectors-environment) ，使其出現在邏輯應用程式設計工具中。

> [!IMPORTANT]
> 若要讓邏輯應用程式和整合帳戶可在 ISE 中一起運作，兩者都必須使用「相同的 ISE」作為其位置。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您建立用來執行邏輯應用程式的 ISE。 如果您沒有 ISE，請 [先建立 ise](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

* 若要建立、新增或更新部署至 ISE 的資源，您必須將該 ISE 上的擁有者或參與者角色指派給您，或透過與 ISE 相關聯的 Azure 訂用帳戶或 Azure 資源群組來繼承許可權。 針對沒有擁有者、參與者或繼承許可權的人員，可以將其指派為「整合服務環境參與者」角色或「整合服務環境開發人員」角色。 如需詳細資訊，請參閱 [什麼是 AZURE RBAC)  (azure 角色型存取控制 ](../role-based-access-control/overview.md)？

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>建立邏輯應用程式

若要建立在整合服務環境中執行 (ISE) 的邏輯應用程式，請遵循下列步驟：

1. 尋找並開啟您的 ISE （如果尚未開啟）。 從 ISE 功能表的 [ **設定** ] 底下，選取 [ **邏輯應用程式**  >  **新增** ]。

   ![將新的邏輯應用程式新增至 ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. 提供您想要建立之邏輯應用程式的相關資訊，例如：

   ![顯示「邏輯應用程式」「建立」視窗的螢幕擷取畫面，其中包含輸入的範例資訊。](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 要建立之邏輯應用程式的名稱 |
   | **訂用帳戶** | 是 | 要使用的 Azure 訂用帳戶的名稱 |
   | **資源群組** | 是 | Azure 資源群組的名稱 (要使用的新或現有)  |
   | **位置** | 是 | 在 [ **整合服務環境** ] 下，選取要使用的 ISE （如果尚未選取）。 <p><p> **重要** ：若要將邏輯應用程式與整合帳戶搭配使用，兩者都必須使用相同的 ISE。 |
   ||||

1. 當您完成時，選取 [建立]  。

1. 繼續 [以一般方式建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

   如需觸發程式和動作的運作方式，以及當您使用 ISE 相較于多租使用者 Logic Apps 服務時如何標示它們的差異，請參閱 [ISE 總覽中的隔離與多租](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)使用者。

1. 若要管理您 ISE 中的邏輯應用程式和 API 連接，請參閱 [管理您的整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>建立整合帳戶

根據在建立時選取的 [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) ，ise 會包含特定的整合帳戶使用方式，而不會產生額外費用。 存在於整合服務環境 (ISE) 的邏輯應用程式只能參考存在於相同 ISE 中的整合帳戶。 因此，若要讓整合帳戶使用 ISE 中的邏輯應用程式，整合帳戶和邏輯應用程式都必須使用與其位置 *相同的環境* 。 如需整合帳戶和 Ise 的詳細資訊，請參閱 [使用 ISE 的整合帳戶](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)。

若要建立使用 ISE 的整合帳戶，請遵循下列步驟：

1. 尋找並開啟您的 ISE （如果尚未開啟）。 從 ISE 功能表的 [ **設定** ] 底下，選取 [ **整合帳戶**  >  **新增** ]。

   ![將新的整合帳戶新增至 ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. 提供您想要建立之邏輯應用程式的相關資訊，例如：

   ![選取整合服務環境](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 您要建立的整合帳戶名稱 |
   | **訂用帳戶** | 是 | 您要使用的 Azure 訂用帳戶名稱 |
   | **資源群組** | 是 | Azure 資源群組的名稱 (要使用的新或現有)  |
   | **定價層** | 是 | 要用於整合帳戶的定價層 |
   | **位置** | 是 | 在 [ **整合服務環境** ] 下，選取您的邏輯應用程式所使用的相同 ISE （如果尚未選取）。 <p><p> **重要** ：若要搭配使用您的整合帳戶與邏輯應用程式，兩者都必須使用相同的 ISE。 |
   ||||

1. 當您完成時，選取 [建立]  。

1. [以一般方式將邏輯應用程式連結至整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。

1. 繼續將資源新增至您的整合帳戶，例如 [交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md) 和 [協定](../logic-apps/logic-apps-enterprise-integration-agreements.md)。

1. 若要管理您 ISE 中的整合帳戶，請參閱 [管理您的整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>新增 ISE 連接器

建立 ISE 之後，受控 ISE 連接器不會自動出現在邏輯應用程式設計工具的連接器選擇器中。 在您可以使用這些 ISE 連接器之前，您必須手動新增這些連接器，並將其部署至 ISE，使其出現在邏輯應用程式設計工具中。

> [!IMPORTANT]
> 受控 ISE 連接器目前不支援 [標記](../azure-resource-manager/management/tag-support.md)。 如果您設定強制標記的原則，嘗試新增 ISE 連接器可能會失敗，並出現類似以下範例的錯誤：
> 
> ```json
> {
>    "error": { 
>       "code": "IntergrationServiceEnvironmentManagedApiDefinitionTagsNotSupported", 
>       "message": "The tags are not supported in the managed API 'azureblob'."
>    }
> }
> ```
> 
> 因此，若要新增 ISE 連接器，您必須停用或移除原則。 

1. 在 ISE 功能表的 [ **設定** ] 底下，選取 [ **受管理的連接器** ]。 在工具列上，選取 [新增]  。

   ![查看受控連接器](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. 在 [ **新增受管理的連接器** ] 窗格中，開啟 [ **尋找連接器** ] 清單。 選取您要使用但尚未部署在 ISE 中的 ISE 連接器。 選取 [建立]。

   ![選取您要在 ISE 中部署的 ISE 連接器](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   只有符合資格但尚未部署至 ISE 的 ISE 連接器，才會顯示為可供您選取。 已部署在 ISE 中的連接器無法供選取。

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>建立自訂連接器

若要在 ISE 中使用自訂連接器，請直接從 ISE 內建立這些自訂連接器。

1. 尋找並開啟您的 ISE （如果尚未開啟）。 從 ISE 功能表的 [ **設定** ] 底下，選取 [ **自訂連接器**  >  **新增** ]。

   ![建立自訂連接器](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. 提供名稱、Azure 訂用帳戶和 Azure 資源群組 (要用於自訂連接器的新或現有) 。

1. 從 [ **位置** ] 清單中的 [ **整合服務環境** ] 區段下，選取您的邏輯應用程式所使用的相同 ISE，然後選取 [ **建立** ]，例如：

   ![顯示 [建立 Logic Apps 自訂連接器] 視窗的螢幕擷取畫面，其中已選取範例資訊。](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. 選取新的自訂連接器，然後選取 [ **編輯** ]，例如：

   ![選取和編輯自訂連接器](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. 從 [OpenAPI 定義](/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) 或 [SOAP](/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)的一般方式，繼續建立連接器。

1. 若要在 ISE 中管理自訂連接器，請參閱 [管理您的整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)。

## <a name="next-steps"></a>後續步驟

* [管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)
