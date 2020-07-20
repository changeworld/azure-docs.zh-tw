---
title: Azure Cosmos DB etcd API 簡介
description: 本文提供 Azure Cosmos DB 中 etcd API 的概觀和主要優點
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 208f0d73b4c8bb11adc9c73796ebc6a622f5ed50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85118163"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Azure Cosmos DB etcd API 簡介 (預覽)

Azure Cosmos DB 是 Microsoft 全域發佈的多模型資料庫服務，適用於任務關鍵性應用程式。 其提供一站式全域散發、彈性調整的輸送量和儲存體、達到第 99 個百分位數的個位數毫秒延遲，以及保證的高可用性，全部都由領先業界的 SLA 所支援。

[Etcd](https://github.com/etcd-io/etcd) 是分散式索引鍵/值存放區。 在 [Kubernetes](https://kubernetes.io/) 中，etcd 會用來儲存 Kubernetes 叢集的狀態和設定。 對於 Kubernetes 叢集的整體叢集健康情況、可擴縮性、彈性可用性和效能而言，確保 etcd 的可用性、可靠性和效能非常重要。

Azure Cosmos DB 中的 etcd API 可讓您使用 Azure Cosmos DB 作為 Azure Kubernetes 的後端存放區。 Azure Cosmos DB 中的 etcd API 目前為預覽狀態。 Azure Cosmos DB 會執行 etcd 有線通訊協定。 若在 Azure Cosmos DB 中使用 etcd API，開發人員就會自動取得高度可靠、[可用](high-availability.md)且[全域散發](distribute-data-globally.md)的 Kubernetes。 此 API 可讓開發人員在完全受控的雲端原生 PaaS 服務上調整 Kubernetes 狀態管理。 

> [!NOTE]
> 不同於 Azure Cosmos DB 中的其他 API，您無法透過 Azure 入口網站、CLI 或 SDK 來佈建 etcd API 帳戶。 您只能藉由部署 Resource Manager 範本來佈建 etcd API 帳戶；如需詳細步驟，請參閱[如何使用 Azure Cosmos DB 佈建 Azure Kubernetes](bootstrap-kubernetes-cluster.md) 一文。 Azure Cosmos DB etcd API 目前以有限預覽形式提供。 您可以藉由填寫註冊表單來[註冊預覽版](https://aka.ms/cosmosetcdapi-signup)。

## <a name="wire-level-compatibility"></a>連線層級相容性

Azure Cosmos DB 會實作 etcd 第 3 版的有線通訊協定，讓[主要節點](https://kubernetes.io/docs/concepts/overview/components/)的 API 伺服器使用 Azure Cosmos DB 時，就如同在本機安裝的 etcd 環境中一樣。 etcd API 支援 TLS 相互驗證。 

下圖顯示 Kubernetes 叢集的元件。 在叢集主機中，API 伺服器會使用 Azure Cosmos DB etcd API，而不是本機安裝的 etcd。 

:::image type="content" source="./media/etcd-api-introduction/etcd-api-wire-protocol.png" alt-text="實作 etcd 有線通訊協定的 Azure Cosmos DB" border="false":::

## <a name="key-benefits"></a>主要權益

### <a name="no-etcd-operations-management"></a>無 etcd 作業管理

作為完全受控的原生雲端服務，Azure Cosmos DB 不再需要 Kubernetes 開發人員來設定和管理 etcd。 Azure Cosmos DB 中的 etcd API 可進行調整、具高可用性、可容錯，且提供高效能。 在多個節點間設定複寫、執行輪流更新、安全性修補程式，以及監視 etcd 健康情況的額外負荷，都會由 Azure Cosmos DB 來處理。

### <a name="global-distribution--high-availability"></a>全域散發和高可用性 

藉由使用 etcd API，Azure Cosmos DB 可保證單一區域中的資料讀取和寫入具有 99.99% 可用性，而在跨多個區域的案例中具有 99.999% 可用性。 

### <a name="elastic-scalability"></a>彈性延展性

Azure Cosmos DB 為跨不同區域的讀取和寫入要求提供彈性的可擴縮性。
隨著 Kubernetes 叢集成長，Azure Cosmos DB 中的 etcd API 帳戶會彈性地進行調整，而且不會發生停機狀況。 將 etcd 資料儲存在 Azure Cosmos DB 中 (而不是 Kubernetes 主要節點) 也可以提供更有彈性的主要節點調整。 

### <a name="security--enterprise-readiness"></a>安全性與企業整備

當 etcd 資料儲存在 Azure Cosmos DB 中時，Kubernetes 開發人員會自動取得 Azure Cosmos DB 支援的[內建待用加密](database-encryption-at-rest.md)、[認證和合規性](compliance.md)及[備份和還原功能](online-backup-and-restore.md)。 

## <a name="next-steps"></a>後續步驟

* [如何使用採用 Azure Cosmos DB 的 Azure Kubernetes](bootstrap-kubernetes-cluster.md)
* [Azure Cosmos DB 的主要優點](introduction.md)
* [AKS 引擎快速入門指南](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)