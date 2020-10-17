---
title: 使用 Azure IoT 中樞的大規模自動裝置管理 | Microsoft Docs
description: 使用 Azure IoT 中樞自動設定來管理多部 IoT 裝置和模組
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: e30daa3f81ed5dcae1323e721bf85cfed8fa9614
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147812"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>使用 Azure 入口網站的自動 IoT 裝置和模組管理

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT 中樞的自動裝置管理可自動為大量裝置管理許多重複且複雜工作。 使用自動裝置管理時，您可根據裝置的屬性，以特定的裝置集合作為目標來定義所需設定，並讓 IoT 中樞在這些裝置進入範圍內時更新裝置。 此更新會使用「自動裝置設定」或「自動模組設定」來完成，其可供對完成與合規性進行摘要處理、處理合併與衝突，以及階段式地推出設定。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

自動裝置管理的運作方式是以所需屬性來更新一組裝置對應項或模組對應項，並根據對應項回報的屬性來回報摘要。  自動裝置管理採用稱為「設定」的新類別和 JSON 文件，其共有三部分：

* **目標條件**會定義要更新的裝置對應項或模組對應項範圍。 目標條件會以查詢形式來指定於對應項標籤和/或回報的屬性上。

* **目標內容**會定義要在目標裝置對應項或模組對應項中新增或更新的所需屬性。 內容包含了一個路徑，連往所要變更屬性的區段。

* **計量**會定義各種設定狀態 (例如 **Success**、**Progress** 及 **Error**) 的摘要計數。 自訂計量會以查詢形式來指定於對應項回報屬性上。  系統計量是測量對應項更新狀態的預設計量，例如作為目標的對應項數目，以及成功更新的對應項數目。

自動設定會在建立設定後不久執行第一次，並在之後以五分鐘的間隔執行。 每次執行自動設定時都會執行計量查詢。

## <a name="implement-twins"></a>實作對應項

自動裝置設定需要使用裝置對應項，以同步處理雲端和裝置之間的狀態。  如需詳細資訊，請參閱[了解和使用 Azure IoT 中樞的裝置對應項](iot-hub-devguide-device-twins.md)。

自動模組設定需要使用模組對應項，以同步處理雲端和模組之間的狀態。 如需詳細資訊，請參閱[了解和使用 Azure IoT 中樞的模組對應項](iot-hub-devguide-module-twins.md)。

## <a name="use-tags-to-target-twins"></a>使用標籤將對應項設為目標

在可建立設定之前，您必須指定所要影響的裝置或模組。 Azure IoT 中樞可在裝置對應項中使用標籤來識別裝置，以及在模組對應項中使用標籤來識別模組。 每個裝置或模組都可以有多個標記，而您可利用任何對解決方案有意義的方式來定義。 例如，如果管理的裝置位於不同位置，則可將下列標籤新增至裝置對應項：

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

2. 選取 [IoT 裝置組態]。

