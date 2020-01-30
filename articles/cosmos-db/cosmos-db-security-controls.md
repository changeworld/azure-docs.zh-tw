---
title: Azure Cosmos DB 的安全性控制項
description: 取得安全性控制項（例如網路、監視、身分識別和資料保護）的檢查清單，以評估 Azure Cosmos DB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.openlocfilehash: d06afb3670b1c3af3f1acf0bd690c1e3e266a11c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771797"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Azure Cosmos DB 的安全性控制項

本文記載內建在 Azure Cosmos DB 中的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| VNet 插入支援| 是 | 使用 VNet 服務端點，您可以設定 Azure Cosmos DB 帳戶，只允許從虛擬網路（VNet）的特定子網進行存取。 您也可以結合 VNet 存取與防火牆規則。 若要深入瞭解，請參閱[從虛擬網路存取 Azure Cosmos DB](VNet-service-endpoint.md)。 |
| 網路隔離和防火牆支援| 是 | 有了防火牆支援，您可以設定 Azure Cosmos 帳戶，只允許來自一組已核准的 IP 位址、IP 位址和/或雲端服務的某個範圍的存取權。 若要深入瞭解，請參閱[在 Azure Cosmos DB 中設定 IP 防火牆](how-to-configure-firewall.md)。|
| 強制通道支援| 是 | 可以在虛擬機器所在的 VNet 上，于用戶端設定。   |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援（Log analytics、App insights 等）| 是 | 所有傳送至 Azure Cosmos DB 的要求都會被記錄下來。 支援[Azure 監視](../azure-monitor/overview.md)、azure 計量、Azure Audit 記錄。  您可以記錄對應于資料平面要求、查詢執行時間統計資料、查詢文字、MongoDB 要求的資訊。 您也可以設定警示。 |
| 控制和管理平面記錄和審核| 是 | 適用于帳戶層級作業（例如防火牆、Vnet、金鑰存取和 IAM）的 Azure 活動記錄。 |
| 資料平面記錄和審核 | 是 | 容器層級作業的診斷監視記錄，例如建立容器、布建輸送量、編制索引原則，以及檔上的 CRUD 作業。 |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 是，在資料庫帳戶層級;在資料平面層級，Cosmos DB 會使用資源權杖和金鑰存取。 |
| 授權| 是 | 在具有主要金鑰（主要和次要）和資源權杖的 Azure Cosmos 帳戶上受到支援。 您可以使用主要金鑰來取得資料的讀取/寫入或唯讀存取權。 資源權杖允許限時存取檔和容器之類的資源。 |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | 是 | 預設會加密所有 Azure Cosmos 資料庫和備份;請參閱[Azure Cosmos DB 中的資料加密](database-encryption-at-rest.md)。 |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | 是 | 請參閱[為您的 Azure Cosmos DB 帳戶設定客戶管理的金鑰](how-to-setup-cmk.md)  |
| 資料行層級加密（Azure 資料服務）| 是 | 僅適用于資料表 API Premium。 並非所有 Api 都支援這項功能。 請參閱[Azure Cosmos DB 簡介：資料表 API](table-introduction.md)。 |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| 是 | 所有 Azure Cosmos DB 資料都會在傳輸時加密。 |
| API 呼叫加密| 是 | Azure Cosmos DB 的所有連接都支援 HTTPS。 Azure Cosmos DB 也支援 TLS 1.2。<br>您可以強制執行最低的 TLS 版本伺服器端。 若要這麼做，請洽詢[azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援（設定的版本設定等）| 否  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Cosmos DB 的其他安全性控制

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 跨原始來源資源分享（CORS） | 是 | 請參閱[設定跨原始來源資源分享（CORS）](how-to-configure-cross-origin-resource-sharing.md)。 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。