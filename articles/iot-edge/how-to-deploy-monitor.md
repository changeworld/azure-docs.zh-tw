---
title: 在 Azure 入口網站 Azure IoT Edge 中大規模部署模組
description: 使用 Azure 入口網站為 IoT Edge 裝置群組建立自動部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271430"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>使用 Azure 入口網站大規模部署和監視 IoT Edge 模組

在 Azure 入口網站中建立**IoT Edge 自動部署**，以一次管理許多裝置的持續部署。 IoT Edge 的自動部署是 IoT 中樞[自動裝置管理](/azure/iot-hub/iot-hub-automatic-device-management)功能的一部分。 部署是動態程式，可讓您將多個模組部署至多個裝置、追蹤模組的狀態和健全狀況，並在必要時進行變更。

如需詳細資訊，請參閱[瞭解單一裝置或大規模的 IoT Edge 自動部署](module-deployment-monitoring.md)。

## <a name="identify-devices-using-tags"></a>使用標記識別裝置

在您可建立部署之前，必須能夠指定您要影響的裝置。 Azure IoT Edge 會使用裝置對應項中的 **tags** 來識別裝置。 每個裝置都可以有多個以任何方式定義的標記，對您的解決方案而言是合理的。

例如，如果您管理智慧建築的校園，您可以將位置、房間類型和環境標籤新增至裝置：

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

IoT Edge 提供兩種不同類型的自動部署，您可以用來自訂您的案例。 您可以建立標準*部署*，其中包括系統執行時間模組和任何其他模組和路由。 每個裝置只能套用一個部署。 或者，您可以建立*分層部署*，其中只包含自訂模組和路由，而非系統執行時間。 許多層部署都可以結合在一部裝置上，以標準部署為基礎。 如需兩種自動部署類型如何搭配使用的詳細資訊，請參閱[瞭解單一裝置或大規模的 IoT Edge 自動部署](module-deployment-monitoring.md)。

建立部署和分層部署的步驟非常類似。 在下列步驟中，會呼叫任何差異。

