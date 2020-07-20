---
title: Azure 中支援的 FHIR 功能-Azure API for FHIR
description: 本文說明在 Azure API for FHIR 中實作為 FHIR 規格的哪些功能
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 7f92395f19d84f904493af458d1334f8013fd263
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85808024"
---
# <a name="features"></a>特性

Azure API for FHIR 提供適用于 Azure 的 Microsoft FHIR Server 完全受控部署。 伺服器是[FHIR](https://hl7.org/fhir)標準的執行。 本檔列出 FHIR 伺服器的主要功能。

## <a name="fhir-version"></a>FHIR 版本

支援的最新版本：`4.0.1`

目前也支援舊版，包括：`3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 支援-PaaS | 支援-OSS （SQL） | 支援的作業系統（Cosmos DB） | 註解                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 讀取                           | Yes       | Yes       | Yes       |                                                     |
| vread                          | Yes       | Yes       | Yes       |                                                     |
| update                         | Yes       | Yes       | Yes       |                                                     |
| 使用開放式鎖定進行更新 | Yes       | Yes       | Yes       |                                                     |
| update （條件式）           | Yes       | Yes       | Yes       |                                                     |
| 跳                          | 否        | 否        | 否        |                                                     |
| delete                         | Yes       | Yes       | Yes       |                                                     |
| delete （條件式）           | 否        | 否        | 否        |                                                     |
| 建立                         | 是       | Yes       | Yes       | 同時支援 POST/PUT                               |
| 建立（條件式）           | Yes       | Yes       | Yes       |                                                     |
| 搜尋                         | Partial   | Partial   | Partial   | 請參閱下方                                           |
| 連鎖搜尋                 | No        | 是       | 否        |                                           |
| 反向連結搜尋         | 否        | 否        | 否        |                                            |
| capabilities                   | Yes       | Yes       | Yes       |                                                     |
| 批次                          | Yes       | Yes       | Yes       |                                                     |
| 交易                    | No        | 是       | 否        |                                                     |
| history                        | Yes       | Yes       | Yes       |                                                     |
| 分頁                         | Partial   | Partial   | Partial   | `self``next`支援和                     |
| 媒介                 | 否        | 否        | 否        |                                                     |

## <a name="search"></a>搜尋

支援所有搜尋參數類型。 

| 搜尋參數類型 | 支援-PaaS | 支援-OSS （SQL） | 支援的作業系統（Cosmos DB） | 註解 |
|-----------------------|-----------|-----------|-----------|---------|
| Number                | Yes       | Yes       | Yes       |         |
| Date/DateTime         | Yes       | Yes       | 是       |         |
| String                | Yes       | Yes       | Yes       |         |
| Token                 | Yes       | Yes       | Yes       |         |
| 參考             | Yes       | Yes       | Yes       |         |
| 複合             | Yes       | Yes       | Yes       |         |
| 數量              | Yes       | Yes       | Yes       |         |
| URI                   | Yes       | Yes       | Yes       |         |
| 特殊               | 否        | 否        | 否        |         |


| 修飾詞             | 支援-PaaS | 支援-OSS （SQL） | 支援的作業系統（Cosmos DB） | 註解 |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | 是       | Yes       | Yes       |         |
|`:exact`               | Yes       | Yes       | Yes       |         |
|`:contains`            | Yes       | Yes       | Yes       |         |
|`:text`                | Yes       | Yes       | Yes       |         |
|`:in`權杖          | 否        | 否        | 否        |         |
|`:below`權杖       | 否        | 否        | 否        |         |
|`:above`權杖       | 否        | 否        | 否        |         |
|`:not-in`權杖      | 否        | 否        | 否        |         |
|`:[type]`證明  | 否        | 否        | 否        |         |
|`:below`uri         | Yes       | Yes       | Yes       |         |
|`:not`                 | 否        | 否        | 否        |         |
|`:above`uri         | 否        | 否        | 否        | 問題[#158](https://github.com/Microsoft/fhir-server/issues/158) |

| 一般搜尋參數 | 支援-PaaS | 支援-OSS （SQL） | 支援的作業系統（Cosmos DB） | 註解 |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | 是       | Yes       | Yes       |         |
| `_lastUpdated`          | Yes       | Yes       | Yes       |         |
| `_tag`                  | Yes       | Yes       | Yes       |         |
| `_profile`              | Yes       | Yes       | Yes       |         |
| `_security`             | Yes       | Yes       | Yes       |         |
| `_text`                 | 否        | 否        | 否        |         |
| `_content`              | 否        | 否        | 否        |         |
| `_list`                 | 否        | 是       | Yes       |         |
| `_has`                  | 否        | 否        | 否        |         |
| `_type`                 | 是       | Yes       | Yes       |         |
| `_query`                | 否        | 否        | 否        |         |

| 搜尋作業       | 支援-PaaS | 支援-OSS （SQL） | 支援的作業系統（Cosmos DB） | 註解 |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | 否        | 否        | 否        |         |
| `_sort`                 | 否        | 否        | 否        |         |
| `_score`                | 否        | 否        | 否        |         |
| `_count`                | 是       | Yes       | 是       |         |
| `_summary`              | 部分   | Partial   | Partial   | 支援 `_summary=count` |
| `_include`              | 否        | 是       | 否        |         |
| `_revinclude`           | 否        | 否        | 否        |         |
| `_contained`            | 否        | 否        | 否        |         |
| `_elements`             | 否        | 否        | 否        |         |

## <a name="persistence"></a>持續性

Microsoft FHIR Server 具有可插入的持續性模組（請參閱 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ）。

目前，FHIR 伺服器的開放原始碼程式碼包含[Azure Cosmos DB](../cosmos-db/index-overview.md)和[SQL Database](https://azure.microsoft.com/services/sql-database/)的執行。

Cosmos DB 是全域散發的多模型（SQL API、MongoDB API 等）資料庫。 它支援不同的[一致性層級](../cosmos-db/consistency-levels.md)。 預設部署範本會設定具有一致性的 FHIR 伺服器 `Strong` ，但一致性原則可以使用 `x-ms-consistency-level` 要求標頭，依據要求來修改（通常是寬鬆的）。

## <a name="role-based-access-control"></a>角色型存取控制

FHIR 伺服器會使用[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)進行存取控制。 具體而言，會強制執行角色型存取控制（RBAC），如果 `FhirServer:Security:Enabled` 設定參數設為 `true` ，而且 FHIR 伺服器的所有要求（除外 `/metadata` ）都必須 `Authorization` 將要求標頭設定為 `Bearer <TOKEN>` 。 權杖必須包含一或多個如宣告中所定義的角色 `roles` 。 如果權杖包含允許指定之資源上指定動作的角色，則會允許要求。

目前，針對指定角色所允許的動作會在 API 上*全域*套用。

## <a name="next-steps"></a>後續步驟

在本文中，您已閱讀 Azure API for FHIR 中支援的 FHIR 功能。 接下來，部署 Azure API for FHIR。
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)
