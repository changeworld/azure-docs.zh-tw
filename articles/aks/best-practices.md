---
title: Azure Kubernetes Service (AKS) 的最佳做法
description: 在 Azure Kubernetes Service (AKS) 中建置和管理應用程式的叢集操作員和開發人員最佳做法集合
services: container-service
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: 627eeffd18a479486e5a682da06bf89cd5f8f0e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77596312"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中建置和管理應用程式的叢集操作員和開發人員最佳做法

若要在 Azure Kubernetes Service (AKS) 中成功建置和執行應用程式，須了解和實作一些重要考量。 這些區域包含多租用戶與排程器功能、叢集與 Pod 安全性，或商務持續性和災害復原。 下列最佳做法分組可協助叢集操作員和開發人員了解每個區域的個別考量，並實作適當的功能。

這些最佳做法與概念性文章是與 AKS 產品群組、工程團隊以及包括 Global Black Belts (GBB) 的專業團隊共同撰寫的。

## <a name="cluster-operator-best-practices"></a>叢集操作員最佳做法

身為叢集操作員，須與應用程式擁有者和開發人員合作，以了解其需求。 您接著可以視需要使用下列最佳做法來設定 AKS 叢集。

**多租使用者**

* [叢集隔離的最佳做法](operator-best-practices-cluster-isolation.md)
    * 包括多租用戶核心元件，以及利用命名空間進行邏輯隔離。
* [基本排程器功能的最佳做法](operator-best-practices-scheduler.md)
    * 包括使用資源配額與 Pod 中斷預算。
* [進階排程器功能的最佳做法](operator-best-practices-advanced-scheduler.md)
    * 包括使用污點與容忍、節點選取器與親和性，以及Inter-pod 親和性和反親和性。
* [驗證和授權的最佳做法](operator-best-practices-identity.md)
    * 包括使用角色型存取控制 (RBAC) 和 Pod 身分識別來與 Azure Active Directory 整合。

**安全性**

* [叢集安全性與升級的最佳做法](operator-best-practices-cluster-security.md)
    * 包括保護 API 伺服器的存取權、限制容器存取，以及管理升級和節點重新開機。
* [容器映像管理與安全性的最佳做法](operator-best-practices-container-image-management.md)
    * 包括保護映射和執行時間，以及基底映射更新的自動化組建。
* [Pod 安全性的最佳做法](developer-best-practices-pod-security.md)
    * 包括保護資源存取權、限制認證公開程度，以及使用 Pod 身分識別與數位金鑰保存庫。

**網路和儲存體**

* [網路連線的最佳做法](operator-best-practices-network.md)
    * 包括不同網路模型、使用輸入與 Web 應用程式防火牆 (WAF)，以及保護節點 SSH 存取。
* [儲存體與備份的最佳做法](operator-best-practices-storage.md)
    * 包括選擇適當的儲存體類型與節點大小，以動態方式佈建磁碟區與資料備份。

**執行符合企業需求的工作負載**

* [商務持續性和災害復原的最佳做法](operator-best-practices-multi-region.md)
    * 包括搭配 Azure 流量管理員使用區域組、多個叢集，以及異地複寫的容器映像。

## <a name="developer-best-practices"></a>開發人員最佳做法

身為開發人員或應用程式擁有者，您可以簡化開發經驗，並定義必要的應用程式效能需求。

* [應用程式開發人員管理資源的最佳做法](developer-best-practices-resource-management.md)
    * 包括定義 Pod 資源要求與限制、設定開發工具，以及檢查應用程式問題。
* [Pod 安全性的最佳做法](developer-best-practices-pod-security.md)
    * 包括保護資源存取權、限制認證公開程度，以及使用 Pod 身分識別與數位金鑰保存庫。

## <a name="kubernetes--aks-concepts"></a>Kubernetes / AKS 概念

為了幫助您了解這些最佳做法的一些功能和元件，您也可以查看下列 Azure Kubernetes Service (AKS) 中叢集的概念性文章：

* [Kubernetes 核心概念](concepts-clusters-workloads.md)
* [存取與身分識別](concepts-identity.md)
* [安全性概念](concepts-security.md)
* [網路概念](concepts-network.md)
* [儲存體選項](concepts-storage.md)
* [調整選項](concepts-scale.md)

## <a name="next-steps"></a>後續步驟

如果您需要開始使用 AKS，請依照下列其中一篇快速入門進行，使用 [Azure CLI](kubernetes-walkthrough.md) 或 [Azure 入口網站](kubernetes-walkthrough-portal.md)來部署 Azure Kubernetes Service (AKS)。
