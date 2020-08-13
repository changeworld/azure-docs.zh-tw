---
title: Azure 中支援的 FHIR 功能-Azure API for FHIR
description: 本文說明在 Azure API for FHIR 中實作為 FHIR 規格的哪些功能
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 0a24339d728c43817b6a7ae6eac8782ad0e27b09
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142514"
---
# <a name="features"></a>特性

Azure API for FHIR 提供適用于 Azure 的 Microsoft FHIR Server 完全受控部署。 伺服器是 [FHIR](https://hl7.org/fhir) 標準的執行。 本檔列出 FHIR 伺服器的主要功能。

## <a name="fhir-version"></a>FHIR 版本

支援的最新版本： `4.0.1`

目前也支援舊版，包括： `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 支援-PaaS | 支援-OSS (SQL)  | 支援-OSS (Cosmos DB)  | 註解                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 讀取                           | 是       | 是       | 是       |                                                     |
| vread                          | 是       | 是       | 是       |                                                     |
| update                         | 是       | 是       | 是       |                                                     |
| 使用開放式鎖定進行更新 | 是       | 是       | 是       |                                                     |
| 更新 (條件)            | 是       | 是       | 是       |                                                     |
| 跳                          | 否        | 否        | 否        |                                                     |
| delete                         | 是       | 是       | 是       |                                                     |
| delete (條件)            | 否        | 否        | 否        |                                                     |
| 建立                         | 是       | 是       | 是       | 同時支援 POST/PUT                               |
| 建立 (條件式)            | 是       | 是       | 是       |                                                     |
| 搜尋                         | 部分   | 部分   | 部分   | 請參閱下方                                           |
| 連鎖搜尋                 | 否        | 是       | 否        |                                           |
| 反向連結搜尋         | 否        | 否        | 否        |                                            |
| capabilities                   | 是       | 是       | 是       |                                                     |
| 批次                          | 是       | 是       | 是       |                                                     |
| 交易                    | 否        | 是       | 否        |                                                     |
| history                        | 是       | 是       | 是       |                                                     |
| 分頁                         | 部分   | 部分   | 部分   | `self``next`支援和                     |
| 媒介                 | 否        | 否        | 否        |                                                     |

## <a name="search"></a>搜尋

支援所有搜尋參數類型。 

| 搜尋參數類型 | 支援-PaaS | 支援-OSS (SQL)  | 支援-OSS (Cosmos DB)  | 註解 |
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


| 修飾詞             | 支援-PaaS | 支援-OSS (SQL)  | 支援-OSS (Cosmos DB)  | 註解 |
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

| 一般搜尋參數 | 支援-PaaS | 支援-OSS (SQL)  | 支援-OSS (Cosmos DB)  | 註解 |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | 是       | 是       | 是       |         |
| `_lastUpdated`          | 是       | 是       | 是       |         |
| `_tag`                  | 是       | 是       | 是       |         |
| `_profile`              | 是       | 是       | 是       |         |
| `_security`             | 是       | 是       | 是       |         |
| `_text`                 | 否        | 否        | 否        |         |
| `_content`              | 否        | 否        | 否        |         |
| `_list`                 | 否        | 是       | 是       |         |
| `_has`                  | 否        | 否        | 否        |         |
| `_type`                 | 是       | 是       | 是       |         |
| `_query`                | 否        | 否        | 否        |         |

| 搜尋作業       | 支援-PaaS | 支援-OSS (SQL)  | 支援-OSS (Cosmos DB)  | 註解 |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | 否        | 否        | 否        |         |
| `_sort`                 | 否        | 否        | 否        |         |
| `_score`                | 否        | 否        | 否        |         |
| `_count`                | 是       | 是       | 是       |         |
| `_summary`              | 部分   | 部分   | 部分   | 支援 `_summary=count` |
| `_include`              | 否        | 是       | 否        |         |
| `_revinclude`           | 否        | 是       | 否        | 包含的專案限制為100。 |
| `_contained`            | 否        | 否        | 否        |         |
| `_elements`             | 否        | 否        | 否        |         |

## <a name="persistence"></a>持續性

Microsoft FHIR Server 具有可插入的持續性模組 (請參閱 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)) 。

目前，FHIR 伺服器的開放原始碼程式碼包含 [Azure Cosmos DB](../cosmos-db/index-overview.md) 和 [SQL Database](https://azure.microsoft.com/services/sql-database/)的執行。

Cosmos DB 是全域散發的多模型 (SQL API、MongoDB API 等 ) 資料庫。 它支援不同的 [一致性層級](../cosmos-db/consistency-levels.md)。 預設部署範本會設定具有一致性的 FHIR 伺服器 `Strong` ，但您可以修改一致性原則， (通常會使用 `x-ms-consistency-level` 要求標頭以要求為基礎，在要求上進行寬鬆的) 。

## <a name="role-based-access-control"></a>角色型存取控制

FHIR 伺服器會使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 進行存取控制。 具體而言，會強制執行以角色為基礎的)  (存取控制，如果 `FhirServer:Security:Enabled` 設定參數設為 `true` ，則 (除了 `/metadata` FHIR 伺服器) 以外的所有要求都必須 `Authorization` 將要求標頭設定為 `Bearer <TOKEN>` 。 權杖必須包含一或多個如宣告中所定義的角色 `roles` 。 如果權杖包含允許指定之資源上指定動作的角色，則會允許要求。

目前，針對指定角色所允許的動作會在 API 上 *全域* 套用。

## <a name="next-steps"></a>後續步驟

在本文中，您已閱讀 Azure API for FHIR 中支援的 FHIR 功能。 接下來，部署 Azure API for FHIR。
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)
