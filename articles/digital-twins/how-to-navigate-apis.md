---
title: 流覽 Api-Azure 數位 Twins |Microsoft Docs
description: 了解查詢 Azure Digital Twins 管理 API 的常見模式。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e9cdfd40a9672d19ef32dede0baadcdd56266bab
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589091"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>如何使用 Azure Digital Twins 管理 API

Azure Digital Twins 管理 API 可為您的 IoT 應用程式提供強大功能。 本文示範如何瀏覽 API 結構。  

## <a name="api-summary"></a>API summary

下列清單顯示 Digital Twins API 的元件。

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces)：這些 API 可與您環境中的實體位置互動。 這些 API 會以[空間圖形](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)的形式，協助您建立、刪除和管理實體位置的數位對應。

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices)：這些 API 可與您環境中的裝置互動。 這些裝置可管理一或多個感應器。 例如，裝置可以是手機、Raspberry Pi 感應器 Pod，或 Lora 閘道器等等。

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors)：這些 API 可協助您與和裝置及實體位置相關聯的感應器通訊。 感應器會記錄並傳送環境值，這些值可接著用來操作空間環境。  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources)：這些 API 可協助您為 Digital Twin 執行個體設定資源 (例如 IoT 中樞)。

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types)：這些 API 可讓您建立與 Digital Twins 物件延伸類型的關聯性，以將特定特性新增至那些物件。 這些類型可在 UI 中輕鬆地篩選和分組物件，並自訂可處理遙測資料的函式。 延伸類型的範例包括 *DeviceType*、*SensorType*、*SensorDataType*、*SpaceType*、*SpaceSubType*、*SpaceBlobType*、*SpaceResourceType* 等等。

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies)：這些 API 可協助您管理主體 (即為延伸類型的集合)。 主體根據各物件類型所代表的實體空間提供名稱。 例如，*BACnet* 主體為 *sensor types*、*datatypes*、*datasubtypes* 和 *dataunittypes* 提供特定名稱。 主體是由服務所建立並管理。 使用者可以載入和卸載主體。 主體載入時，其相關聯的所有類型名稱都會啟用，並準備好可在您的空間圖形中佈建。 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys)：您可以使用這些 API 為自己的「空間」、「裝置」、「使用者」和「感應器」建立自訂屬性。 這些屬性會建立為索引鍵/值組。 您可以設定這些屬性的 *PrimitiveDataType*，定義它們的資料類型。 例如，您可以為您的感應器類型 *uint* 定義名稱為 *BasicTemperatureDeltaProcessingRefreshTime* 的屬性，然後為每個感應器指派此屬性的值。 您也可以在建立屬性時為這些值新增條件約束，例如「最小」和「最大」範圍，以及允許的值，例如 *ValidationData*。

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers)：這些 API 可讓您指定要從傳入裝置資料評估的條件。 如需詳細資訊，請參閱[這篇文章](concepts-user-defined-functions.md#matchers)。 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions)：這些 API 可讓您建立、刪除或更新自訂函式，自訂函式可在「比對器」定義的條件發生時執行，處理來自環境的資料。 如需自訂函式 (也稱為「使用者定義函式」) 的詳細資訊，請參閱[這篇文章](concepts-user-defined-functions.md#user-defined-functions)。 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints)：這些 API 可讓您建立端點，讓您的 Digital Twins 解決方案能夠與其他 Azure 服務通訊，以進行資料儲存和分析。 如需詳細資訊，請閱讀[這篇文章](concepts-events-routing.md)。 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores)：這些 API 可讓您管理空間的安全性金鑰存放區。 這些存放區能保存安全性金鑰的集合，並可讓您輕鬆擷取最新的有效金鑰。

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users)：這些 API 可讓您將使用者與您的空間建立關聯，以在需要時找出這些人。 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System)：這些 API 可讓您管理整個系統的設定，例如空間和感應器的預設類型。 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments)：這些 API 可讓您將角色與實體 (例如使用者識別碼、使用者定義的函式識別碼等等) 建立關聯。每個角色指派都包含要建立關聯的實體的識別碼、實體類型、要建立關聯的角色識別碼、租用戶識別碼，以及實體可使用該關聯存取的資源上限路徑定義。 如需詳細資訊，請閱讀[這篇文章](security-role-based-access-control.md)。


