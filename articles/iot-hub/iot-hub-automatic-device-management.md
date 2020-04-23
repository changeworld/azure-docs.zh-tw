---
title: 使用 Azure IoT 中心大規模自動裝置管理 |微軟文件
description: 使用 Azure IoT 中心自動設定管理多個 IoT 裝置和模組
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 276f115f579fbd1ab077722b220a4a0c6c571850
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025062"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>使用 Azure 門戶自動 IoT 裝置與模組管理

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT Hub 中的自動裝置管理可自動執行管理大型設備佇列的許多重複性和複雜的任務。 通過自動裝置管理,您可以根據設備的屬性定位一組設備,定義所需的配置,然後讓 IoT 中心在設備進入作用域時更新它們。 此更新使用_自動設備配置_或_自動模組配置_完成,它允許您總結完成和合規性,處理合併和衝突,並分階段推出配置。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

自動設備管理的工作原理是更新一組具有所需屬性的設備孿生或模組孿生,並報告基於孿生報告屬性的摘要。  它引入了一個新的類和 JSON 文檔,稱為*配置*,包含三個部分:

* **目標條件**定義要更新的設備孿生或模組孿生的範圍。 目標條件被指定為對孿生標記和/或報告屬性的查詢。

* **目標內容**定義要在目標設備孿生或模組孿生中添加或更新所需的屬性。 內容包含了一個路徑，連往所要變更屬性的區段。

* **計量**會定義各種設定狀態 (例如 **Success**、**Progress** 及 **Error**) 的摘要計數。 自定義指標指定為對孿生報告屬性的查詢。  系統指標是衡量孿生更新狀態的默認指標,例如目標雙胞胎數和已成功更新的雙胞胎數。

自動配置在創建配置后不久首次運行,然後每隔五分鐘運行一次。 每次運行自動配置時都會運行指標查詢。

## <a name="implement-twins"></a>實施雙胞胎

自動裝置設定需要使用裝置對應項，以同步處理雲端和裝置之間的狀態。  有關詳細資訊,請參閱在[IoT 中心中瞭解和使用裝置孿生](iot-hub-devguide-device-twins.md)。

自動模組配置需要使用模組孿生來在雲和模組之間同步狀態。 有關詳細資訊,請參閱在[IoT 中心中瞭解和使用模組孿生](iot-hub-devguide-module-twins.md)。

## <a name="use-tags-to-target-twins"></a>使用標記定位雙胞胎

在創建配置之前,必須指定要影響的設備或模組。 Azure IoT 中心標識設備並使用設備孿生中的標記,並使用模組孿生中的標記標識模組。 每個設備或模組可以有多個標記,您可以以任何對解決方案有意義的方式定義它們。 例如,如果您管理不同位置的設備,則向設備孿生添加以下標記:

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