3. 選取 [新增裝置設定] 或 [新增模組設定]。

   ![新增裝置設定或模組設定](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

建立設定有五個步驟。 下列各節將逐步解說每一個步驟。 

### <a name="name-and-label"></a>名稱與標籤

1. 為設定指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。

2. 新增標籤以協助追蹤您的設定。 標籤是可描述設定的成對「**名稱**, **值**」。 例如，`HostPlatform, Linux` 或 `Version, 3.0.1`。

3. 選取 [下一步] 以移至下一個步驟。 

### <a name="specify-settings"></a>指定設定

本章節會定義要在目標裝置對應項或模組對應項中設定的內容。 每組設定都有兩個輸入。 第一個是對應項路徑，該路徑可前往所要設定對應項屬性內的 JSON 區段。  第二個是要插入該區段的 JSON 內容。 

例如，您可將對應項路徑設定為 `properties.desired.chiller-water`，然後提供下列 JSON 內容： 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![設定對應項路徑和內容](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


您也可以藉由指定完整對應項路徑，並提供不含括弧的值來設定個別設定。 例如，使用對應項路徑 `properties.desired.chiller-water.temperature`，將內容設定為 `66`。 然後為壓力屬性建立新的對應項設定。 

如果有兩個以上的設定都以相同對應項路徑作為目標，則會套用優先順序最高設定的內容 (優先順序會在步驟 4 中定義)。

如果想要移除現有屬性，請將屬性值指定為 `null`。

您可選取 [新增裝置對應項設定] 或 [新增模組對應項設定] 來新增其他設定。

### <a name="specify-metrics-optional"></a>指定計量 (選擇性)

計量會提供在套用設定內容後，裝置或模組所回報各種狀態的摘要計數。 例如，您可以建立擱置設定變更的計量、錯誤的計量，以及成功設定變更的計量。

每個設定最多可擁有五個自訂計量。 

1. 在 [計量名稱] 輸入名稱。

2. 在 [計量準則] 輸入查詢。  查詢是根據裝置對應項報告屬性。  此計量代表查詢所傳回的資料列數目。

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

如果要建置計量來回報已設定的模組，請從 `devices.modules` 選取 `moduleId`。 例如：

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>目標裝置

使用對應項的標籤屬性，以將目標設為應該接收此設定的特定裝置或模組。 您也可以將對應項回報屬性設為目標。

自動裝置設定只能以裝置對應項標籤為目標，而自動模組設定只能以模組對應項標籤為目標。 

由於多個設定可能會以相同裝置或模組為目標，所以每個設定需具有優先順序號碼。 如果發生衝突，則會優先選擇優先順序最高的設定。 

1. 為設定的 [優先順序] 輸入一個正整數。 最高的數值會視為最高優先順序。 如果有兩個設定具有相同的優先順序號碼，則會優先選擇最新建立的設定。 

2. 輸入 [目標條件] 來判斷這個設定會將哪些裝置或模組設為目標。 條件會以對應項標籤或對應項回報屬性為基礎，且應符合運算式格式。 

   針對自動裝置設定，您可直接指定要設為目標的標籤或回報屬性。 例如，`tags.environment='test'` 或 `properties.reported.chillerProperties.model='4000x'`。 您可以指定 `*` 以將所有裝置設為目標。 
   
   針對自動模組設定，請使用查詢，從註冊於 IoT 中樞的模組來指定標籤或回報屬性。 例如，`from devices.modules where tags.environment='test'` 或 `from devices.modules where properties.reported.chillerProperties.model='4000x'`。 萬用字元不能用於為所有模組設定目標。 

3. 選取 [下一步] 移到最後一個步驟。

### <a name="review-configuration"></a>檢閱設定

檢閱您的設定資訊，然後選取 [提交]。

## <a name="monitor-a-configuration"></a>監視設定

若要檢視設定的詳細資料，並監視正在執行該設定的裝置，請使用下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的 IoT 中樞。 

2. 選取 [IoT 裝置組態]。

3. 檢查設定清單。 針對每個設定，您可以檢視下列詳細資料：

   * **識別碼**：設定的名稱。

   * **目標條件** - 用來定義目標裝置或模組的查詢。

   * **優先順序**：指派給設定的優先順序號碼。

   * **建立時間**：建立設定時的時間戳記。 當有兩個具有相同優先順序的設定時，系統會使用此時間戳記來判斷應優先選擇的設定。 

   * **系統計量**：由 IoT 中樞所計算且無法由開發人員自訂的計量。 [目標] 會指定符合目標條件的裝置對應項數目。 [套用] 會指定由設定所修改的裝置對應項數目，這在有優先順序較高的個別設定同時也做出變更的情況下，可能會包含局部修改。 

   * **自訂計量** - 由開發人員針對對應項回報屬性指定為查詢的計量。  每個設定最多可定義五個自訂計量。 
   
4. 選取您想要監視的設定。  

5. 檢查設定詳細資料。 針對接收設定的裝置，您可以使用索引標籤來檢視裝置的特定詳細資料。

   * **目標條件** - 符合目標條件的裝置或模組。 

   * **計量**：系統計量和自訂計量的清單。  您可選取下拉式清單中的計量，然後選取 [檢視裝置] 或 [檢視模組] 來檢視每個計量所計算裝置或模組的清單。

   * **裝置對應項設定**或**模組對應項設定** - 由設定所設定的對應項設定。 

   * **設定標籤**：用來描述設定的機碼值組。  標籤對功能沒有任何影響。 

## <a name="modify-a-configuration"></a>修改設定

當修改設定時，所做的變更會立即複寫到所有目標裝置或模組。 

如果您更新目標條件，就會發生下列更新：

* 如果對應項不符合舊的目標條件，但符合新的目標條件，且此設定為該對應項的最高優先順序設定，則會套用此設定。 

* 如果目前正在執行此設定的對應項不再符合目標條件，則會移除設定中的設定，並以優先順序次高的設定來修改該對應項。 

* 如果目前執行此設定的對應項不再符合目標條件，也不符合任何其他設定的目標條件，則系統會將設定中的設定移除，且不會對該對應項進行任何其他變更。 

若要修改設定，請使用下列步驟： 

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的 IoT 中樞。 

2. 選取 [IoT 裝置組態]。 

3. 選取您想要修改的設定。 

4. 對下列欄位進行更新： 

   * 目標條件 
   * 標籤 
   * 優先順序 
   * 計量

4. 選取 [儲存]。

5. 依照[監視設定](#monitor-a-configuration)中的步驟來監看所推出的變更。 

## <a name="delete-a-configuration"></a>刪除設定

當您刪除設定時，任何裝置對應項都會採用其優先順序次高的設定。 如果裝置對應項不符合任何其他設定的目標條件，則不會套用任何其他設定。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的 IoT 中樞。 

2. 選取 [IoT 裝置組態]。 

3. 使用核取方塊來選取您想要刪除的設定。 

4. 選取 [刪除] 。

5. 您會看到要求確認的提示。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何大規模設定和監視 IoT 裝置。 遵循下列連結以深入了解如何管理 Azure IoT 中樞：

* [管理大量的 IoT 中樞裝置身分識別](iot-hub-bulk-identity-mgmt.md)
* [IoT 中樞計量](iot-hub-metrics.md)
* [作業監視](iot-hub-operations-monitoring.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/quickstart-linux.md)

若要探索使用 IoT 中樞裝置佈建服務進行 Just-In-Time 自動佈建，請參閱： 

* [Azure IoT 中樞裝置佈建服務](../iot-dps/index.yml)