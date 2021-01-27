---
title: '將串流分析作業連線至 Azure 虛擬網路中的資源 (VNET) '
description: 本文說明如何將 Azure 串流分析作業與 VNET 中的資源連線。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 4701cb4122b4196b08b2a427b34d49c7784b91a7
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878234"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>將串流分析作業連線至 Azure 虛擬網路中的資源 (VNet) 

您的串流分析工作會對您的輸入進行輸出連線，並輸出 Azure 資源以即時處理資料並產生結果。 這些輸入和輸出資源 (例如，Azure 事件中樞和 Azure SQL Database) 可能位於 Azure 防火牆後方，或在 Azure 虛擬網路中 (VNet) 。 串流分析服務會從無法直接包含在網路規則中的網路運作。

不過，在這類情況下，有兩種方式可以安全地將串流分析作業連線至您的輸入和輸出資源。
* 使用串流分析叢集中的私人端點。
* 使用「受控識別驗證」模式與「允許信任的服務」網路設定結合。

您的串流分析作業不接受任何輸入連線。

## <a name="private-endpoints-in-stream-analytics-clusters"></a>串流分析叢集中的私人端點。
[串流分析](./cluster-overview.md) 叢集是單一租使用者專用計算叢集，您可以在其中執行串流分析作業。 您可以在串流分析叢集中建立受控私人端點，以允許在您的叢集上執行的任何工作，對您的輸入和輸出資源進行安全的輸出連接。

在串流分析叢集中建立私人端點是 [兩個步驟的操作](./private-endpoints.md)。 此選項最適合用於中型至大型串流工作負載，因為串流分析叢集的最小大小為 36 su (不過，不同訂用帳戶或環境（例如開發、測試和生產) 等環境中的不同作業可共用 36 su）。

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>使用「允許信任的服務」設定進行受控識別驗證
某些 Azure 服務提供 [ **允許信任的 Microsoft 服務** 網路] 設定（啟用時），可讓您的串流分析作業使用強式驗證安全地連線至您的資源。 此選項可讓您將作業連接到您的輸入和輸出資源，而不需要串流分析叢集和私人端點。 設定您的作業使用這項技術是一個2步驟的作業：
* 在串流分析作業中設定輸入或輸出時，請使用受控識別驗證模式。
* 藉由將 Azure 角色指派給作業系統指派的受控識別，來授與您的特定串流分析作業對您目標資源的明確存取權。 

啟用 [ **允許信任的 Microsoft 服務** ] 並不會授與任何作業的總存取權。 這可讓您完全控制哪些特定的串流分析工作可以安全地存取您的資源。 

您的作業可以使用這項技術連接到下列 Azure 服務：
1. [Blob 儲存體或 Azure Data Lake Storage Gen2](./blob-output-managed-identity.md) -可以是您作業的儲存體帳戶、串流輸入或輸出。
2. [Azure 事件中樞](./event-hubs-managed-identity.md) -可以是您作業的串流輸入或輸出。

如果您的作業需要連線至其他輸入或輸出類型，您可以先從串流分析寫入至事件中樞輸出，然後再使用 Azure Functions 寫入至您選擇的任何目的地。 如果您想要直接從串流分析寫入至 VNet 或防火牆中保護的其他輸出類型，則唯一的選項是使用串流分析叢集中的私人端點。

## <a name="next-steps"></a>後續步驟

* [在串流分析叢集中建立和移除私人端點](./private-endpoints.md)
* [使用受控識別驗證連接到 VNet 中的事件中樞](./event-hubs-managed-identity.md)
* [使用受控識別驗證連線至 Blob 儲存體，並在 VNet 中 ADLS Gen2](./blob-output-managed-identity.md)