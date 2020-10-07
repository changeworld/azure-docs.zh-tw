---
title: 個人資訊的命名實體
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: eedfe4f24797a9b564479b2f8d3f4d04b0751272
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779450"
---
> [!NOTE]
> 若要 (PHI) 偵測受保護的健康情況資訊，請使用 `domain=phi` 參數和模型版本 `2020-04-01` 或更新版本。
>
> 例如： `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii?domain=phi&model-version=2020-07-01`
 
當您將要求傳送至端點時，會傳回下列實體類別 `/v3.1-preview.2/entities/recognition/pii` 。

| 類別   | 子類別 | 描述                          | 正在啟動模型版本 | 備註 |
|------------|-------------|--------------------------------------|------------------------|---|
| 人     | N/A         | 人員的名稱。  | `2019-10-01`  | 也會隨一起傳回 `domain=phi` 。 |
| PersonType | N/A         | 人員所持有的工作類型或角色。 | `2020-02-01` | |
| PhoneNumber | N/A | 只有) 的電話號碼 (美國和 EU 電話號碼。 | `2019-10-01` | 也會隨一起傳回 `domain=phi` 。 |
|組織  | N/A | 公司、政治群組、音樂波段、運動俱樂部、政府機構和公共組織。  | `2019-10-01` | 國籍和 religions 不包含在此實體類型中。  |
|組織 | 醫療 | 醫療公司和群組。 | `2020-04-01` |  |
|組織 | 股票間交換 | Stock exchange 群組。 | `2020-04-01` |  |
| 組織 | 運動 | 體育相關組織。 | `2020-04-01` |  |
| 位址 | N/A | 完整郵寄地址。  | `2020-04-01` | 也會隨一起傳回 `domain=phi` 。 |
| 歐盟 GPS 座標 | N/A | 歐盟內位置的 GPS 座標。  | `2019-10-01` |  |
| 電子郵件 | N/A | 電子郵件地址。 | `2019-10-01` | 也會隨一起傳回 `domain=phi` 。   |
| URL | N/A | 網站的 Url。 | `2019-10-01` | 也會隨一起傳回 `domain=phi` 。 |
| IP | N/A | 網路 IP 位址。 | `2019-10-01` | 也會隨一起傳回 `domain=phi` 。 |
| Datetime | N/A | 日的日期和時間。 | `2019-10-01` |  | 
| DateTime | Date | 行事曆日期。 | `2019-10-01` | 也會隨一起傳回 `domain=phi` 。 |
| 數量 | N/A | 數位和數位數量。 | `2019-10-01` |  |
| 數量 | 年齡 | 年齡。 | `2019-10-01` | | |

## <a name="azure-information"></a>Azure 資訊

此實體類別包含可識別的 Azure 資訊，包括驗證資訊和連接字串。 從模型版本開始提供 `2019-10-01` 。 不以參數傳回 `domain=phi` 。

| 子類別                           | 描述                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB 驗證金鑰             | Azure Cosmos DB 伺服器的授權金鑰。                           |
| Azure IAAS 資料庫連接字串與 Azure SQL 連接字串 | Azure 基礎結構即服務 (IaaS) 資料庫與 SQL 連接字串的連接字串。 |
| Azure SQL 連接字串           | Azure SQL Database 中資料庫的連接字串。                                |
| Azure IoT 連接字串           | Azure IoT 的連接字串。                        |
| Azure 發佈設定密碼        | Azure 發佈設定的密碼。                                        |
| Azure Redis 快取連接字串   | Redis 快取的連接字串。                             |
| Azure SAS                             | Azure 軟體即服務 (SaaS) 的連接字串。                     |
| Azure 服務匯流排連接字串   | Azure 服務匯流排的連接字串。                                 |
| Azure 儲存體帳戶金鑰             | Azure 儲存體帳戶的帳戶金鑰。                                   |
| Azure 儲存體帳戶金鑰 (一般)   | Azure 儲存體帳戶的一般帳戶金鑰。                           |
| SQL Server 連接字串          | 執行 SQL Server 之電腦的連接字串。                                         |

## <a name="identification"></a>識別

[!INCLUDE [supported identification entities](./identification-entities.md)]
