---
title: 瞭解物件模型和空間智慧圖形-Azure 數位 Twins |Microsoft Docs
description: 使用 Azure Digital Twins 建立人員、地點與裝置間的關聯性模型
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: cafec321e7c40e27d8de731feda1103451271507
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265203"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>了解 Digital Twins 物件模型和空間智慧圖形

Azure Digital Twins 是一項 Azure IoT 服務，可針對實體環境和相關聯的裝置、感應器和使用者提供完整的虛擬表示法。 它能將網域特定的概念整理為有幫助的模型來改善開發。 這些模型接著會被置於空間智慧圖形內。 這類概念可忠實建立人員、空間和裝置之間的關聯性和互動方式的模型。

Digital Twins 物件模型能描述網域特定的概念、類別和屬性。 模型是由想要依據自身需求自訂解決方案的使用者預先定義。 這些預先定義的 Digital Twins 物件模型會共同組成「本體」__。 智慧建築的本體會描述區域 (region)、地點、樓層、辦公室、區域 (zone)、會議室和聚焦會議室。 能源網的本體會描述各種發電站、變電站、能源資源和客戶。 透過使用 Digital Twins 物件模型和本體，便可以自訂各種案例和需求。

Digital Twins 物件模型和本體皆準備就緒後，即可填入「空間圖形」__。 空間圖形是與 IoT 解決方案相關的空間、裝置和人員之間各種關聯性的虛擬表示法。 此圖表顯示使用智慧建築本體的空間圖形範例。

