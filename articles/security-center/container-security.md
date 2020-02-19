---
title: Azure 資訊安全中心中的容器安全性 |Microsoft Docs
description: 瞭解 Azure 資訊安全中心的容器安全性功能。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: ef87d8d02e6d7800435cab207a88197ef7c94b7c
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430987"
---
# <a name="container-security-in-security-center"></a>資訊安全中心中的容器安全性

Azure 資訊安全中心是適用于容器安全性的 Azure 原生解決方案。 資訊安全中心也是您的雲端工作負載、Vm、伺服器和容器安全性的最佳單一視窗體驗。

本文說明資訊安全中心如何協助您改善、監視和維護容器及其應用程式的安全性。 您將瞭解資訊安全中心如何協助下列容器安全性的核心層面：

* 弱點管理
* 強化容器的環境
* 執行時間保護

[![Azure 資訊安全中心的 [容器安全性] 索引標籤](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

如需有關如何使用這些功能的指示，請參閱[監視容器的安全性](monitor-container-security.md)。

## <a name="vulnerability-management---scanning-container-images-preview"></a>弱點管理-掃描容器映射（預覽）
若要監視 ARM 型 Azure Container Registry，請確定您是在資訊安全中心的標準層（請參閱[定價](/azure/security-center/security-center-pricing)）。 然後啟用選用的容器登錄套件組合。 推送新的映射時，資訊安全中心會使用領先業界的弱點掃描廠商 Qualys 掃描映射。

找到問題時（依 Qualys 或資訊安全中心），您會在資訊安全中心儀表板中收到通知。 針對每個弱點，資訊安全中心提供可採取動作的建議，以及嚴重性分類，以及如何修復問題的指引。 如需資訊安全中心的容器建議詳細資料，請參閱[建議的參考清單](recommendations-reference.md#recs-containers)。

## <a name="environment-hardening"></a>強化環境

### <a name="continuous-monitoring-of-your-docker-configuration"></a>持續監視 Docker 設定
Azure 資訊安全中心可識別 IaaS Linux Vm 上裝載的非受控容器，或其他執行 Docker 容器的 Linux 電腦。 資訊安全中心會持續評估這些容器的設定。 然後將它們與「[網際網路安全性」（CIS） Docker 基準測試的中心](https://www.cisecurity.org/benchmark/docker/)進行比較。

資訊安全中心包含 CIS Docker 基準測試的整個規則集，並在您的容器無法滿足任何控制項時發出警示。 當它找到錯誤的錯誤時，資訊安全中心會產生安全性建議。 使用 [**建議] 頁面**來查看建議並補救問題。 您也會在 [**容器**] 索引標籤上看到建議，其中會顯示使用 Docker 部署的所有虛擬機器。 

如需有關這項功能可能會出現之相關資訊安全中心建議的詳細資訊，請參閱建議參考資料表的[容器一節](recommendations-reference.md#recs-containers)。

當您探索 VM 的安全性問題時，資訊安全中心提供有關機器上容器的其他資訊。 這類資訊包括 Docker 版本，以及在主機上執行的映射數目。 

>[!NOTE]
> 這些 CIS 基準測試不會在 AKS 管理的實例或 Databricks 管理的 Vm 上執行。

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>連續監視您的 Kubernetes 叢集（預覽）
資訊安全中心與 Azure Kubernetes Service （AKS）搭配運作，這是 Microsoft 管理的容器協調流程服務，可用於開發、部署和管理容器化應用程式。

AKS 提供安全性控制和叢集安全性狀態的可見度。 資訊安全中心使用下列功能來執行下列動作：
* 持續監視 AKS 叢集的設定
* 產生與業界標準一致的安全性建議

如需有關這項功能可能會出現之相關資訊安全中心建議的詳細資訊，請參閱建議參考資料表的[容器一節](recommendations-reference.md#recs-containers)。

## <a name="run-time-protection---real-time-threat-detection"></a>執行時間保護-即時威脅偵測

資訊安全中心為您的容器化環境提供即時威脅偵測，並產生可疑活動的警示。 您可以使用這項資訊來快速修復安全性問題，並改善您容器的安全性。

我們偵測到主機和 AKS 叢集層級的威脅。 如需完整詳細資料，請參閱[Azure 容器的威脅偵測](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)。


## <a name="container-security-faq"></a>容器安全性常見問題

### <a name="what-types-of-images-can-azure-security-center-scan"></a>哪些映射類型可以 Azure 資訊安全中心掃描？
資訊安全中心會掃描以 Linux OS 為基礎的映射。 

Qualys 掃描器不支援只包含您的應用程式及其執行時間相依性的 "distroless" 映射。

### <a name="how-does-we-scan-azure-security-center-scan-an-image"></a>我們會如何掃描 Azure 資訊安全中心掃描影像？
映射會從登錄中解壓縮。 然後，它會在隔離的沙箱中執行，並使用 Qualys 掃描器來解壓縮已知弱點的清單。

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Azure 資訊安全中心掃描影像的頻率為何？
系統會在每次推送時觸發影像掃描。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>我可以透過 REST API 取得掃描結果嗎？
是。 結果會在[子評量 REST API](/rest/api/securitycenter/subassessments/list/)之下。 此外，您可以使用 Azure Resource Graph （ARG），這是適用于所有資源的類似 Kusto API：查詢可以提取特定的掃描。
 

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure 資訊安全中心中的容器安全性，請參閱下列相關文章：

* 若要查看容器相關資源的安全性狀態，請參閱[保護您的機器和應用程式](security-center-virtual-machine-protection.md#containers)的容器一節。

* [與 Azure Kubernetes Service 整合](azure-kubernetes-service-integration.md)的詳細資料

* [與 Azure Container Registry 整合](azure-container-registry-integration.md)的詳細資料