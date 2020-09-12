---
title: Azure 中支援的 FHIR 功能-Azure API for FHIR
description: 本文說明在 Azure API for FHIR 中執行的 FHIR 規格功能
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: afb4026a7865f2cc8f831d8d1d7b1d332014d310
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007565"
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
| 建立                         | 是       | 是       | 是       | 支援 POST/PUT                               |
| 建立 (條件式)            | 是       | 是       | 是       |                                                     |
| 搜尋                         | Partial   | Partial   | Partial   | 請參閱下方                                           |
| 連鎖搜尋                 | 否        | 是       | 否        |                                           |
| 反向連結搜尋         | 否        | 否        | 否        |                                            |
| capabilities                   | 是       | 是       | 是       |                                                     |
| 批次                          | 是       | 是       | 是       |                                                     |
| 交易                    | 否        | 是       | 否        |                                                     |
| history                        | 是       | 是       | 是       |                                                     |
| 分頁                         | Partial   | Partial   | Partial   | `self``next`支援和                     |
| 仲介                 | 否        | 否        | 否        |                                                     |

## <a name="search"></a>搜尋

支援所有搜尋參數類型。 

| 搜尋參數類型 | 支援-PaaS | 支援的作業系統 (SQL)  | 支援的-OSS (Cosmos DB)  | 註解 |
|-----------------------|-----------|-----------|-----------|---------|
| Number                | 是       | 是       | 是       |         |
| Date/DateTime         | 是       | 是       | 是       |         |
| String                | 是       | 是       | 是       |         |
| Token                 | 是       | 是       | 是       |         |
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
| `_profile`              | 是       | 是       | 是       |         |
| `_security`             | 是       | 是       | 是       |         |
| `_text`                 | 否        | 否        | 否        |         |
| `_content`              | 否        | 否        | 否        |         |
| `_list`                 | 是       | 是       | 是       |         |
| `_has`                  | 否        | 否        | 否        |         |
| `_type`                 | 是       | 是       | 是       |         |
| `_query`                | 否        | 否        | 否        |         |

| 搜尋作業       | 支援-PaaS | 支援的作業系統 (SQL)  | 支援的-OSS (Cosmos DB)  | 註解 |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | 否        | 否        | 否        |         |
| `_sort`                 | Partial        | Partial   | Partial        |   支援 `_sort=_lastUpdated`       |
| `_score`                | 否        | 否        | 否        |         |
| `_count`                | 是       | 是       | 是       |         |
| `_summary`              | 部分   | Partial   | Partial   | 支援 `_summary=count` |
| `_include`              | 否        | 是       | 否        |         |
| `_revinclude`           | 否        | 是       | 否        | 包含的專案限制為100。 |
| `_contained`            | 否        | 否        | 否        |         |
| `_elements`             | 是        | 是        | 是        |         |

## <a name="extended-operations"></a>擴充作業

擴充 RESTful API 所支援的所有作業。

| 搜尋參數類型 | 支援-PaaS | 支援的作業系統 (SQL)  | 支援的-OSS (Cosmos DB)  | 註解 |
|-----------------------|-----------|-----------|-----------|---------|
| $export (整個系統)                 | 是       | 是       | 是       |         |
| 患者/$export         | 是       | 是       | 是       |         |
| 群組/$export               | 是       | 是       | 是       |         |

## <a name="persistence"></a>持續性

Microsoft FHIR Server 具有可插入的持續性模組 (請參閱 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)) 。

目前 FHIR 伺服器開放原始碼程式碼包含 [Azure Cosmos DB](../cosmos-db/index-overview.md) 和 [SQL Database](https://azure.microsoft.com/services/sql-database/)的執行。

Cosmos DB 是全域散發的多模型 (SQL API、MongoDB API 等 ) 資料庫。 它支援不同的 [一致性層級](../cosmos-db/consistency-levels.md)。 預設部署範本會以一致性設定 FHIR 伺服器 `Strong` ，但您可以修改一致性原則， (通常會使用要求標頭，以要求為基礎放寬) 的要求 `x-ms-consistency-level` 。

## <a name="role-based-access-control"></a>角色型存取控制

FHIR 伺服器使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 進行存取控制。 具體而言，如果設定參數設定為，則會強制執行以角色為基礎的存取控制 (RBAC) ， `FhirServer:Security:Enabled` `true` 而且除了 `/metadata` FHIR 伺服器) 以外 (的所有要求都必須 `Authorization` 將要求標頭設定為 `Bearer <TOKEN>` 。 權杖必須包含一或多個定義于宣告中的角色 `roles` 。 如果權杖中包含允許指定之資源上指定動作的角色，則會允許要求。

目前，針對指定角色所允許的動作會在 API 上 *全域* 套用。

## <a name="next-steps"></a>接下來的步驟

在本文中，您已瞭解 Azure API for FHIR 中支援的 FHIR 功能。 接下來，部署 Azure API for FHIR。
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)
