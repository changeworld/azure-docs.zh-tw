---
title: 在 Azure 入口網站 Azure IoT Edge 中大規模部署模組
description: 使用 Azure 入口網站為 IoT Edge 裝置群組建立自動部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9d03b6f4a512c22564480405ec0f0e0c0e62a958
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048418"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>使用 Azure 入口網站大規模部署 IoT Edge 模組

在 Azure 入口網站中建立 **IoT Edge 自動部署** ，以同時管理許多裝置的進行中部署。 IoT Edge 的自動部署是 IoT 中樞[自動裝置管理](../iot-hub/iot-hub-automatic-device-management.md)功能的一部分。 部署是動態程序，可讓您將多個模組部署到多個裝置、追蹤模組的狀態和健康情況，並視需要進行變更。

如需詳細資訊，請參閱[了解單一裝置或大規模的 IoT Edge 自動部署](module-deployment-monitoring.md)。

## <a name="identify-devices-using-tags"></a>使用標記識別裝置

在您可建立部署之前，必須能夠指定您要影響的裝置。 Azure IoT Edge 會使用裝置對應項中的 **tags** 來識別裝置。 每部裝置都可以有多個您利用任何對您解決方案有意義的方式定義的標記。

例如，如果您管理智慧建築的校園，您可能會將位置、空間類型和環境標籤新增至裝置：

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

如需裝置對應項和標記的詳細資訊，請參閱[了解和使用 IoT 中樞的裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)。

## <a name="create-a-deployment"></a>建立部署

IoT Edge 提供兩種不同類型的自動部署，您可以用來自訂您的案例。 您可以建立標準 *部署*，其中包括系統執行時間模組和任何其他模組和路由。 每個裝置只能套用一個部署。 或者，您可以建立僅包含自訂模組和路由的 *分層式部署*，而不是系統執行時間。 許多分層式部署都可以在一個裝置上結合在標準部署之上。 如需這兩種自動部署類型如何一起運作的詳細資訊，請參閱 [瞭解單一裝置或大規模的 IoT Edge 自動部署](module-deployment-monitoring.md)。

建立部署和分層部署的步驟非常類似。 在下列步驟中，會呼叫任何差異。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的 IoT 中樞。
1. 在左窗格的功能表上，選取 [**自動裝置管理**] 下的**IoT Edge** 。
1. 在上方列中，選取 [ **建立部署** ] 或 [ **建立分層式部署**]。

建立部署有五個步驟。 下列各節將逐步解說每一個步驟。

>[!NOTE]
>本文中的步驟反映 IoT Edge 代理程式和中樞的最新架構版本。 架構版本1.1 與 IoT Edge 1.0.10 版本一起發行，並啟用模組啟動順序和路由優先順序功能。
>
>如果您要部署至執行1.0.9 或更早版本的裝置，請在嚮導的 [**模組**] 步驟中編輯**執行時間設定**，以使用架構版本1.0。

### <a name="step-1-name-and-label"></a>步驟1：名稱和標籤

1. 為部署指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。
1. 您可以新增機碼值組形式的標籤，以協助追蹤部署。 例如，**HostPlatform** 和 **Linux**，或 **Version** 和 **3.0.1**。
1. 選取 **[下一步]：** 要移至步驟2的模組。

### <a name="step-2-modules"></a>步驟2：模組

您最多可以將50個模組新增至部署。 如果您建立不含模組的部署，它會從目標裝置移除任何目前的模組。

在部署中，您可以管理 IoT Edge 代理程式和 IoT Edge 中樞模組的設定。 選取 [ **執行時間設定** ] 以設定兩個執行時間模組。 在多層式部署中，不會包含執行時間模組，因此無法設定。

若要新增自訂程式碼作為模組，或手動新增 Azure 服務模組，請遵循下列步驟：

