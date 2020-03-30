---
title: Azure 宇宙 DB 蝕刻 API 簡介
description: 本文概述了 Azure Cosmos DB 中蝕刻 API 的概述和關鍵優勢
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: acd87fac5ec2edc40d27d98f073e13c0acae8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498588"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Azure 宇宙 DB 蝕刻 API 簡介（預覽版）

Azure Cosmos DB 是 Microsoft 全域發佈的多模型資料庫服務，適用於任務關鍵性應用程式。 它提供交鑰匙全球分發、輸送量和存儲的彈性擴展、第 99 個百分位數的單位毫秒延遲，以及保證的高可用性，所有這些都由行業領先的 SLA 支援。

[蝕刻](https://github.com/etcd-io/etcd)是一個分散式金鑰/值存儲。 在[庫伯奈斯](https://kubernetes.io/)，蝕刻用於存儲庫伯奈斯集群的狀態和配置。 確保蝕刻的可用性、可靠性和性能對於 Kubernetes 群集的整體群集運行狀況、可擴充性、彈性可用性和性能至關重要。 

Azure 宇宙 DB 中的蝕刻 API 允許您使用 Azure 宇宙 DB 作為[Azure 庫伯奈的](../aks/index.yml)後端存儲。 Azure Cosmos DB 中的 etcd API 目前為預覽狀態。 Azure 宇宙 DB 實現蝕刻線協定。 借助 Azure Cosmos DB 中的蝕刻 API，開發人員將自動獲得高度可靠、[可用](high-availability.md)、[全域分佈](distribute-data-globally.md)的 Kubernetes。 此 API 允許開發人員在完全託管的雲本機 PaaS 服務上擴展 Kubernetes 狀態管理。 

> [!NOTE]
> 與 Azure Cosmos DB 中的其他 API 不同，不能通過 Azure 門戶、CLI 或 SDK 預配蝕刻 API 帳戶。 您可以通過僅部署資源管理器範本來預配蝕刻 API 帳戶;有關詳細步驟，請參閱[如何使用 Azure Cosmos DB 提供 Azure 庫伯奈斯](bootstrap-kubernetes-cluster.md)。 Azure 宇宙 DB 蝕刻 API 當前處於有限預覽狀態。 您可以通過填寫註冊表單[來註冊預覽](https://aka.ms/cosmosetcdapi-signup)。

## <a name="wire-level-compatibility"></a>電線級相容性

Azure Cosmos DB 實現了蝕刻版本 3 的有線協定，並允許[主節點的](https://kubernetes.io/docs/concepts/overview/components/)API 伺服器使用 Azure Cosmos DB，就像在本地安裝的蝕刻環境中一樣。 蝕刻 API 支援 TLS 相互身份驗證。 

下圖顯示了庫伯內斯群集的元件。 在群集主機中，API 伺服器使用 Azure Cosmos DB 蝕刻 API，而不是本地安裝的等號。 

![Azure 宇宙 DB 實現蝕刻線協定](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>主要權益

### <a name="no-etcd-operations-management"></a>無蝕刻操作管理

作為完全託管的本機雲服務，Azure Cosmos DB 無需 Kubernetes 開發人員來設置和管理等項。 Azure Cosmos DB 中的蝕刻 API 可擴展、高可用性、容錯，並提供高性能。 設置跨多個節點進行複製、執行滾動更新、安全修補程式和監視等運行狀況的開銷由 Azure Cosmos DB 處理。

### <a name="global-distribution--high-availability"></a>全球分銷&高可用性 

通過使用蝕刻 API，Azure Cosmos DB 可確保單個區域中的資料讀取和寫入 99.99% 的可用性，以及跨多個區域 99.99% 的可用性。 

### <a name="elastic-scalability"></a>彈性延展性

Azure Cosmos DB 為不同區域的讀寫請求提供彈性可伸縮性。
隨著 Kubernetes 群集的增長，Azure Cosmos DB 中的蝕刻 API 帳戶彈性擴展，而不會產生任何停機時間。 在 Azure Cosmos DB 中存儲蝕刻資料，而不是 Kubernetes 主節點還可以實現更靈活的主節點縮放。 

### <a name="security--enterprise-readiness"></a>安全&企業就緒性

當蝕刻資料存儲在 Azure Cosmos DB 中時，Kubernetes 開發人員會自動獲取 Azure Cosmos DB 支援的[內置加密](database-encryption-at-rest.md)、[認證和合規性](compliance.md)以及[備份和還原功能](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)。 

## <a name="next-steps"></a>後續步驟

* [如何使用採用 Azure Cosmos DB 的 Azure Kubernetes](bootstrap-kubernetes-cluster.md)
* [Azure 宇宙資料庫的主要優勢](introduction.md)
* [AKS 發動機快速起動指南](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)