1. 在  [Azure 入口網站](https://portal.azure.com)中，移至您的 IoT 中樞。
1. 在左窗格的功能表上，選取 [**自動裝置管理**] 下的 [ **IoT Edge** ]。
1. 在上方列中，選取 [**建立部署**] 或 [**建立分層部署**]。

建立部署有五個步驟。 下列各節將逐步解說每一個步驟。

### <a name="step-1-name-and-label"></a>步驟1：名稱和標籤

1. 為部署指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。
1. 您可以新增機碼值組形式的標籤，以協助追蹤部署。 例如，**HostPlatform** 和 **Linux**，或 **Version** 和 **3.0.1**。
1. 選取 **[下一步]：** 要移至步驟二的模組。

### <a name="step-2-modules"></a>步驟2：模組

您最多可以在部署中新增20個模組。 如果您建立不含模組的部署，它會從目標裝置移除任何目前的模組。

在 [部署] 中，您可以管理 IoT Edge 代理程式和 IoT Edge 中樞模組的設定。 選取 [**執行時間設定**] 來設定這兩個執行時間模組。 在分層部署中，不會包含執行時間模組，因此無法設定。

您可以新增三種類型的模組：

* IoT Edge 模組
* Marketplace 模組
* Azure 串流分析模組

#### <a name="add-an-iot-edge-module"></a>新增 IoT Edge 模組

若要新增自訂程式碼作為模組，或手動新增 Azure 服務模組，請遵循下列步驟：

1. 在頁面的 [**容器登錄認證**] 區段中，為包含此部署之模組映射的任何私人容器登錄提供名稱和認證。 如果 IoT Edge 代理程式找不到 Docker 映射的 container registry 認證，將會報告錯誤500。
1. 在頁面的 [IoT Edge 模組] 區段中，按一下 [新增]。
1. 從下拉式功能表中選取 [ **IoT Edge 模組**]。
1. 為模組提供**IoT Edge 的模組名稱**。
1. 針對 [映像 URI] 欄位，為您的模組輸入容器映像。
1. 使用下拉式功能表來選取**重新啟動原則**。 從下列選項中選擇：
   * **always** -如果模組因任何原因而關閉，一律會重新開機。
   * **永不**-如果模組因任何原因而關閉，永遠不會重新開機。
   * **失敗**時-模組會在損毀時重新開機，但如果它完全關閉則不會。
   * **狀況不良**-模組會在損毀或傳回狀況不良狀態時重新開機。 每個模組必須負責實作健康情況狀態函式。
1. 使用下拉功能表來選取模組的**預期狀態**。 從下列選項中選擇：
   * [執行中] 是預設選項。 模組將會在部署後立即開始執行。
   * **已停止**-部署之後，模組將會維持閒置，直到您或另一個模組開始呼叫為止。
1. 指定任何應傳遞至容器的**容器建立選項**。 如需詳細資訊，請參閱 [docker create](https://docs.docker.com/engine/reference/commandline/create/)。
1. 如果您想要將標記或其他屬性新增至模組對應項，請選取 [模組對應項**設定**]。
1. 輸入此模組的 [環境變數]。 環境變數會將設定資訊提供給模組。
1. 選取 [**新增**]，將您的模組新增至部署。

#### <a name="add-a-module-from-the-marketplace"></a>從 Marketplace 新增模組

若要從 Azure Marketplace 新增模組，請遵循下列步驟：

1. 在頁面的 [IoT Edge 模組] 區段中，按一下 [新增]。
1. 從下拉式功能表中選取 [ **Marketplace 模組**]。
1. 從**IoT Edge 模組 Marketplace**  頁面中選擇模組。 您選取的模組會針對您的訂用帳戶、資源群組和裝置自動設定。 然後它會出現在您的 IoT Edge 模組清單中。 有些模組可能需要額外的設定。 如需詳細資訊，請參閱[從 Azure Marketplace 部署模組](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)。

#### <a name="add-a-stream-analytics-module"></a>新增串流分析模組

若要從 Azure 串流分析新增模組，請遵循下列步驟：

1. 在頁面的 [IoT Edge 模組] 區段中，按一下 [新增]。
1. 從下拉式功能表中選取 [ **Azure 串流分析模組**]。
1. 在右窗格中，選擇您的**訂**用帳戶。
1. 選擇您的 IoT **Edge 作業**。
1. 選取 [儲存]，將您的模組新增至部署。

#### <a name="configure-module-settings"></a>設定模組設定

將模組新增至部署之後，您可以選取其名稱，以開啟 [**更新 IoT Edge 模組**] 頁面。 在此頁面上，您可以編輯 [模組設定]、[環境變數]、[建立選項] 和 [模組對應項]。 如果您已從 marketplace 新增模組，它可能已經填入其中一些參數。

如果您要建立多層式部署，您可能會在其他部署中設定以相同裝置為目標的模組。 若要更新模組對應項而不覆寫其他版本，請開啟 [模組對應項**設定**] 索引標籤。在模組對應項的所需屬性中，建立具有子區段唯一名稱的新模組對應項**屬性**，例如 `properties.desired.settings`。 如果您只在 [`properties.desired`] 欄位中定義屬性，它會覆寫任何較低優先順序部署中所定義之模組的所需屬性。

![設定多層式部署的模組對應項屬性](./media/how-to-deploy-monitor/module-twin-property.png)

如需分層部署中模組對應項設定的詳細資訊，請參閱[分層部署](module-deployment-monitoring.md#layered-deployment)。

設定好部署的所有模組之後，請選取 **[下一步：路由]** 移至步驟3。

### <a name="step-3-routes"></a>步驟3：路由

路由會定義模組在部署內彼此通訊的方式。 根據預設，此 wizard 會提供一個稱為**上游**的路由，並定義為**從/messages/\* 至 $upstream**，這表示任何模組的任何訊息輸出都會傳送到您的 IoT 中樞。  

請使用[宣告路由](module-composition.md#declare-routes)中的資訊來新增或更新路由，然後選取 [下一步] 繼續檢閱區段。

選取 **[下一步：計量]** 。

### <a name="step-4-metrics"></a>步驟4：計量

計量能提供在套用設定內容後，裝置所回報各種狀態的摘要計數。

1. 在 [計量名稱] 輸入名稱。

1. 在 [計量準則] 輸入查詢。 查詢會以 IoT Edge 中樞模組對應項[所報告的屬性](module-edgeagent-edgehub.md#edgehub-reported-properties)作為基礎。 此計量代表查詢所傳回的資料列數目。

   例如：

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

選取 **[下一步：目標裝置]** 。

### <a name="step-5-target-devices"></a>步驟5：目標裝置

使用裝置的 tags 屬性，將目標設為應該接收此部署的特定裝置。

由於多個部署可能會以相同裝置為目標，因此，您應該為每個部署提供優先順序號碼。 如果發生衝突，最高優先順序的部署（較大的值表示較高的優先順序）獲勝。 如果兩個部署具有相同的優先順序號碼，則最新建立的部署獲勝。

如果多個部署的目標為相同的裝置，則只會套用優先順序較高的應用。 如果多層式部署的目標為相同的裝置，則會全部套用。 不過，如果有任何屬性重複，例如，如果有兩個相同名稱的路由，則來自較高優先順序分層部署的一個會覆寫其餘部分。

以裝置為目標的任何分層部署都必須具有高於基底部署的優先順序，才能套用。

1. 為部署**優先順序**輸入一個正整數。
1. 輸入**目標條件**來判斷這個部署會將哪些裝置設為目標。 條件是以裝置對應項標記或裝置對應項報告屬性為基礎，且應符合運算式格式。 例如，`tags.environment='test'` 或 `properties.reported.devicemodel='4000x'`。

選取 **[下一步：審核 + 建立]** 以繼續進行最後一個步驟。

### <a name="step-6-review-and-create"></a>步驟6：審查和建立

檢查您的部署資訊，然後選取 [**建立**]。

## <a name="monitor-a-deployment"></a>監視部署

若要檢視部署的詳細資料並監視正在執行該部署的裝置，請使用下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 選取 [IoT Edge]。
1. 選取 [ **IoT Edge 部署**] 索引標籤。

   ![檢視 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 檢查部署清單。 針對每個部署，您可以查看下列詳細資料：
   * **識別碼**：部署的名稱。
   * **類型**-部署的類型，也就是**部署**或**分層部署**。
   * **目標條件**-用來定義目標裝置的標記。
   * **優先順序**：指派給部署的優先順序號碼。
   * **系統計量** - [設為目標] 會指定 IoT 中樞內符合目標條件的裝置對應項數目，而 [以套用] 則會指定已在 IoT 中樞內將部署內容套用到至模組對應項的裝置數目。
   * **裝置計量**-從 IoT Edge 用戶端執行時間報告成功或錯誤的部署中的 IoT Edge 裝置數目。
   * **自訂計量**-部署中針對您為部署定義的任何計量報告資料的 IoT Edge 裝置數目。
   * **建立時間**-建立部署時的時間戳記。 當兩個部署具有相同的優先順序時，可使用此時間戳記來中斷繫結。
1. 選取您想要監視的部署。  
1. 檢查部署詳細資料。 您可以使用索引標籤來檢閱部署的詳細資料。

## <a name="modify-a-deployment"></a>修改部署

當您修改部署時，所做的變更會立即複寫到所有目標裝置。

如果您更新目標條件，就會發生下列更新：

* 如果裝置不符合舊的目標條件，但符合新的目標條件，而且此部署為該裝置的最高優先順序，則會將此部署套用到裝置。
* 如果目前執行此部署的裝置不再符合目標條件，它會解除安裝此部署，並採用下一個最高優先順序的部署。
* 如果目前執行此部署的裝置不再符合目標條件，且不符合任何其他部署的目標條件，則不會在裝置上發生任何變更。 裝置會以模組目前的狀態繼續執行它目前的模組，但不再將其當成此部署的一部分來管理。 一旦它符合任何其他部署的目標條件之後，就會解除安裝此部署，並採用新的部署。

若要修改部署，請使用下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 選取 [IoT Edge]。
1. 選取 [ **IoT Edge 部署**] 索引標籤。

   ![檢視 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 選取您想要修改的部署。
1. 在下列索引標籤上進行更新：
   * **目標條件**
   * **計量**-您可以修改或刪除已定義的度量，或加入新的計量。
   * **標籤**
   * **模組**
   * **路由**
   * **部署**

1. 選取 [儲存]。
1. 依照[監視部署](#monitor-a-deployment)中的步驟監看變更推出的情形。

## <a name="delete-a-deployment"></a>刪除部署

當您刪除部署時，任何已部署的裝置都會採用其下一個最高優先順序的部署。 如果您的裝置不符合任何其他部署的目標條件，則在刪除部署時不會移除模組。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 選取 [IoT Edge]。
1. 選取 [ **IoT Edge 部署**] 索引標籤。

   ![檢視 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 使用核取方塊來選取您想要刪除的部署。
1. 選取 [刪除]。
1. 提示將會通知您，這個動作將刪除此部署，並將所有裝置還原成先前狀態。  這表示會套用優先順序較低的部署。 如果沒有其他部署的目標，將不會移除任何模組。 如果您想要從裝置中移除所有模組，請建立不含模組的部署，並將其部署至相同的裝置。 選取 **[是]** 以繼續。

## <a name="next-steps"></a>後續步驟

深入瞭解如何[將模組部署到 IoT Edge 裝置](module-deployment-monitoring.md)。