1. 在頁面的 [ **容器登錄設定** ] 區段中，提供認證以存取任何包含您模組映射的私人容器登錄。
1. 在頁面的 [ **IoT Edge 模組** ] 區段中，選取 [ **新增**]。
1. 從下拉式功能表中選擇三種類型的模組之一：

   * **IoT Edge 模組** -您提供模組名稱和容器映射 URI。 例如，範例 SimulatedTemperatureSensor 模組的映射 URI 為 `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` 。 如果模組映射儲存在私人容器登錄中，請在此頁面上新增認證以存取映射。
   * **Marketplace 模組** -主控于 Azure Marketplace 中的模組。 某些 marketplace 模組需要額外的設定，因此請參閱 [Azure Marketplace IoT Edge 模組](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 清單中的模組詳細資料。
   * **Azure 串流分析模組** -從 Azure 串流分析工作負載產生的模組。

1. 如有需要，請重複步驟2和3，將其他模組新增至您的部署。

將模組新增至部署之後，您可以選取其名稱，以開啟 [ **更新 IoT Edge 模組** ] 頁面。 在此頁面上，您可以編輯模組設定、環境變數、建立選項、啟動順序和模組對應項。 如果您已從 marketplace 新增模組，則可能已經填入其中一些參數。 如需可用模組設定的詳細資訊，請參閱 [模組設定和管理](module-composition.md#module-configuration-and-management)。

如果您要建立多層式部署，則可能會設定存在於以相同裝置為目標的其他部署中的模組。 若要更新模組對應項而不覆寫其他版本，請開啟 [模組對應項 **設定** ] 索引標籤。使用模組對應項所需屬性內子區段的唯一名稱來建立新的模組對應項 **屬性** ，例如 `properties.desired.settings` 。 如果您只在欄位中定義屬性 `properties.desired` ，它會覆寫任何較低優先順序部署中所定義之模組的所需屬性。

![設定多層式部署的模組對應項屬性](./media/how-to-deploy-monitor/module-twin-property.png)

如需多層式部署中模組對應項設定的詳細資訊，請參閱 [分層式部署](module-deployment-monitoring.md#layered-deployment)。

設定好部署的所有模組之後，請選取 **[下一步：** 移至步驟3的路由]。

### <a name="step-3-routes"></a>步驟3：路由

在 [路由] 索引標籤上，請定義要用來在模組與 IoT 中樞之間傳遞訊息的方式。 訊息會使用名稱/值組來構成。

例如，具有名稱 **路由** 的路由以及 **從/messages/ \* 到 $upstream** 的值，都會取得任何模組輸出的任何訊息，並將其傳送至您的 IoT 中樞。  

**Priority**和**Time to live**參數是選擇性參數，您可以將它們包含在路由定義中。 Priority 參數可讓您選擇應先處理其訊息的路由，或最後處理的路由。 優先順序是藉由設定數位0-9 來決定，其中0是最高優先順序。 [存留時間] 參數可讓您宣告該路由中的訊息在處理或從佇列中移除之前，應保留的時間長度。

如需有關如何建立路由的詳細資訊，請參閱宣告 [路由](module-composition.md#declare-routes)。

選取 **[下一步：計量]**。

### <a name="step-4-metrics"></a>步驟4：計量

計量能提供在套用設定內容後，裝置所回報各種狀態的摘要計數。

1. 在 [計量名稱] 輸入名稱。

1. 在 [計量準則] 輸入查詢。 查詢會以 IoT Edge 中樞模組對應項[所報告的屬性](module-edgeagent-edgehub.md#edgehub-reported-properties)作為基礎。 此計量代表查詢所傳回的資料列數目。

   例如：

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

選取 **[下一步：目標裝置]**。

### <a name="step-5-target-devices"></a>步驟5：目標裝置

使用裝置的 tags 屬性，將目標設為應該接收此部署的特定裝置。

由於多個部署可能會以相同裝置為目標，因此，您應該為每個部署提供優先順序號碼。 如果發生衝突，優先順序最高的部署 (較大的值表示) 獲勝的優先順序較高。 如果兩個部署具有相同的優先順序號碼，則最新建立的部署獲勝。

如果有多個部署以相同裝置為目標，則只會套用優先順序較高的應用程式。 如果多個分層部署以相同裝置為目標，則會套用它們。 但是，如果有重複的任何屬性（例如，如果有兩個相同名稱的路由），則較高優先順序的分層部署中的一個會覆寫其餘部分。

以裝置為目標的任何分層部署都必須具有比基礎部署更高的優先順序，才能套用。

1. 為部署**優先順序**輸入一個正整數。
1. 輸入**目標條件**來判斷這個部署會將哪些裝置設為目標。 條件會以裝置對應項標籤或裝置對應項報告屬性為基礎，且應符合運算式格式。例如，`tags.environment='test'` 或 `properties.reported.devicemodel='4000x'`。

選取 **[下一步]： [檢查 + 建立]** 以繼續進行最後一個步驟。

### <a name="step-6-review-and-create"></a>步驟6：審查和建立

檢查您的部署資訊，然後選取 [ **建立**]。

若要監視您的部署，請參閱 [監視 IoT Edge 部署](how-to-monitor-iot-edge-deployments.md)。

## <a name="modify-a-deployment"></a>修改部署

當您修改部署時，所做的變更會立即複寫到所有目標裝置。 您可以針對現有的部署修改下列設定和功能：

* 目標條件
* 自訂度量
* 標籤
* 標籤
* 所需屬性

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>修改目標條件、自訂計量和標籤

1. 在 IoT 中樞中，從左窗格功能表中選取 [ **IoT Edge** ]。
1. 選取 [ **IoT Edge 部署** ] 索引標籤，然後選取您要設定的部署。
1. 選取 [ **目標條件** ] 索引標籤。將 **目標條件** 變更為目標裝置。 您也可以調整 **優先順序**。  選取 [儲存]。

    如果您更新目標條件，就會發生下列更新：

    * 如果裝置不符合舊的目標條件，但符合新的目標條件，而且此部署為該裝置的最高優先順序，則會將此部署套用到裝置。
    * 如果目前執行此部署的裝置不再符合目標條件，它會解除安裝此部署，並採用下一個最高優先順序的部署。
    * 如果目前執行此部署的裝置不再符合目標條件，且不符合任何其他部署的目標條件，則不會在裝置上發生任何變更。 裝置會以模組目前的狀態繼續執行它目前的模組，但不再將其當成此部署的一部分來管理。 一旦它符合任何其他部署的目標條件之後，就會解除安裝此部署，並採用新的部署。

1. 選取 [ **計量** ] 索引標籤，然後按一下 [ **編輯計量** ] 按鈕。 使用範例語法作為指南，以新增或修改自訂計量。 選取 [儲存]。

    ![編輯部署中的自訂計量](./media/how-to-deploy-monitor/metric-list.png)

1. 選取 [ **標籤] 索引標籤** 並進行所需的變更，然後選取 [ **儲存**]。

## <a name="delete-a-deployment"></a>刪除部署

當您刪除部署時，任何已部署的裝置都會採用其下一個最高優先順序的部署。 如果您的裝置不符合任何其他部署的目標條件，則在刪除部署時不會移除模組。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 選取 [IoT Edge]****。
1. 選取 [ **IoT Edge 部署** ] 索引標籤。

   ![檢視 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 使用核取方塊來選取您想要刪除的部署。
1. 選取 [刪除]。
1. 提示將會通知您，這個動作將刪除此部署，並將所有裝置還原成先前狀態。將會套用優先順序較低的部署。如果沒有將其他部署設為目標，將不會移除任何模組。 如果您想要從裝置中移除所有模組，請建立不含模組的部署，並將其部署至相同的裝置。選取 [是] 以繼續。

## <a name="next-steps"></a>後續步驟

深入了解如何[將模組部署到 IoT Edge 裝置](module-deployment-monitoring.md)。