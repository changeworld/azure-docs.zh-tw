---
title: 向整合服務環境添加資源
description: 將邏輯應用、集成帳戶、自訂連接器和託管連接器添加到整合服務環境 （ISE）
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164874"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>在 Azure 邏輯應用中向整合服務環境 （ISE） 添加資源

創建[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)後，添加邏輯應用、集成帳戶和連接器等資源，以便它們可以訪問 Azure 虛擬網路中的資源。 例如，創建 ISE 後可用的託管 ISE 連接器不會自動顯示在邏輯應用設計器中。 在使用這些 ISE 連接器之前，必須手動[將這些連接器添加並部署到 ISE，](#add-ise-connectors-environment)以便它們顯示在邏輯應用設計器中。

> [!IMPORTANT]
> 對於邏輯應用和集成帳戶在 ISE 中協同工作，兩者必須使用*與其位置相同的 ISE。*

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您為運行邏輯應用而創建的 ISE。 如果沒有 ISE，[請先創建 ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

* 要創建、添加或更新部署到 ISE 的資源，需要為該 ISE 上的擁有者或參與者角色分配，或者通過與 ISE 關聯的 Azure 訂閱或 Azure 資源組繼承許可權。 對於沒有擁有者、參與者或繼承許可權的使用者，可以為其分配整合服務環境參與者角色或整合服務環境開發人員角色。 有關基於角色的存取控制 （RBAC） 的詳細資訊，請參閱[什麼是 Azure 資源的基於角色的存取控制 （RBAC）？](../role-based-access-control/overview.md)

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>創建邏輯應用

要生成在整合服務環境 （ISE） 中運行的邏輯應用，請按照以下步驟操作：

1. 查找並打開 ISE（如果尚未打開）。 在 ISE 功能表中，**在"設置"** 下，選擇 **"邏輯應用** > **添加**"。

   ![向 ISE 添加新邏輯應用](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. 提供有關要創建的邏輯應用的資訊，例如：

   ![選取整合服務環境](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 要創建的邏輯應用的名稱 |
   | **訂閱** | 是 | 要使用的 Azure 訂用帳戶的名稱 |
   | **資源組** | 是 | 要使用的 Azure 資源組（新資源或現有）的名稱 |
   | **位置** | 是 | 在**整合服務環境中**，選擇要使用的 ISE（如果尚未選擇）。 <p><p> **重要提示**：要將邏輯應用與集成帳戶一起使用，兩者都必須使用相同的 ISE。 |
   ||||

1. 當您完成時，選取 [建立]****。

1. 繼續[以通常的方式創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

   有關觸發器和操作的工作方式以及與多租戶邏輯應用服務相比使用 ISE 時如何標記它們的差異，請參閱[ISE 概述中的隔離與多租戶](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)。

1. 要管理 ISE 中的邏輯應用和 API 連接，請參閱[管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>創建集成帳戶

根據創建時選擇的[ISE SKU，](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)您的 ISE 包括特定的集成帳戶使用方式，無需額外費用。 存在於整合服務環境 （ISE） 中的邏輯應用只能引用存在於同一 ISE 中的集成帳戶。 因此，對於集成帳戶要使用 ISE 中的邏輯應用，集成帳戶和邏輯應用必須使用與其位置*相同的環境*。 有關集成帳戶和 ISE 的詳細資訊，請參閱[ISE 的集成帳戶](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)。

要創建使用 ISE 的集成帳戶，請按照以下步驟操作：

1. 查找並打開 ISE（如果尚未打開）。 在 ISE 功能表中，**在"設置"** 下，選擇 **"添加集成帳戶** > **Add**"。

   ![向 ISE 添加新集成帳戶](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. 提供有關要創建的邏輯應用的資訊，例如：

   ![選取整合服務環境](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 要創建的集成帳戶的名稱 |
   | **訂閱** | 是 | 要使用的 Azure 訂閱的名稱 |
   | **資源組** | 是 | 要使用的 Azure 資源組（新資源或現有）的名稱 |
   | **定價層** | 是 | 用於集成帳戶的定價層 |
   | **位置** | 是 | 在**整合服務環境中**，選擇邏輯應用使用的相同 ISE（如果尚未選擇）。 <p><p> **重要提示**：要將集成帳戶與邏輯應用一起使用，兩者都必須使用相同的 ISE。 |
   ||||

1. 當您完成時，選取 [建立]****。

1. [以通常的方式將邏輯應用連結到您的集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。

1. 繼續將資源添加到您的集成帳戶，如[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)和[協定](../logic-apps/logic-apps-enterprise-integration-agreements.md)。

1. 要管理 ISE 中的集成帳戶，請參閱[管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>添加 ISE 連接器

創建 ISE 後可用的 Microsoft 託管連接器不會自動顯示在邏輯應用設計器上的連接器選取器中。 在使用這些 ISE 連接器之前，必須手動將這些連接器添加並部署到 ISE，以便它們顯示在邏輯應用設計器中。

1. 在 ISE 功能表上，**在"設置"** 下，選擇 **"託管連接器**"。 在工具列上，選取 [新增]****。

   ![查看託管連接器](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. 在"**添加新託管連接器**"窗格中，打開 **"查找連接器**"清單。 選擇要使用的 ISE 連接器，但尚未在 ISE 中部署。 選取 [建立]****。

   ![選擇要在 ISE 中部署的 ISE 連接器](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   只有符合條件但尚未部署到 ISE 的 ISE 連接器才能選擇。 已部署在 ISE 中的連接器似乎無法選擇。

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>創建自訂連接器

要在 ISE 中使用自訂連接器，請直接從 ISE 內部創建這些自訂連接器。

1. 查找並打開 ISE（如果尚未打開）。 在 ISE 功能表中，在 **"設置"** 下，選擇 **"自訂連接器** > **添加**"。

   ![建立自訂連接器](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. 提供用於自訂連接器的名稱、Azure 訂閱和 Azure 資源組（新或現有）。

1. 在 **"位置**"清單中，在 **"整合服務環境**"部分下，選擇邏輯應用使用的相同 ISE，然後選擇 **"創建**"，例如：

   ![選取整合服務環境](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. 選擇新的自訂連接器，然後選擇 **"編輯**"，例如：

   ![選擇和編輯自訂連接器](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. 繼續以通常的方式從[OpenAPI 定義](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition)或[SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)創建連接器。

1. 要管理 ISE 中的自訂連接器，請參閱[管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)。

## <a name="next-steps"></a>後續步驟

* [管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)
