---
title: Azure 資訊安全中心和 Azure Kubernetes Service
description: 瞭解 Azure 資訊安全中心與 Azure Kubernetes Services 的整合
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 7f43f50fc35b5ecbc9720224036ea13e2ba753f5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894938"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Azure Kubernetes Services 與 Security Center 整合

Azure Kubernetes Service (AKS) 是 Microsoft 用來開發、部署和管理容器化應用程式的受控服務。 

啟用 **適用于 Kubernetes 的 Azure Defender** ，以深入瞭解您的 AKS 節點、雲端流量和安全性控制。

總而言之，Security Center 和 AKS 形成最佳的雲端原生 Kubernetes 安全性產品。

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>資訊安全中心的 Kubernetes 防護有哪些元件？

Kubernetes 的安全中心保護是由兩個元素的組合提供：

- **Azure 資訊安全中心的虛擬機器威脅防護** -使用資訊安全中心在其他 vm 上使用的相同 Log Analytics 代理程式，資訊安全中心可顯示 AKS 節點發生安全性問題。 代理程式也會監視容器特定的分析。

- **Azure 資訊安全中心的選擇性 Azure Defender For Kubernetes 方案** -Kubernetes 方案會透過 AKS 服務從 Kubernetes 子系統接收記錄和資訊。 這些記錄已可透過 AKS 服務在 Azure 中使用。 當您啟用適用于 Kubernetes 的 Azure Defender 時，您會將記錄的存取權授與「安全性中心」。 如此一來，使用 AKS 主要節點收集的資料，安全性中心就能為您的 AKS 叢集帶來安全性優勢。 Azure 資訊安全中心從 Kubernetes 環境掃描的部分資料可能包含機密資訊。


## <a name="next-steps"></a>下一步

若要深入瞭解資訊安全中心的容器安全性功能，請參閱：

* [Azure 資訊安全中心與容器安全性](container-security.md)

* [與 Azure Container Registry 整合](azure-container-registry-integration.md)

* [Microsoft 的資料管理](https://www.microsoft.com/trust-center/privacy/data-management) -說明 Microsoft 服務 (的資料原則，包括 Azure、Intune 和 Microsoft 365) 、Microsoft 資料管理的詳細資料，以及影響資料的保留原則
