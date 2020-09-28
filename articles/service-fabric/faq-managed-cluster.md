---
title: Service Fabric 受控叢集的常見問題
description: Service Fabric 受控叢集的常見問題，包括功能、使用案例和一般案例。
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 73815e793357dd38244dd429f9056c4c6a0bfff1
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410308"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Service Fabric 受控叢集的常見問題

有許多關於 Service Fabric 受控叢集 (preview) 可以做什麼，以及應該如何使用的常見問題。 本文件涵蓋許多這類問題和解答。

## <a name="general"></a>一般

### <a name="what-are-service-fabric-managed-clusters"></a>什麼是 Service Fabric 受控叢集？

Service Fabric 受控叢集是 Service Fabric 叢集資源模型的演進，其設計可讓您更輕鬆地部署和管理叢集。 Service Fabric 受控叢集使用 Azure Resource Manager 封裝模型，讓使用者只需要定義和部署單一叢集資源，相較于目前必須部署的許多獨立資源 (虛擬機器擴展集、Load Balancer、IP 及更多) 。

### <a name="what-regions-are-supported-in-the-preview"></a>預覽版支援哪些區域？

Service Fabric 受控叢集預覽支援的區域包括 `centraluseuap` 、、 `eastus2euap` `eastasia` 、 `northeurope` 、 `westcentralus` 和 `eastus2` 。

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>我可以將現有的 Service Fabric 叢集就地遷移至受控叢集資源嗎？

目前，您必須建立新的 Service Fabric 叢集資源，才能使用新的 Service Fabric 受控叢集資源類型。

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Service Fabric 受控叢集有額外的費用嗎？

否，除了叢集所需的基礎計算、儲存體和網路資源成本之外，Service Fabric 受控叢集不會產生額外的費用。

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Service Fabric 受控叢集資源是否引進了新的 SLA？

SLA 不會從目前的 Service Fabric 資源模型變更。

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>基本和標準 SKU 叢集之間有何差異？

基本 SKU 叢集表示，大部分的設定都是由 Service Fabric 資源提供者提供。 基本 SKU 叢集的目的是要用於測試和預先生產環境。 標準 SKU 叢集可讓使用者設定叢集，以明確符合其需求。 如需詳細資訊，請參閱 [Service Fabric 受控叢集 sku](overview-managed-cluster.md#service-fabric-managed-cluster skus.md) ，以取得更多詳細資料。

## <a name="cluster-deployment-and-management"></a>叢集部署與管理

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>我在虛擬機器擴展集上執行自訂腳本擴充功能，是否可以繼續使用受控 Service Fabric 資源來執行？

是的，您仍然可以在節點類型上指定 VM 擴充功能。 如需詳細資訊，請參閱節點類型延伸模組範例以取得詳細資料。

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>我想要擁有僅限內部的負載平衡器，可以嗎？

目前無法擁有內部的負載平衡器。 建議您鎖定網路安全性群組規則，以封鎖任何不想要的輸入/輸出流量。

### <a name="can-i-autoscale-my-cluster"></a>我可以自動調整叢集嗎？ 
預覽版目前無法使用自動調整功能。

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>我可以跨可用性區域部署叢集嗎？ 
預覽中目前無法使用跨可用性區域叢集。

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>我可以為我的叢集執行時間選擇自動和手動升級嗎？ 
在預覽中，所有執行時間升級都會自動完成。

## <a name="applications"></a>應用程式

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Service Fabric 受控叢集是否有本機開發體驗？

從現有的 Service Fabric 叢集來的本機開發經驗保持不變。 如需詳細資訊，請參閱 [建立 .Net 應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-quickstart-dotnet) ，以取得有關本機開發體驗的詳細資料。

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>我可以將我的應用程式部署為 Azure Resource Manager 資源嗎？

在預覽中，您無法將應用程式部署為 Azure Resource Manager 資源。 您必須透過 PowerShell 或 CLI 直接連接到叢集，以部署應用程式。 在受控 Service Fabric 叢集進入正式運作之前，將會新增此功能。
