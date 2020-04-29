---
title: 一般查詢模式-Azure 數位 Twins |Microsoft Docs
description: 瞭解 Azure 數位 Twins 管理 Api 的數個常見 API 查詢模式。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 133c0e0dcc07afb85a0f3af9ae51d2207abac293
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77589108"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>如何針對一般工作查詢 Azure Digital Twins API

本文將說明查詢模式如何協助您針對 Azure Digital Twins 執行常見案例。 本文假設您的 Digital Twins 執行個體已在執行。 您可以使用任何 REST 用戶端，例如 Postman。 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>查詢空間和型別

本節說明可取得有關您所佈建空間之詳細資訊的範例查詢。 使用範例查詢提出已驗證的 GET HTTP 要求，以您的設定值取代預留位置。 

- 取得根節點的空間。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- 依名稱取得空間，且包含裝置、感應器、計算的值和感應器值。 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- 取得空間及其裝置/感應器資訊，其父代是指定的空間識別碼，而且這是[相對於指定空白字元](how-to-navigate-apis.md#api-navigation)的層級 2 至 5。 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- 取得具有指定識別碼的空間，且包含計算的值與感應器值。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- 取得特定空間的屬性索引鍵。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- 取得具有屬性索引鍵名稱為 *AreaInSqMeters* 且其值為 30 的空間。 您也可以執行字串作業，例如使用 `name = X contains Y` 取得包含屬性索引鍵的空間。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- 取得所有名稱 *Temperature* 且有相關聯的相依性和本體。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>查詢角色和角色指派

本節說明一些可取得角色及其指派之詳細資訊的查詢。 

- 取得 Azure Digital Twins 支援的所有角色。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- 取得 Digital Twins 執行個體中的所有角色指派。 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- 取得特定路徑上的角色指派。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>查詢裝置

本節說明一些如何使用管理 API 來取得有關裝置特定資訊的範例。 所有的 API 呼叫都必須是已驗證的 GET HTTP 要求。

- 取得所有裝置。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- 尋找所有裝置狀態。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- 取得特定裝置。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- 取得已連接至根空間的所有裝置。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- 取得已連接至空間層級 2 到 4 的所有裝置。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- 取得直接連接至特定空間識別碼的裝置。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- 取得連接至特定空間及其子系的所有裝置。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- 取得連接至空間子系 (但不包括該空間) 的所有裝置。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- 取得連接至空間直接子系的所有裝置。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- 取得連接至空間上階之一的所有裝置。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- 取得連接至層級小於或等於 5 之空間子系的所有裝置。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- 取得所有連接至與識別碼 *YOUR_SPACE_ID* 相同層級之空間的所有裝置。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- 取得裝置的 IoT 中樞連接字串。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- 取得具有指定硬體識別碼的裝置，包含附加的感應器。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- 取得特定資料類型的感應器，在此案例中為 *Motion* 和 *Temperature*。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>查詢比對器和使用者定義函式 

- 取得所有已佈建的比對器和其識別碼。

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- 取得特定比對器的相關詳細資料，包括與其相關聯的空間和使用者定義函式。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- 針對感應器評估比對器，並啟用記錄以進行偵錯。 傳回的 HTTP GET 訊息會告訴你比對器和感應器是否屬於資料類型。 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- 取得使用者定義函式的識別碼。 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- 取得特定使用者定義函式的內容 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>查詢使用者

本結識犯一些範例 API 查詢以在 Azure Digital Twins 中管理使用者。 提出 HTTP GET 要求，使用您設定中的值取代預留位置。 

- 取得所有使用者。 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- 取得特定使用者。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>後續步驟

若要了解如何使用管理 API 進行驗證，請閱讀[使用 API 進行驗證](./security-authenticating-apis.md)。

若要深入瞭解您的 API 端點，請閱讀[如何使用數位 Twins Swagger](./how-to-use-swagger.md)。
