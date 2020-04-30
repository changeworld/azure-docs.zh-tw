---
title: 管理 Azure Logic Apps 中的整合服務環境
description: 檢查網路健康狀態，並管理整合服務環境（ISE）中的邏輯應用程式、連線、自訂連接器和整合帳戶，以進行 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: f48106be67763c093a183be01098cab74391752e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79284196"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>在 Azure Logic Apps 中管理您的整合服務環境（ISE）

本文說明如何針對您的[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)執行管理工作，例如：

* 管理您 ISE 中的資源，例如邏輯應用程式、連線、整合帳戶和連接器。
* 檢查 ISE 的網路健全狀況。
* 新增容量、重新開機 ISE，或刪除您的 ISE，請遵循本主題中的步驟。 若要將這些成品新增至您的 ISE，請參閱[將構件新增至您的整合服務環境](../logic-apps/add-artifacts-integration-service-environment-ise.md)。

## <a name="view-your-ise"></a>查看您的 ISE

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在入口網站的搜尋方塊中，輸入「整合服務環境」，然後選取 [**整合服務環境**]。

   ![尋找整合服務環境](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. 從 [結果] 清單中，選取您的整合服務環境。

   ![選取整合服務環境](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. 繼續閱讀下一節，以尋找 ISE 中的邏輯應用程式、連線、連接器或整合帳戶。

<a name="check-network-health"></a>

## <a name="check-network-health"></a>檢查網路健全狀況

在您的 ISE 功能表的 [**設定**] 底下，選取 [**網路健康**情況]。 此窗格會顯示子網的健全狀況狀態，以及其他服務的輸出相依性。

![檢查網路健全狀況](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>管理您的邏輯應用程式

您可以查看和管理 ISE 中的邏輯應用程式。

1. 在您的 ISE 功能表的 [**設定**] 底下，選取 [**邏輯應用程式**]。

   ![查看邏輯應用程式](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. 若要移除您在 ISE 中不再需要的邏輯應用程式，請選取那些邏輯應用程式，然後選取 [**刪除**]。 若要確認您想要刪除，請選取 **[是]**。

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>管理 API 連線

您可以查看和管理在 ISE 中執行的邏輯應用程式所建立的連線。

1. 在您的 ISE 功能表的 [**設定**] 底下，選取 [ **API**連線]。

   ![查看 API 連線](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. 若要移除您在 ISE 中不再需要的連線，請選取這些連接，然後選取 [**刪除**]。 若要確認您想要刪除，請選取 **[是]**。

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>管理 ISE 連接器

您可以查看和管理部署至 ISE 的 API 連接器。

1. 在您的 ISE 功能表的 [**設定**] 底下，選取 [**受控連接器**]。

   ![查看受管理的連接器](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. 若要移除您不想在 ISE 中使用的連接器，請選取這些連接器，然後選取 [**刪除**]。 若要確認您想要刪除，請選取 **[是]**。

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>管理自訂連接器

您可以查看和管理您部署到 ISE 的自訂連接器。

1. 在您的 ISE 功能表的 [**設定**] 底下，選取 [**自訂連接器**]。

   ![尋找自訂連接器](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. 若要移除您在 ISE 中不再需要的自訂連接器，請選取這些連接器，然後選取 [**刪除**]。 若要確認您想要刪除，請選取 **[是]**。

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>管理整合帳戶

1. 在您的 ISE 功能表的 [**設定**] 下，選取 [**整合帳戶**]。

   ![尋找整合帳戶](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. 若要在不再需要的情況下從 ISE 中移除整合帳戶，請選取那些整合帳戶，然後選取 [**刪除**]。

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>新增 ISE 容量

Premium ISE 基礎單位具有固定容量，因此如果您需要更多輸送量，可以在建立期間或之後新增更多縮放單位。 開發人員 SKU 不包含新增縮放單位的功能。

1. 在[Azure 入口網站](https://portal.azure.com)中，移至您的 ISE。

1. 若要查看 ISE 的使用量和效能計量，請在 ISE 功能表上選取 **[總覽**]。

   ![查看 ISE 的使用方式](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. 在 [**設定**] 下，選取 [**相應**放大]。在 [**設定**] 窗格中，從下列選項中選取：

   * [**手動調整**](#manual-scale)：根據您要使用的處理單位數目進行調整。
   * [**自訂自動**](#custom-autoscale)調整：根據效能計量進行選擇，方法是選取各種準則，並指定符合該準則的臨界值條件。

   ![選取您想要的縮放類型](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>手動調整

1. 選取 [**手動調整**] 之後，針對 [**其他容量**]，選取您想要使用的縮放單位數。

   ![選取您想要的縮放類型](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. 完成時，選取 [儲存]****。

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>自訂自動調整

1. 在您選取 [**自訂自動**調整] 之後，針對 [**自動調整設定名稱**]，提供您的設定名稱，並選擇性地選取設定所屬的 Azure 資源群組。

   ![提供自動調整設定的名稱，然後選取 [資源群組]](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. 針對 [**預設**條件]，選取 [**根據度量調整規模**] 或 [**調整為特定實例計數**]。

   * 如果您選擇 [實例型]，請輸入處理單位的數位，這是介於0到10之間的值。

   * 如果您選擇 [以計量為基礎]，請遵循下列步驟：

     1. 在 [**規則**] 區段中，選取 [**新增規則**]。

     1. 在 [**調整規則**] 窗格上，設定規則引發時要採取的準則和動作。

     1. 針對 [**實例限制**]，請指定下列值：

        * **最小值**：要使用的處理單位數目下限
        * **最大值**：要使用的處理單位數目上限
        * **預設值**：如果在讀取資源計量時發生任何問題，而且目前的容量低於預設容量，則自動調整會相應放大為預設的處理單位數。 不過，如果目前容量超過預設容量，自動調整就不會相應縮小。

1. 若要新增另一個條件，請選取 [**新增調整條件**]。

1. 當您完成自動調整規模設定後，請儲存您的變更。

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>重新開機 ISE

如果您變更 DNS 伺服器或 DNS 伺服器設定，就必須重新開機 ISE，讓 ISE 能夠收取這些變更。 重新開機 Premium SKU ISE 不會導致停機，因為回收期間一次重新開機一個的冗余和元件。 不過，開發人員 SKU ISE 會遇到停機時間，因為沒有任何重複的複本存在。 如需詳細資訊，請參閱[ISE sku](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)。

1. 在[Azure 入口網站](https://portal.azure.com)中，移至您的 ISE。

1. 在 ISE 功能表上，選取 **[總覽**]。 在 [總覽] 工具列上，**重新開機**。

   ![重新開機整合服務環境](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>刪除 ISE

刪除您不再需要的 ISE 或包含 ISE 的 Azure 資源群組之前，請確認您在包含這些資源的 Azure 資源群組或 Azure 虛擬網路上沒有任何原則或鎖定，因為這些專案可能會封鎖刪除。

刪除 ISE 之後，您可能必須等候最多9小時，然後再嘗試刪除您的 Azure 虛擬網路或子網。

## <a name="next-steps"></a>後續步驟

* [將資源新增到整合服務環境](../logic-apps/add-artifacts-integration-service-environment-ise.md)