---
title: 使用 Azure IoT 中樞進行大規模的自動裝置管理 |Microsoft Docs
description: 使用 Azure IoT 中樞自動設定來管理多個 IoT 裝置和模組
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 276f115f579fbd1ab077722b220a4a0c6c571850
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025062"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>使用 Azure 入口網站自動進行 IoT 裝置和模組管理

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT 中樞中的自動裝置管理，可將管理大型裝置機群的許多重複且複雜的工作自動化。 使用自動裝置管理時，您可以根據裝置的內容，定義所需的設定，然後讓 IoT 中樞在裝置進入範圍時進行更新。 此更新是使用_自動裝置_設定或_自動模組_設定來完成，這可讓您摘要完成和合規性、處理合併和衝突，以及以階段式方法推出設定。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

自動裝置管理的運作方式是使用所需的屬性來更新一組裝置 twins 或模組 twins，並根據對應項報告屬性來報告摘要。  它引進了名為設定的新類別和 JSON 檔 *，其中包含*三個部分：

* **目標條件**會定義要更新之裝置 twins 或模組 twins 的範圍。 目標條件會指定為對應項標記和（或）報告屬性的查詢。

* **目標內容**會定義要在目標裝置 twins 或模組 twins 中新增或更新的所需屬性。 內容包含了一個路徑，連往所要變更屬性的區段。

* **計量**會定義各種設定狀態 (例如 **Success**、**Progress** 及 **Error**) 的摘要計數。 自訂計量會指定為對應項報告屬性上的查詢。  系統計量是測量對應項更新狀態的預設度量，例如目標的 twins 數目，以及已成功更新的 twins 數目。

第一次在建立設定之後，以及每隔五分鐘的間隔執行自動設定。 每次執行自動設定時，都會執行計量查詢。

## <a name="implement-twins"></a>執行 twins

自動裝置設定需要使用裝置對應項，以同步處理雲端和裝置之間的狀態。  如需詳細資訊，請參閱[瞭解及使用 IoT 中樞中的裝置 twins](iot-hub-devguide-device-twins.md)。

自動模組設定需要使用模組 twins 來同步處理雲端與模組之間的狀態。 如需詳細資訊，請參閱[瞭解及使用 IoT 中樞中的模組 twins](iot-hub-devguide-module-twins.md)。

## <a name="use-tags-to-target-twins"></a>使用標籤來以 twins 為目標

建立設定之前，您必須指定想要影響的裝置或模組。 Azure IoT 中樞可識別裝置，並在裝置對應項中使用標記，並使用模組對應項中的標籤來識別模組。 每個裝置或模組都可以有多個標籤，而您可以用任何對解決方案有意義的方式來定義它們。 例如，如果您管理不同位置的裝置，請將下列標記新增至裝置對應項：

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>建立設定

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的 IoT 中樞。 

2. 選取 [IoT 裝置組態]****。

