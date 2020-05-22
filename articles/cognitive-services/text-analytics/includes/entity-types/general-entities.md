---
title: 一般命名實體
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 60cd6b8ba2cbfca497ae1b92113b12e85a34bf26
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83778192"
---
## <a name="general-entity-types"></a>一般實體類型：

### <a name="person"></a>個人

辨識文字中的人員名稱。

語言：
* 公開預覽： `Arabic` 、 `Czech` 、 `Chinese-Simplified` 、 `Danish` 、 `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` 、、、、、、、、、、、、、、和`Turkish`

| 子類型名稱 | Description                                                      | 從模型版本開始提供 |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N/A          | 辨識的人員名稱 `Bill Gates` ，例如`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
人員所持有的作業類型或角色。

語言：
* 公開預覽：`English`

| 子類型名稱 | Description                                                                                | 從模型版本開始提供 |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | 作業類型，例如、、 `civil engineer` `salesperson` `chef` `librarian` 、`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>位置

自然和人為地標、結構、地理功能和地緣政治實體。

語言：

* 公開預覽： `Arabic` 、 `Czech` 、 `Chinese-Simplified` 、 `Danish` 、 `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` 、、、、、、、、、、、、、、和`Turkish`

| 子類型名稱              | Description                                                                              | 從模型版本開始提供 |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N/A                       | 位置，例如、 `Atlantic Ocean` `library` `Eiffel Tower` 、`Statue of Liberty`  | `2019-10-01`                           |
| 地緣政治實體（GPE）-僅限英文| 城市、國家/地區、州/省，例如、、 `Seattle` `Pennsylvania` `South Africa` 、`Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>組織  

已辨識的組織、公司、機構和其他人員群組。 例如：公司、政治群組、音樂波段、運動俱樂部、政府機構和公用組織。 Nationalities 和 religions 不包含在此實體類型中。 

語言： 

* 公開預覽： `Arabic` 、 `Czech` 、 `Chinese-Simplified` 、 `Danish` 、 `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` 、、、、、、、、、、、、、、和`Turkish`

| 子類型名稱 | Description                                                                                             | 從模型版本開始提供 |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | 組織，例如、 `Microsoft` `NASA` `National Oceanic and Atmospheric Administration` 、`VOA` | `2019-10-01`                           |

### <a name="event"></a>事件  

歷史、社交和自然發生的事件。  

語言： 

* 公開預覽：`English`

| 子類型名稱 | Description                                                            | 從模型版本開始提供 |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N/A          | 事件，例如 `wedding` 、 `hurricane` 、 `car accident` 、 `solar eclipse` 、`American Revolution` | `2020-02-01`                           |

### <a name="product"></a>產品  

各種類別目錄的實體物件。  

語言： 

* 公開預覽：`English`

| 子類型名稱 | Description                                                                        | 從模型版本開始提供 |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | 例如，、 `Microsoft Surface laptop` 、 `sunglasses` `motorcycle` 、 `bag` 、`Xbox` | `2020-02-01`                           |
| 運算    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>技能  

描述功能或專業知識的實體。  

語言： 

* 公開預覽：`English`

| 子類型名稱 | Description                                                                 | 從模型版本開始提供 |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| N/A          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>電話號碼

電話號碼（僅限美國電話號碼）。 

語言：

* 公開預覽：`English`

| 子類型名稱 | Description                                    | 從模型版本開始提供 |
|--------------|------------------------------------------------|----------------------------------------|
| N/A          | 美國電話號碼，例如`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>電子郵件

電子郵件地址。 

語言：

* 公開預覽：`English`

| 子類型名稱 | Description                                      | 從模型版本開始提供 |
|--------------|--------------------------------------------------|----------------------------------------|
| N/A          | 電子郵件地址，例如`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

網際網路 Url。

語言：

* 公開預覽：`English`

| 子類型名稱 | Description                                          | 從模型版本開始提供 |
|--------------|------------------------------------------------------|----------------------------------------|
| N/A          | 網站的 Url，例如`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP 位址

網際網路通訊協定位址

語言：

* 公開預覽：`English`

| 子類型名稱 | Description                              | 從模型版本開始提供 |
|--------------|------------------------------------------|----------------------------------------|
| N/A          | 例如的網路位址`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>Datetime

日期和時間實體。 

* 從模型版本開始提供`2019-10-01`

語言：

* 公開預覽： `Chinese-Simplified` 、 `English` 、 `French` `German` 和`Spanish`

| 子類型名稱    | 範例                     |
|-------------|------------------------------|
| N/A         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| 日期  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| 日期範圍    | `August 2nd to August 5th`         |
| 時間範圍   | `4-6PM`, `10:00AM to Noon`          |
| Duration | `2.5 minutes`, `one and a half hours`         |
| 設定 | `every Saturday`         |

###  <a name="quantity"></a>數量

數位和數值數量。 

* 從模型版本開始提供`2019-10-01`

語言：

* 公開預覽： `Chinese-Simplified` 、 `English` 、 `French` `German` 和`Spanish`

| 子類型名稱    | 範例                     |
|-------------|------------------------------|
| 數字         | `6`, `six`                   |
| 百分比  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Age         | `90 day old`, `30 years old` |
| 貨幣    | `$10.99`, `&euro;30.00`           |
| 維度   | `10 miles`, `40 cm`          |
| 溫度 | `32 degrees`, `10°C`         |
