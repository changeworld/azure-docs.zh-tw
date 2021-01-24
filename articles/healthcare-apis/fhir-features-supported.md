---
title: Azure 中支援的 FHIR 功能-Azure API for FHIR
description: 本文說明在 Azure API for FHIR 中執行的 FHIR 規格功能
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 43372eb0a9f7c08f6957627950769c1941580bd8
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745862"
---
# <a name="features"></a>功能

Azure API for FHIR 可為 Azure 提供完全受控的 Microsoft FHIR Server 部署。 伺服器是 [FHIR](https://hl7.org/fhir) 標準的實作為。 本檔列出 FHIR 伺服器的主要功能。

## <a name="fhir-version"></a>FHIR 版本

支援的最新版本： `4.0.1`

目前也支援舊版，包括： `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 支援-PaaS | 支援的作業系統 (SQL)  | 支援的-OSS (Cosmos DB)  | 註解                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 讀取                           | 是       | 是       | 是       |                                                     |
| vread                          | 是       | 是       | 是       |                                                     |
| update                         | 是       | 是       | 是       |                                                     |
| 使用開放式鎖定更新 | 是       | 是       | 是       |                                                     |
| 更新 (條件式)            | 是       | 是       | 是       |                                                     |
| 補丁                          | 否        | 否        | 否        |                                                     |
| delete                         | 是       | 是       | 是       |                                                     |
| 刪除 (條件式)            | 否        | 否        | 否        |                                                     |
| history                        | 是       | 是       | 是       |                                                     |
| 建立                         | 是       | 是       | 是       | 支援 POST/PUT                               |
| 建立 (條件式)            | 是       | 是       | 是       | 問題 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| 搜尋                         | 部分   | 部分   | 部分   | 請參閱下方                                           |
| 連鎖搜尋                 | 否        | 是       | 否        |                                           |
| 反向連結搜尋         | 否        | 否        | 否        |                                            |
| capabilities                   | 是       | 是       | 是       |                                                     |
| 批次                          | 是       | 是       | 是       |                                                     |
| 交易                    | 否        | 是       | 否        |                                                     |
| 分頁                         | 部分   | 部分   | 部分   | `self``next`支援和                     |
| 仲介                 | 否        | 否        | 否        |                                                     |

## <a name="search"></a>搜尋

支援所有搜尋參數類型。 

| 搜尋參數類型 | 支援-PaaS | 支援的作業系統 (SQL)  | 支援的-OSS (Cosmos DB)  | 註解 |
|-----------------------|-----------|-----------|-----------|---------|
| Number                | 是       | 是       | 是       |         |
| Date/DateTime         | 是       | 是       | 是       |         |
| String                | 是       | 是       | 是       |         |
| 權杖                 | 是       | 是       | 是       |         |
| 參考             | 是       | 是       | 是       |         |
| 複合             | 是       | 是       | 是       |         |
| 數量              | 是       | 是       | 是       |         |
| URI                   | 是       | 是       | 是       |         |
| 特殊               | 否        | 否        | 否        |         |


| 修飾詞             | 支援-PaaS | 支援的作業系統 (SQL)  | 支援的-OSS (Cosmos DB)  | 註解 |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | 是       | 是       | 是       |         |
|`:exact`               | 是       | 是       | 是       |         |
|`:contains`            | 是       | 是       | 是       |         |
|`:text`                | 是       | 是       | 是       |         |
|`:in` (token)           | 否        | 否        | 否        |         |
|`:below` (token)        | 否        | 否        | 否        |         |
|`:above` (token)        | 否        | 否        | 否        |         |
|`:not-in` (token)       | 否        | 否        | 否        |         |
|`:[type]` (參考)   | 否        | 否        | 否        |         |
|`:below` (uri)          | 是       | 是       | 是       |         |
|`:not`                 | 否        | 否        | 否        |         |
|`:above` (uri)          | 否        | 否        | 否        | 問題 [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| 一般搜尋參數 | 支援-PaaS | 支援的作業系統 (SQL)  | 支援的-OSS (Cosmos DB)  | 註解 |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | 是       | 是       | 是       |         |
| `_lastUpdated`          | 是       | 是       | 是       |         |
| `_tag`                  | 是       | 是       | 是       |         |
| `_profile`              | 部分   | 部分   | 部分   | 僅在 STU3 中支援，不支援 R4 |
| `_security`             | 是       | 是       | 是       |         |
| `_text`                 | 否        | 否        | 否        |         |
| `_content`              | 否        | 否        | 否        |         |
| `_list`                 | 是       | 是       | 是       |         |
| `_has`                  | 否        | 否        | 否        |         |
| `_type`                 | 是       | 是       | 是       |         |
| `_query`                | 否        | 否        | 否        |         |
| `_filter`               | 否        | 否        | 否        |         |

| 搜尋結果參數 | 支援-PaaS | 支援的作業系統 (SQL)  | 支援的-OSS (Cosmos DB)  | 註解 |
|-------------------------|-----------|-----------|-----------|---------|
| `_sort`                 | 部分        | 部分   | 部分        |   支援 `_sort=_lastUpdated`       |
| `_count`                | 是       | 是       | 是       | `_count` 的限制為100個字元。 如果設定為大於100，則只會傳回100，並會在組合中傳回警告。 |
| `_include`              | 是       | 是       | 是       |包含的專案限制為100。 在 PaaS 和 OSS 上包含 Cosmos DB 不包括：反覆運算支援。|
| `_revinclude`           | 是       | 是       | 是       | 包含的專案限制為100。 在 PaaS 和 OSS 上包含 Cosmos DB 不包括：反覆運算支援。|
| `_summary`              | 部分   | 部分   | 部分   | 支援 `_summary=count` |
| `_total`                | 部分   | 部分   | 部分   | _total = 非且 _total = 精確      |
| `_elements`             | 是       | 是       | 是       |         |
| `_contained`            | 否        | 否        | 否        |         |
| `containedType`         | 否        | 否        | 否        |         |
| `_score`                | 否        | 否        | 否        |         |

## <a name="extended-operations"></a>擴充作業

擴充 RESTful API 所支援的所有作業。

| 搜尋參數類型 | 支援-PaaS | 支援的作業系統 (SQL)  | 支援的-OSS (Cosmos DB)  | 註解 |
|------------------------|-----------|-----------|-----------|---------|
| $export (整個系統)  | 是       | 是       | 是       |         |
| 患者/$export        | 是       | 是       | 是       |         |
| 群組/$export          | 是       | 是       | 是       |         |

## <a name="persistence"></a>持續性

Microsoft FHIR Server 具有可插入的持續性模組 (請參閱 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)) 。

目前 FHIR 伺服器開放原始碼程式碼包含 [Azure Cosmos DB](../cosmos-db/index-overview.md) 和 [SQL Database](https://azure.microsoft.com/services/sql-database/)的執行。

Cosmos DB 是全域散發的多模型 (SQL API、MongoDB API 等 ) 資料庫。 它支援不同的 [一致性層級](../cosmos-db/consistency-levels.md)。 預設部署範本會以一致性設定 FHIR 伺服器 `Strong` ，但您可以修改一致性原則， (通常會使用要求標頭，以要求為基礎放寬) 的要求 `x-ms-consistency-level` 。

## <a name="role-based-access-control"></a>角色型存取控制

FHIR 伺服器使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 進行存取控制。 具體而言，如果設定參數設定為，則會強制執行以角色為基礎的存取控制 (RBAC) ， `FhirServer:Security:Enabled` `true` 而且除了 `/metadata` FHIR 伺服器) 以外 (的所有要求都必須 `Authorization` 將要求標頭設定為 `Bearer <TOKEN>` 。 權杖必須包含一或多個定義于宣告中的角色 `roles` 。 如果權杖中包含允許指定之資源上指定動作的角色，則會允許要求。

目前，針對指定角色所允許的動作會在 API 上 *全域* 套用。

## <a name="service-limits"></a>服務限制

* [**要求單位 (ru)**](../cosmos-db/concepts-limits.md) -您最多可以在入口網站中為 Azure API for FHIR 設定 10000 ru。 您至少需要 400 ru 或 10 ru/GB （以較大者為准）。 如果您需要超過 10000 ru，您可以放入支援票證，以增加這項功能。 可用的最大值為1000000。

* **並行連接** 和 **實例** -根據預設，在叢集中的兩個實例上有五個並行連線 (總共) 個並行要求。 如果您認為您需要更多的並行要求，請開啟支援票證，並提供您需求的詳細資料。

* 套件組合 **大小**-每個套件組合僅限500個專案。

* **資料大小** -資料/檔必須稍微小於 2 MB。

## <a name="performance-expectations"></a>效能期望

系統的效能取決於 ru 數目、並行連接，以及您正在執行的作業類型 (Put、Post 等 ) 。 以下是您可以根據設定的 ru 來預期的一些一般範圍。 一般而言，效能會隨著 ru 的增加而呈線性調整：

| ru 數目 | 資源/秒 |
|----------|---------------|
| 400      | 5-10          |
| 1,000    | 100-150       |
| 10,000   | 225-400       |
| 100,000  | 2500-4000   |

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解 Azure API for FHIR 中支援的 FHIR 功能。 接下來，部署 Azure API for FHIR。
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)