[![Digital Twins 空間圖形建築](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

空間圖形可將空間、裝置、感應器和使用者結合在一起。 每個空間圖形都會以模仿真實世界的方式互相連結。 在此範例中，場地 43 具有四個樓層，每個樓層都有許多不同的區域。 使用者會與其工作站相關聯，並取得圖形某些部分的存取權。 系統管理員有權對空間圖形進行變更，而訪客只有檢視特定建築資料的權限。

## <a name="digital-twins-object-models"></a>Digital Twins 物件模型

Digital Twins 物件模型支援下列主要物件類別：

- **空間**是虛擬或`Tenant`實體位置， `Customer` `Region`例如、、和。 `Venue`
- **裝置**是虛擬或實體的裝置部件，例如 `AwesomeCompany Device` 和 `Raspberry Pi 3`。
- **感應器**是可偵測事件的物件，例如 `AwesomeCompany Temperature Sensor` 和 `AwesomeCompany Presence Sensor`。
- **使用者**會識別現用者及其特性。

其他物件類別包括：

- **資源**會連結至空間，並通常代表空間圖形中的物件所要使用的 Azure 資源，例如 `IoTHub`。
- **Blob**會連結至物件 (例如空間、裝置、感應器及使用者)。 它們會以具 mime 類型和中繼資料之檔案的形式被使用，例如 `maps`、`pictures` 及 `manuals`。
- **擴充類型**是可為實體增補某些特性的可擴充列舉，例如 `SpaceType` 和 `SpaceSubtype`。
- **本體**代表一組擴充`Default`類型，例如`Building` `BACnet`、、和。 `EnergyGrid`
- **屬性索引鍵和值**是空間、裝置、感應器和使用者的自訂特性。 它們可搭配內建特性使用，例如，以 `DeltaProcessingRefreshTime` 作為索引鍵，並以 `10` 作為值。
- **角色**是在空間圖形中指派給使用者和裝置的許可權集， `Space Administrator` `User Administrator`例如、、和。 `Device Administrator`
- **角色指派**是空間圖形中角色和物件之間的關聯。 例如，使用者或服務主體可以被授與權限，以管理空間圖形中的特定空間。
- **安全性金鑰存放區**可為指定空間物件下之階層內所包含的所有裝置提供安全性金鑰，使裝置能夠安全地與 Digital Twins 進行通訊。
- **使用者定義函式** (UDF) 可允許在空間圖形內進行可自訂感應器遙測的處理。 例如，UDF 可以：
  - 設定感應器值。
  - 執行以感應器讀數為基礎的自訂邏輯，並將輸出設定至空間。
  - 將中繼資料附加至空間。
  - 在符合預先定義的條件時傳送通知。 目前可以用 JavaScript 撰寫 UDF。
- **比對器**是可決定針對指定遙測訊息應執行哪些 UDF 的物件。
- **端點**是可路由遙測訊息和數位 Twins 事件的位置，例如`Event Hub` `Service Bus`、和。 `Event Grid`

## <a name="spatial-intelligence-graph"></a>空間智慧圖形

空間圖形是定義於 Digital Twins 物件模型中的空間、裝置和人員的階層式圖形。 空間圖形支援繼承、篩選、周遊、延展性和擴充性。 您可透過 REST API 集合來管理空間圖形並與其互動。

如果您在自己的訂用帳戶中部署 Digital Twins 服務，便會成為根節點的全域系統管理員。 接著，系統會自動授與您整個結構的完整存取權。 使用空間 API 在圖形中佈建空間。 使用感應器 API 來利用裝置 API 及感應器佈建服務。 [開放原始碼工具](https://github.com/Azure-Samples/digital-twins-samples-csharp)也可以用來大量布建圖表。

**圖形繼承**。 繼承會套用至源自父節點及其下所有節點的權限和屬性。 例如，如果為使用者指派指定節點上的角色，該使用者就會有該角色針對指定節點的權限，以及其下每個節點的權限。 針對指定節點所定義的每個屬性索引鍵和擴充類型，也會會由該節點下所有的節點繼承。

**圖形篩選**。 篩選是用來縮小要求結果。 您可以依識別碼、名稱、類型、子類型、父空間及相關聯的空間來進行篩選。 您也可以依感應器資料類型、屬性索引鍵和值、*traverse*、*minLevel*、*maxLevel*，以及其他 OData 篩選參數來進行篩選。

**圖形周遊**。 您可以透過空間圖形的深度與廣度進行周遊。 在深度方面，使用參數 *traverse*、*minLevel* 及 *maxLevel*來由上往下或由下往上周遊圖形。 針對廣度，周遊圖形以取得直接連結至父空間或其其中一個子系的同層級節點。 在查詢物件時，您可以使用 GET API 的 *includes* 參數來取得與該物件具關連性的所有相關物件。

**圖形延展性**。 Digital Twins 可確保圖形的延展性，因此能夠處理您實際的工作負載。 Digital Twins 可用來代表不動產、基礎結構、裝置、感應器、遙測等項目的大型組合。

**圖形擴充性**。 使用擴充性來以新的類型和本體自訂基礎的 Digital Twins 物件模型。 您的 Digital Twins 資料也能透過可擴充的屬性和值來補強。

### <a name="spatial-intelligence-graph-management-apis"></a>空間智慧圖形管理 API

在您從 [Azure 入口網站](https://portal.azure.com)部署 Digital Twins 之後，系統便會自動產生管理 API 的[Swagger](https://swagger.io/tools/swagger-ui/) \(英文\) URL。 它會以下列格式顯示在 Azure 入口網站中的 [概觀]**** 區段。

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name | 更換為 |
| --- | --- |
| YOUR_INSTANCE_NAME | Digital Twins 執行個體的名稱 |
| YOUR_LOCATION | 裝載您執行個體的伺服器區域 |

 完整的 URL 格式顯示在下列影像中。

[![Digital Twins 入口網站管理 API](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

如需如何使用空間智慧圖形的詳細資料，請瀏覽「Azure Digital Twins 管理 API 搶先預覽」。

> [!TIP]
> Swagger 搶先預覽中有 API 功能集的示範。
> 這些內容位在 [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger)。

深入了解[如何使用 Swagger](how-to-use-swagger.md)。

所有 API 呼叫都必須使用 [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code) 進行驗證。 API 會遵循 [Microsoft REST API 指導方針慣例](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)。 大部分傳回集合的 API 都支援 [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) 系統查詢選項。

## <a name="next-steps"></a>後續步驟

- 若要了解裝置連線能力，以及如何將遙測訊息傳送至 Digital Twins，請參閱 [Azure Digital Twins 裝置連線能力和遙測輸入](concepts-device-ingress.md)。

- 若要深入了解管理 API 的限制和節流，請參閱 [Azure Digital Twins API 管理和限制](concepts-service-limits.md)。
