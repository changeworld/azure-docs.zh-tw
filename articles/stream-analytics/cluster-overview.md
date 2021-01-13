---
title: Azure 串流分析叢集概觀 (預覽)
description: 深入了解串流分析叢集的單一租用戶的專用供應項目。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 75a95fa4ad36b69ca920f527060644dcc5264ce8
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019528"
---
# <a name="overview-of-azure-stream-analytics-cluster-preview"></a>Azure 串流分析叢集概觀 (預覽)

Azure 串流分析叢集提供單一租用戶部署，可用於複雜且高需求的串流案例。 在完整的級別下，串流分析叢集每秒可即時處理超過 200 MB 的資料。 在專用叢集上執行的串流分析作業可以運用標準供應項目中的所有功能，並針對對您的輸入和輸出提供私人連結連線支援。

串流分析叢集是由串流單位 (SU) 來計費，代表配置給叢集的 CPU 和記憶體資源量。 標準和專用供應項目的串流單元是相同的。 您可以為每個叢集購買 36、72、108、144、180 或 216 個 SU。 串流分析叢集可以作為組織的串流平台，而且可以由處理各種使用案例的不同小組共用。

## <a name="what-are-stream-analytics-clusters"></a>什麼是串流分析叢集

串流分析叢集是由與在多租用戶環境中執行串流分析作業的相同引擎所提供。 單一租用戶專用叢集具有下列功能：

* 使用單一租用戶裝載，不會受到其他租用戶的干擾。 當流量激增時，您的資源會真正「獨立」並提供更佳的執行效能。

* 當串流使用量隨時間增加時，請將您的叢集調整為 36 到 216 個 SU。

* VNet 支援，可讓您的串流分析作業使用私人端點安全地連線到其他資源。

* 能夠在任何區域中撰寫 C# 使用者定義的函式和自訂還原序列化程式。

* 零維護成本，可讓您專心建置即時分析解決方案。

## <a name="how-to-get-started"></a>如何開始使用

您可以透過 [Azure 入口網站](https://aka.ms/asaclustercreateportal)[建立串流分析叢集](create-cluster.md)。 如果您有任何疑問或需要上線的相關協助，可以與[串流分析小組](mailto:askasa@microsoft.com)連絡。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>我該選擇串流分析叢集或串流分析作業？

最簡單的入門方式是建立並開發串流分析作業以熟悉服務，並了解其如何符合您的分析需求。

串流分析作業單獨不支援 VNet。 如果您的輸入或輸出受到防火牆或 Azure 虛擬網路保護，您有下列兩個選項：

* 如果本機電腦可存取 VNet 所保護的輸入和輸出資源 (例如 Azure 事件中樞或 Azure SQL Database)，您可以 在本機電腦上[安裝適用於 Visual Studio 的 Azure 串流分析工具](stream-analytics-tools-for-visual-studio-install.md)。 您可以在裝置上[執行本機開發和測試串流分析作業](stream-analytics-live-data-local-testing.md)，而不會產生任何費用。 準備好要在您的架構中使用串流分析後，就可以建立串流分析叢集、設定私人端點，並大規模執行您的作業。

* 您可以建立串流分析叢集、使用管線的必要私用端點來設定叢集，並在叢集上執行您的串流分析作業。

### <a name="what-performance-can-i-expect"></a>我可以預期的效能為何？

標準和專用供應項目的 SU 是相同的。 使用完整 36 個 SU 叢集的單一作業可以達到每秒大約 36 MB 輸送量 (包括毫秒延遲)。 確切的數目取決於事件格式和分析類型。 因為是專用的，串流分析叢集能提供更可靠的效能保證。 您叢集上執行的所有作業都只屬於您。

### <a name="can-i-scale-my-cluster"></a>我可以調整叢集嗎？

可以。 您可以輕鬆地設定叢集容量，以便視需要[擴大或縮減](scale-cluster.md)，以符合您的變更需求。

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>我可以在我所建立的這些新叢集上執行現有的作業嗎？

可以。 您可以將現有的作業連結至新建立的串流分析叢集，並照常執行。 不需要從頭重新建立現有的串流分析作業。

### <a name="how-much-will-these-clusters-cost-me"></a>這些叢集會花費多少成本？

您的串流分析叢集會根據所選的 SU 容量收費。 叢集會按小時計費，而且在這些叢集中執行的每項作業都不會產生額外費用。 如需私人端點計費更新，請參閱[私人連結服務定價頁面](https://azure.microsoft.com/pricing/details/private-link/)。

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>我可以從我的串流分析叢集私人連線到哪些輸入和輸出？

串流分析支援各種輸入和輸出類型。 您可以在叢集中[建立私人端點](private-endpoints.md)，以允許作業存取輸入和輸出資源。 目前，Azure SQL Database、Azure 儲存體、Azure Data Lake Storage Gen2、Azure 事件中樞和 Azure 服務匯流排都是支援的服務，而其他類型將會在不久後新增。 

## <a name="next-steps"></a>後續步驟

現在您已大致了解 Azure 串流分析叢集。 接下來，您可以建立叢集並執行串流分析作業： 

* [建立串流分析叢集](create-cluster.md)
* [在 Azure 串流分析叢集中管理私人端點](private-endpoints.md)
* [管理串流分析叢集中的串流分析作業](manage-jobs-cluster.md)
* [建立串流分析作業](stream-analytics-quick-create-portal.md)