3. 選取 [**新增裝置**設定] 或 [**新增模組**設定]。

   ![新增裝置設定或模組設定](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

建立設定有五個步驟。 下列各節將逐步解說每一個步驟。 

### <a name="name-and-label"></a>名稱與標籤

1. 為設定指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。

2. 新增標籤以協助追蹤您的設定。 標籤是可描述設定的成對「**名稱**, **值**」。 例如，`HostPlatform, Linux` 或 `Version, 3.0.1`。

3. 選取 [下一步]**** 以移至下一個步驟。 

### <a name="specify-settings"></a>指定設定

此區段會定義要在目標裝置或模組 twins 中設定的內容。 每組設定都有兩個輸入。 第一個是對應項路徑，這是將設定之對應項所需屬性內 JSON 區段的路徑。  第二個是要插入該區段的 JSON 內容。 

例如，您可以將對應項路徑設定為`properties.desired.chiller-water` ，然後提供下列 JSON 內容： 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![設定對應項路徑和內容](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


您也可以藉由指定整個對應項路徑，並提供不含括弧的值，來設定個別設定。 例如，使用對應項路徑`properties.desired.chiller-water.temperature`，將內容設定為。 `66` 然後為 [壓力] 屬性建立新的對應項設定。 

如果有兩個或多個設定的目標為相同的對應項路徑，則會套用最高優先順序設定的內容（優先順序定義于步驟4）。

如果您想要移除現有的屬性，請將屬性值指定`null`為。

您可以選取 [**新增裝置**對應項設定] 或 [**新增模組**對應項設定] 來新增其他設定。

### <a name="specify-metrics-optional"></a>指定計量 (選擇性)

計量會提供裝置或模組在套用設定內容之後可能回報的各種狀態的摘要計數。 例如，您可以建立擱置設定變更的計量、錯誤的計量，以及成功設定變更的計量。

每個設定最多可有五個自訂計量。 

1. 輸入 [**度量名稱**] 的名稱。

2. 在 [計量準則]**** 輸入查詢。  查詢是根據裝置對應項報告屬性。  此計量代表查詢所傳回的資料列數目。

例如：

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

您可以加入已套用設定的子句，例如： 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

如果您要建立度量來報告已設定的模組，請`moduleId`選取`devices.modules`[從]。 例如：

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>目標裝置

使用 twins 中的 tags 屬性，將目標設為應該接收此設定的特定裝置或模組。 您也可以將對應項報告屬性設為目標。

自動裝置設定只能以裝置對應項標記為目標，而自動模組設定只能以模組對應項標記為目標。 

因為多個設定可能以相同的裝置或模組為目標，所以每個設定都需要優先順序號碼。 如果發生衝突，則會優先選擇優先順序最高的設定。 

1. 為設定的 [優先順序]**** 輸入一個正整數。 最高的數值會視為最高優先順序。 如果有兩個設定具有相同的優先順序號碼，則會優先選擇最新建立的設定。 

2. 輸入**目標條件**，以決定將以這種設定為目標的裝置或模組。 條件是以對應項標記或對應項所報告的屬性為基礎，且應符合運算式格式。 

   針對自動裝置設定，您可以只指定要設為目標的標記或報告屬性。 例如，`tags.environment='test'` 或 `properties.reported.chillerProperties.model='4000x'`。 您可以指定 `*` 以將所有裝置設為目標。 
   
   針對自動模組設定，請使用查詢，從向 IoT 中樞註冊的模組指定標記或報告屬性。 例如，`from devices.modules where tags.environment='test'` 或 `from devices.modules where properties.reported.chillerProperties.model='4000x'`。 萬用字元不能用來以所有模組為目標。 

3. 選取 [下一步]**** 移到最後一個步驟。

### <a name="review-configuration"></a>檢閱設定

檢閱您的設定資訊，然後選取 [提交]****。

## <a name="monitor-a-configuration"></a>監視設定

若要檢視設定的詳細資料，並監視正在執行該設定的裝置，請使用下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的 IoT 中樞。 

2. 選取 [IoT 裝置組態]****。

3. 檢查設定清單。 針對每個設定，您可以檢視下列詳細資料：

   * **識別碼**：設定的名稱。

   * **目標條件**-用來定義目標裝置或模組的查詢。

   * **優先順序**：指派給設定的優先順序號碼。

   * **建立時間**：建立設定時的時間戳記。 當有兩個具有相同優先順序的設定時，系統會使用此時間戳記來判斷應優先選擇的設定。 

   * **系統計量**：由 IoT 中樞所計算且無法由開發人員自訂的計量。 [目標] 會指定符合目標條件的裝置對應項數目。 [套用] 會指定由設定所修改的裝置對應項數目，這在有優先順序較高的個別設定同時也做出變更的情況下，可能會包含局部修改。 

   * **自訂計量**-由開發人員指定的度量，做為對應項所報告屬性的查詢。  每個設定最多可定義五個自訂計量。 
   
4. 選取您想要監視的設定。  

5. 檢查設定詳細資料。 針對接收設定的裝置，您可以使用索引標籤來檢視裝置的特定詳細資料。

   * **目標條件**-符合目標條件的裝置或模組。 

   * **計量**：系統計量和自訂計量的清單。  您可以藉由在下拉式清單中選取計量，然後選取 [**查看裝置**] 或 [**查看模組**]，來查看為每個計量計算的裝置或模組清單。

   * **裝置**對應項設定或**模組**對應項設定-設定所設定的對應項設定。 

   * **設定標籤**：用來描述設定的機碼值組。  標籤對功能沒有任何影響。 

## <a name="modify-a-configuration"></a>修改設定

當您修改設定時，變更會立即複寫到所有目標裝置或模組。 

如果您更新目標條件，就會發生下列更新：

* 如果對應項不符合舊的目標條件，但符合新的目標條件，而此設定是該對應項的最高優先順序，則會套用此設定。 

* 如果目前正在執行此設定的對應項不再符合目標條件，則會移除設定中的設定，並由下一個最高優先順序的設定來修改對應項。 

* 如果目前執行此設定的對應項不再符合目標條件，且不符合任何其他設定的目標條件，則會移除設定中的設定，而且不會在對應項上進行其他變更。 

若要修改設定，請使用下列步驟： 

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的 IoT 中樞。 

2. 選取 [IoT 裝置組態]****。 

3. 選取您想要修改的設定。 

4. 對下列欄位進行更新： 

   * 目標條件 
   * 標籤 
   * 優先順序 
   * 計量

4. 選取 [儲存]  。

5. 依照[監視設定](#monitor-a-configuration)中的步驟來監看所推出的變更。 

## <a name="delete-a-configuration"></a>刪除設定

當您刪除設定時，任何裝置對應項都會採用其優先順序次高的設定。 如果裝置對應項不符合任何其他設定的目標條件，則不會套用任何其他設定。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的 IoT 中樞。 

2. 選取 [IoT 裝置組態]****。 

3. 使用核取方塊來選取您想要刪除的設定。 

4. 選取 [刪除]  。

5. 您會看到要求確認的提示。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何大規模設定和監視 IoT 裝置。 遵循下列連結以深入了解如何管理 Azure IoT 中樞：

* [管理大量的 IoT 中樞裝置身分識別](iot-hub-bulk-identity-mgmt.md)
* [IoT 中樞度量](iot-hub-metrics.md)
* [作業監視](iot-hub-operations-monitoring.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/tutorial-simulate-device-linux.md)

若要探索使用 IoT 中樞裝置佈建服務進行 Just-In-Time 自動佈建，請參閱： 

* [Azure IoT 中樞裝置佈建服務](/azure/iot-dps)