3. 選擇 **'新增裝置設定**' 或 **'新增模組' 設定**。

   ![新增裝置設定或模組設定](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

建立設定有五個步驟。 下列各節將逐步解說每一個步驟。 

### <a name="name-and-label"></a>名稱與標籤

1. 為設定指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。

2. 新增標籤以協助追蹤您的設定。 標籤是可描述設定的成對「**名稱**, **值**」。 例如，`HostPlatform, Linux` 或 `Version, 3.0.1`。

3. 選取 [下一步]**** 以移至下一個步驟。 

### <a name="specify-settings"></a>指定設定

本節定義要在目標設備或模組孿生中設置的內容。 每組設定都有兩個輸入。 第一個是雙路徑,它是要設置的雙所需屬性中通往 JSON 部分的路徑。  第二個是要插入該區段的 JSON 內容。 

例如,您可以將雙路徑設定為,`properties.desired.chiller-water`然後提供以下 JSON 內容: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![設定雙路徑與內容](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


還可以通過指定整個孿生路徑並提供沒有括弧的值來設置各個設置。 例如,使用雙路徑`properties.desired.chiller-water.temperature`,將內容設定`66`為 。 然後為壓力屬性創建新的雙設置。 

如果兩個或多個配置針對同一雙路徑,則將應用來自最高優先順序配置的內容(優先順序在步驟 4 中定義)。

如果要刪除現有屬性,請指定 屬性值`null`到 。

您可以通過選擇 **「添加設備孿生設置**」或 **「添加模組孿生設置**」來添加其他設置。

### <a name="specify-metrics-optional"></a>指定計量 (選擇性)

指標提供設備或模組在應用配置內容後可能報告的各種狀態的匯總計數。 例如，您可以建立擱置設定變更的計量、錯誤的計量，以及成功設定變更的計量。

每個配置最多可以有五個自定義指標。 

1. 輸入**指標名稱**的名稱。

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

如果要建置要回報已設定模組的指標,請從中`moduleId``devices.modules`選擇 。 例如：

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>目標裝置

使用孿生標記屬性來定位應接收此配置的特定設備或模組。 您還可以定位孿生報告的屬性。

自動設備配置只能針對設備孿生標記,而自動模組配置只能針對模組孿生標記。 

由於多個配置可能針對同一設備或模組,因此每個配置都需要一個優先順序編號。 如果發生衝突，則會優先選擇優先順序最高的設定。 

1. 為設定的 [優先順序]**** 輸入一個正整數。 最高的數值會視為最高優先順序。 如果有兩個設定具有相同的優先順序號碼，則會優先選擇最新建立的設定。 

2. 輸入**目標條件**,以確定此設定將針對哪些設備或模組。 條件基於孿生標記或孿生報告屬性,應與表達式格式匹配。 

   對於自動裝置配置,您可以僅指定要定位的標記或報告屬性。 例如，`tags.environment='test'` 或 `properties.reported.chillerProperties.model='4000x'`。 您可以指定 `*` 以將所有裝置設為目標。 
   
   對於自動模組配置,請使用查詢指定註冊到IoT中心模組的標記或報告屬性。 例如，`from devices.modules where tags.environment='test'` 或 `from devices.modules where properties.reported.chillerProperties.model='4000x'`。 通配符不能用於針對所有模組。 

3. 選取 [下一步]**** 移到最後一個步驟。

### <a name="review-configuration"></a>檢閱設定

檢閱您的設定資訊，然後選取 [提交]****。

## <a name="monitor-a-configuration"></a>監視設定

若要檢視設定的詳細資料，並監視正在執行該設定的裝置，請使用下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的 IoT 中樞。 

2. 選取 [IoT 裝置組態]****。

3. 檢查設定清單。 針對每個設定，您可以檢視下列詳細資料：

   * **識別碼**：設定的名稱。

   * **目標條件**- 用於定義目標設備或模組的查詢。

   * **優先順序**：指派給設定的優先順序號碼。

   * **建立時間**：建立設定時的時間戳記。 當有兩個具有相同優先順序的設定時，系統會使用此時間戳記來判斷應優先選擇的設定。 

   * **系統計量**：由 IoT 中樞所計算且無法由開發人員自訂的計量。 [目標] 會指定符合目標條件的裝置對應項數目。 [套用] 會指定由設定所修改的裝置對應項數目，這在有優先順序較高的個別設定同時也做出變更的情況下，可能會包含局部修改。 

   * **自定義指標**- 開發人員指定為對孿生報告屬性的查詢的指標。  每個設定最多可定義五個自訂計量。 
   
4. 選取您想要監視的設定。  

5. 檢查設定詳細資料。 針對接收設定的裝置，您可以使用索引標籤來檢視裝置的特定詳細資料。

   * **目標條件**- 與目標條件匹配的設備或模組。 

   * **計量**：系統計量和自訂計量的清單。  您可以透過在下拉清單中選擇指標,然後選擇 **「查看裝置**」或「**查看模組**」來查看針對每個指標計算的設備或模組的清單。

   * **設備孿生設置**或**模組孿生設置**- 由配置設置的孿生設置。 

   * **設定標籤**：用來描述設定的機碼值組。  標籤對功能沒有任何影響。 

## <a name="modify-a-configuration"></a>修改設定

修改設定時,更改會立即複製到所有目標設備或模組。 

如果您更新目標條件，就會發生下列更新：

* 如果孿生不符合舊的目標條件,但滿足新的目標條件,並且此配置是該孿生的最高優先順序,則應用此配置。 

* 如果當前運行此配置的孿生不再滿足目標條件,則配置中的設置將被刪除,並且孿生將由下一個最高優先順序配置修改。 

* 如果當前運行此配置的孿生不再滿足目標條件,並且不符合任何其他配置的目標條件,則將刪除配置中的設置,並且不會對孿生進行其他更改。 

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

在本文中,您學習了如何大規模配置和監視 IoT 設備。 遵循下列連結以深入了解如何管理 Azure IoT 中樞：

* [管理大量的 IoT 中樞裝置身分識別](iot-hub-bulk-identity-mgmt.md)
* [IoT 中樞度量](iot-hub-metrics.md)
* [作業監視](iot-hub-operations-monitoring.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中心開發人員指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/tutorial-simulate-device-linux.md)

若要探索使用 IoT 中樞裝置佈建服務進行 Just-In-Time 自動佈建，請參閱： 

* [Azure IoT 中樞裝置佈建服務](/azure/iot-dps)
