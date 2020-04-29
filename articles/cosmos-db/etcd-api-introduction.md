---
title: Azure Cosmos DB etcd API 簡介
description: 本文提供 etcd API 在 Azure Cosmos DB 中的總覽和主要優點
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79498588"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Azure Cosmos DB etcd API （預覽）簡介

Azure Cosmos DB 是 Microsoft 全域發佈的多模型資料庫服務，適用於任務關鍵性應用程式。 它提供了全包式的全域散發、彈性調整的輸送量和儲存體、在第99個百分位數的單一數位毫秒延遲，以及保證的高可用性，全部都由領先業界的 SLA 所支援。

[Etcd](https://github.com/etcd-io/etcd)是分散式索引鍵/值存放區。 在[Kubernetes](https://kubernetes.io/)中，etcd 是用來儲存 Kubernetes 叢集的狀態和設定。 確保 etcd 的可用性、可靠性和效能對於 Kubernetes 叢集的整體叢集健全狀況、擴充性、彈性可用性和效能非常重要。 

Azure Cosmos DB 中的 etcd API 可讓您使用 Azure Cosmos DB 作為[Azure Kubernetes](../aks/index.yml)的後端存放區。 Azure Cosmos DB 中的 etcd API 目前為預覽狀態。 Azure Cosmos DB 會執行 etcd 有線通訊協定。 在 Azure Cosmos DB 中使用 etcd API，開發人員將會自動取得高度可靠、[可用](high-availability.md)、[全域散發](distribute-data-globally.md)的 Kubernetes。 此 API 可讓開發人員在完全受控的雲端原生 PaaS 服務上調整 Kubernetes 狀態管理。 

> [!NOTE]
> 不同于 Azure Cosmos DB 中的其他 Api，您無法透過 Azure 入口網站、CLI 或 Sdk 來布建 etcd API 帳戶。 您只能藉由部署 Resource Manager 範本來布建 etcd API 帳戶;如需詳細步驟，請參閱[如何使用 Azure Cosmos DB 布建 Azure Kubernetes](bootstrap-kubernetes-cluster.md)一文。 Azure Cosmos DB etcd API 目前處於有限預覽狀態。 您可以填寫註冊表單，[以註冊預覽](https://aka.ms/cosmosetcdapi-signup)。

## <a name="wire-level-compatibility"></a>連線層級相容性

Azure Cosmos DB 會執行 etcd 第3版的線路通訊協定，並允許[主要節點的](https://kubernetes.io/docs/concepts/overview/components/)API 伺服器使用 Azure Cosmos DB，就像在本機安裝的 etcd 環境中所做的一樣。 Etcd API 支援 TLS 相互驗證。 

下圖顯示 Kubernetes 叢集的元件。 在叢集主機中，API 伺服器會使用 Azure Cosmos DB etcd API，而不是本機安裝的 etcd。 

![Azure Cosmos DB 實施 etcd 的線路通訊協定](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>主要權益

### <a name="no-etcd-operations-management"></a>沒有 etcd 作業管理

做為完全受控的原生雲端服務，Azure Cosmos DB 不再需要 Kubernetes 開發人員設定和管理 etcd。 Azure Cosmos DB 中的 etcd API 可調整、高可用性、容錯，並提供高效能。 在多個節點間設定複寫、執行輪流更新、安全性修補程式，以及監視 etcd 健全狀況的額外負荷，都是由 Azure Cosmos DB 來處理。

### <a name="global-distribution--high-availability"></a>全域散發 & 高可用性 

藉由使用 etcd API，Azure Cosmos DB 保證在單一區域中讀取和寫入資料的99.99% 可用性，以及跨多個區域的99.999% 可用性。 

### <a name="elastic-scalability"></a>彈性延展性

Azure Cosmos DB 為跨不同區域的讀取和寫入要求提供彈性的擴充性。
隨著 Kubernetes 叢集成長，Azure Cosmos DB 彈性中的 etcd API 帳戶會進行調整，而不會造成任何停機時間。 將 etcd 資料儲存在 Azure Cosmos DB 中，而不是 Kubernetes 主要節點也可以提供更有彈性的主要節點調整。 

### <a name="security--enterprise-readiness"></a>安全性 & 企業就緒

當 etcd 資料儲存在 Azure Cosmos DB 中時，Kubernetes 開發人員會自動取得待用的[內建加密](database-encryption-at-rest.md)、[認證和合規性](compliance.md)，以及 Azure Cosmos DB 所支援的[備份和還原功能](online-backup-and-restore.md)。 

## <a name="next-steps"></a>後續步驟

* [如何使用採用 Azure Cosmos DB 的 Azure Kubernetes](bootstrap-kubernetes-cluster.md)
* [Azure Cosmos DB 的主要優點](introduction.md)
* [AKS 引擎快速入門手冊](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)