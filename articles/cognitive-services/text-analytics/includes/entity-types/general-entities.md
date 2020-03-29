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
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77211412"
---
## <a name="general-entity-types"></a>常規實體類型：

### <a name="person"></a>個人

在文本中識別人員姓名。

語言：
* 公共預覽： `Arabic` `Czech`、 `Chinese-Simplified` `Danish`、 `Dutch` `English`、 `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish``Turkish`

| 子類型名稱 | 描述                                                      | 可從模型版本開始 |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N/A          | 識別人員姓名，例如`Bill Gates`，`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>人員類型
工作類型或人員擔任的角色。

語言：
* 公共預覽：`English`

| 子類型名稱 | 描述                                                                                | 可從模型版本開始 |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | 作業類型，例如`civil engineer` `salesperson`， `chef` `librarian`， ， 、 、 、`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Location

自然和人為的地標、結構、地理特徵和地緣政治實體。

語言：

* 公共預覽： `Arabic` `Czech`、 `Chinese-Simplified` `Danish`、 `Dutch` `English`、 `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish``Turkish`

| 子類型名稱              | 描述                                                                              | 可從模型版本開始 |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N/A                       | 位置，例如`Atlantic Ocean`， `library` `Eiffel Tower`，`Statue of Liberty`  | `2019-10-01`                           |
| 地緣政治實體 （GPE） - 僅限英語| 城市、國家/地區、州`Seattle`等`Pennsylvania` `South Africa`， 、`Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>組織  

公認的組織、公司、機構和其他群體。 例如：公司、政治團體、樂隊、體育俱樂部、政府機構和公共組織。 民族和宗教不包括在此實體類型中。 

語言： 

* 公共預覽： `Arabic` `Czech`、 `Chinese-Simplified` `Danish`、 `Dutch` `English`、 `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish``Turkish`

| 子類型名稱 | 描述                                                                                             | 可從模型版本開始 |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | 組織，例如`Microsoft`， `NASA` `National Oceanic and Atmospheric Administration``VOA` | `2019-10-01`                           |

### <a name="event"></a>事件  

歷史、社會和自然事件。  

語言： 

* 公共預覽：`English`

| 子類型名稱 | 描述                                                            | 可從模型版本開始 |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N/A          | 事件，如`wedding` `hurricane`， `car accident` `solar eclipse`， ，`American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Products  

不同類別的物理物件。  

語言： 

* 公共預覽：`English`

| 子類型名稱 | 描述                                                                        | 可從模型版本開始 |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | `Microsoft Surface laptop`例如， `sunglasses`、 `motorcycle`、 `bag`、 、`Xbox` | `2020-02-01`                           |
| 運算    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>技能  

描述能力或專業知識的實體。  

語言： 

* 公共預覽：`English`

| 子類型名稱 | 描述                                                                 | 可從模型版本開始 |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| N/A          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>電話號碼

電話號碼（僅限美國電話號碼）。 

語言：

* 公共預覽：`English`

| 子類型名稱 | 描述                                    | 可從模型版本開始 |
|--------------|------------------------------------------------|----------------------------------------|
| N/A          | 美國電話號碼，例如`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>電子郵件

電子郵件地址。 

語言：

* 公共預覽：`English`

| 子類型名稱 | 描述                                      | 可從模型版本開始 |
|--------------|--------------------------------------------------|----------------------------------------|
| N/A          | 電子郵件地址，例如`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

互聯網網址。

語言：

* 公共預覽：`English`

| 子類型名稱 | 描述                                          | 可從模型版本開始 |
|--------------|------------------------------------------------------|----------------------------------------|
| N/A          | 網址到網站，例如`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP 位址

互聯網協定位址

語言：

* 公共預覽：`English`

| 子類型名稱 | 描述                              | 可從模型版本開始 |
|--------------|------------------------------------------|----------------------------------------|
| N/A          | 例如網路位址`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>Datetime

日期和時間實體。 

* 可從模型版本開始`2019-10-01`

語言：

* 公共預覽： `Chinese-Simplified` `English`、 `French` `German` 、 和`Spanish`

| 子類型名稱    | 範例                     |
|-------------|------------------------------|
| N/A         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Date  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| 日期範圍    | `August 2nd to August 5th`         |
| 時間範圍   | `4-6PM`, `10:00AM to Noon`          |
| Duration | `2.5 minutes`, `one and a half hours`         |
| 設定 | `every Saturday`         |

###  <a name="quantity"></a>數量

數位和數位數量。 

* 可從模型版本開始`2019-10-01`

語言：

* 公共預覽： `Chinese-Simplified` `English`、 `French` `German` 、 和`Spanish`

| 子類型名稱    | 範例                     |
|-------------|------------------------------|
| Number         | `6`, `six`                   |
| 百分比  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Age         | `90 day old`, `30 years old` |
| 貨幣    | `$10.99`, `€30.00`           |
| 維度   | `10 miles`, `40 cm`          |
| 溫度 | `32 degrees`, `10°C`         |