## <a name="api-navigation"></a>API 瀏覽

Digital Twins API 支援使用下列參數篩選和瀏覽整個空間圖形：

- **spaceId**：此 API 會依指定的空間識別碼篩選結果。 此外，布林值旗標 **useParentSpace** 可適用於 [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) API，其可指出指定的空間識別碼參考父空間，而非目前的空間。 

- **minLevel** 和 **maxLevel**：根空間會被視為層級 1。 具有父空間在層級 *n* 的空間，位於層級 *n+1*。 設定這些值之後，您就可以篩選特定層級的結果。 這些是設定時已內含的值。 裝置、感應器和其他物件會被視為與其最接近之空間處在相同層級。 若要取得指定層級的所有物件，請將 **minLevel** 和 **maxLevel** 設為相同的值。

- **minRelative** 和 **maxRelative**：當指定這些篩選時，對應的層級是相對於指定空間識別碼的層級：
   - 相對層級 *0* 是與指定之空間識別碼相同的層級。
   - 相對層級 *1* 代表與指定空間識別碼子系的層級相同。 相對層級 *n* 代表空間低於指定空間 *n* 個叢集。
   - 相對層級 *-1* 代表與指定空間的父空間層級相同。

- **traverse**：可讓您以下列值指定的方向從指定的空間識別碼周遊。
   - **無**：此預設值會篩選指定的空間識別碼。
   - **Down**：這會依指定之空間識別碼及其子系篩選。 
   - **Up**：這會依指定之空間識別碼與其上階篩選。 
   - **Span**：這會依指定之空間識別碼的相同層級，篩選空間圖形的水平部分。 這需要將 **minRelative** 或 **maxRelative** 設為 True。 


### <a name="examples"></a>範例

下列清單顯示一些瀏覽 [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API 的範例。 請注意，預留位置 `YOUR_MANAGEMENT_API_URL` 指的是 Digital Twins API 的 URI (格式 `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`)，其中 `YOUR_INSTANCE_NAME` 是您的 Azure Digital Twins 執行個體名稱，而 `YOUR_LOCATION` 是裝載您執行個體的所在區域。

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` 會傳回連接至根空間的所有裝置。
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` 會傳回已連接至空間層級 2、3 或 4 的所有裝置。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` 會傳回直接連接至 mySpaceId 的所有裝置。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` 會傳回連接至 mySpaceId 或其子系之一的所有裝置。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` 會傳回連接至 mySpaceId 子系 (但不包含 mySpaceId) 的所有裝置。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` 會傳回連接至 mySpaceId 直接子系的所有裝置。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` 會傳回連接至 mySpaceId 上階之一的所有裝置。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` 會傳回連接至層級小於或等於 5 之 mySpaceId 子系的所有裝置。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` 會傳回連接至與 mySpaceId 相同層級之空間的所有裝置。


## <a name="odata-support"></a>OData 支援

大部分傳回集合的 API (例如 /spaces 上的 GET 呼叫) 都支援下列一般 [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) 系統查詢選項的子集：  

* **$filter**
* **$orderby** 
* **$top**
* **$skip**：如果您想要顯示整個集合，您應該在單一呼叫中要求它作為完整集合，然後在應用程式中執行分頁。 

> [!NOTE]
> 目前不支援某些 OData 選項（例如 **$count**、 **$expand**和 **$search**的查詢選項）。

### <a name="examples"></a>範例

下列清單描述數個具有有效 OData 語法的查詢：

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>後續步驟

若要了解一些常見的 API 查詢模式，請閱讀[如何針對一般工作查詢 Azure Digital Twins API](./how-to-query-common-apis.md)。

若要深入瞭解您的 API 端點，請閱讀[如何使用數位 Twins Swagger](./how-to-use-swagger.md)。

若要查看 OData 語法和可用的比較運算子，請參閱[Azure 認知搜尋中的 odata 比較運算子](../search/search-query-odata-comparison-operators.md)。
