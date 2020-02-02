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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 9886f41b25e7c1b34f9805a2c16b716c642ae90e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936299"
---
# <a name="container-security-in-security-center"></a>資訊安全中心中的容器安全性

Azure 資訊安全中心是適用于容器安全性的 Azure 原生解決方案。 資訊安全中心也是您的雲端工作負載、Vm、伺服器和容器安全性的最佳單一視窗體驗。

本文說明如何改善、監視和維護容器及其應用程式的安全性。 您將瞭解資訊安全中心如何協助下列容器安全性的核心層面：

* 弱點管理
* 強化容器的環境
* 執行時間保護

[![Azure 資訊安全中心的 [容器安全性] 索引標籤](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

## <a name="vulnerability-management---scanning-container-images-preview"></a>弱點管理-掃描容器映射（預覽）
若要監視 ARM 型 Azure Container Registry，請確定您是在資訊安全中心的標準層（請參閱[定價](/azure/security-center/security-center-pricing)）。 然後啟用選用的容器登錄套件組合。 推送新的映射時，資訊安全中心會使用領先業界的弱點掃描廠商 Qualys 掃描映射。

找到問題時（依 Qualys 或資訊安全中心），您會在資訊安全中心儀表板中收到通知。 針對每個弱點，資訊安全中心提供可採取動作的建議，以及嚴重性分類，以及如何修復問題的指引。 如需資訊安全中心建議的詳細資訊，請參閱[建議的參考清單](recommendations-reference.md)。

## <a name="environment-hardening"></a>強化環境

### <a name="continuous-monitoring-of-your-docker-configuration"></a>持續監視 Docker 設定
Azure 資訊安全中心可識別 IaaS Linux Vm 上裝載的非受控容器，或其他執行 Docker 容器的 Linux 電腦。 資訊安全中心會持續評估這些容器的設定。 然後將它們與「[網際網路安全性」（CIS） Docker 基準測試的中心](https://www.cisecurity.org/benchmark/docker/)進行比較。 

資訊安全中心包含 CIS Docker 基準測試的整個規則集，並在您的容器無法滿足任何控制項時發出警示。 當它找到錯誤的錯誤時，資訊安全中心會產生安全性建議。 使用 [**建議] 頁面**來查看建議並補救問題。 您也會在 [**容器**] 索引標籤上看到建議，其中會顯示使用 Docker 部署的所有虛擬機器。 當您要探索虛擬機器上的安全性問題時，資訊安全中心提供有關機器上容器的其他資訊。 這類資訊包括 Docker 版本，以及在主機上執行的映射數目。 如需建議的詳細資料，請參閱[這裡](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-protection)。 

>[!NOTE]
> 這些 CIS 基準測試不會在 AKS 管理的實例或 Databricks 管理的 Vm 上執行。

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>連續監視您的 Kubernetes 叢集（預覽）
資訊安全中心與 Azure Kubernetes Service （AKS）搭配運作，這是 Microsoft 管理的容器協調流程服務，可用於開發、部署和管理容器化應用程式。

AKS 提供安全性控制和叢集安全性狀態的可見度。 資訊安全中心使用下列功能來執行下列動作：
* 持續監視 AKS 叢集的設定
* 產生與業界標準一致的安全性建議

如需資訊安全中心建議的詳細資訊，請參閱[虛擬機器保護](security-center-virtual-machine-protection.md)。

## <a name="run-time-protection---real-time-threat-detection"></a>執行時間保護-即時威脅偵測

資訊安全中心為您的容器化環境提供即時威脅偵測，並產生可疑活動的警示。 您可以使用這項資訊來快速修復安全性問題，並改善您容器的安全性。

我們偵測到主機和 AKS 叢集層級的威脅。 如需完整詳細資料，請參閱[Azure 容器的威脅偵測](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)。

## <a name="to-view-the-security-posture-of-your-container-related-resources"></a>若要查看容器相關資源的安全性狀態
1.  開啟資訊安全中心的 [**計算 & 應用程式**] 頁面。
2.  按一下 [**容器**] 索引標籤。AKS 叢集、ACR 登錄和執行 Docker 的 Vm 的狀態隨即出現。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure 資訊安全中心中的容器安全性，請參閱：
* [與 Azure Kubernetes Service 整合](azure-kubernetes-service-integration.md)的詳細資料

* [與 Azure Container Registry 整合](azure-container-registry-integration.md)的詳細資料