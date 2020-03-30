---
title: Azure 安全中心和 Azure 庫伯奈斯服務
description: 瞭解 Azure 安全中心與 Azure 庫伯奈斯服務的集成
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d1cd4691586b27282d221a19c5fb7a1af034ed6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125149"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Azure 庫伯奈斯服務集成安全中心

Azure 庫伯奈斯服務 （AKS） 是 Microsoft 用於開發、部署和管理容器化應用程式的託管服務。 

將 AKS 與 Azure 安全中心的標準層（請參閱[定價](security-center-pricing.md)）結合使用，以更深入地瞭解 AKS 節點、雲流量和安全控制。

使用 AKS 主節點已收集的資料，安全中心為您的 AKS 群集帶來安全優勢。 

![Azure 安全中心和 Azure 庫伯奈斯服務 （AKS） 高級概述](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

這兩個工具共同構成了最佳的雲原生 Kubernetes 安全產品。 

## <a name="benefits-of-integration"></a>整合的優點

將兩種服務結合使用可提供：

* **安全建議**- 安全中心標識您的 AKS 資源並對其進行分類：從群集到單個虛擬機器。 然後，您可以查看每個資源的安全建議。 有關詳細資訊，請參閱[建議參考清單中的容器建議](recommendations-reference.md#recs-computeapp)。 

* **環境強化**- 安全中心不斷監控 Kubernetes 群集和 Docker 配置的配置。 然後，它生成反映行業標準的安全建議。

* **運行時保護**- 通過持續分析以下 AKS 源，安全中心會提醒您主機和 AKS 群集級別檢測到的威脅*和*惡意活動：
    * 原始安全事件，如網路資料和進程創建
    * 庫伯內斯審計日誌

    有關詳細資訊，請參閱[Azure 容器的威脅保護](threat-protection.md#azure-containers)

    有關可能警報的清單，請參閱警報參考表中的這些部分[：AKS 群集級別警報](alerts-reference.md#alerts-akscluster)和[容器主機分級警報](alerts-reference.md#alerts-containerhost)。  

![Azure 安全中心和 Azure 庫伯奈斯服務 （AKS） 詳解](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Azure 安全中心從庫伯奈斯環境掃描的某些資料可能包含敏感資訊。


## <a name="next-steps"></a>後續步驟

要瞭解有關安全中心的容器安全功能的更多，請參閱：

* [Azure 安全中心和容器安全性](container-security.md)

* [與 Azure Container Registry 整合](azure-container-registry-integration.md)

* [Microsoft 的資料管理](https://www.microsoft.com/trust-center/privacy/data-management)- 描述 Microsoft 服務（包括 Azure、Intune 和 Office 365）的資料策略、Microsoft 資料管理的詳細資訊以及影響資料的保留原則