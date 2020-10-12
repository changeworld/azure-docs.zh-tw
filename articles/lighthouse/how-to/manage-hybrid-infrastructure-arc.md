---
title: 使用 Azure Arc 大規模管理混合式基礎結構
description: 瞭解如何有效地管理客戶的電腦和 Azure 外部的 Kubernetes 叢集。
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 66a798265683045d7ff9f3d8d811141800d08f9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336610"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>使用 Azure Arc 大規模管理混合式基礎結構

作為服務提供者，您可能已上線多個客戶租使用者來 [Azure Lighthouse](../overview.md)。 Azure Lighthouse 可讓服務提供者一次在多個 Azure Active Directory () Azure AD 之間執行大規模作業，讓管理工作更有效率。

[Azure Arc](../../azure-arc/overview.md) 有助於簡化跨內部部署、邊緣和多重雲端的複雜和分散式環境，讓您可以在任何位置部署 azure 服務並將 azure 管理延伸到任何基礎結構。

在 [啟用 Azure Arc 的伺服器](../../azure-arc/servers/overview.md)上，客戶可以使用管理原生 Azure 虛擬機器的相同方式，在其公司網路上管理裝載于 Azure 外部的任何 Windows 和 Linux 機器。 透過將混合式機器連結至 Azure，其會變成已連線，而且系統會將其視為 Azure 中的資源。 服務提供者可以接著管理這些非 Azure 機器，以及其客戶的 Azure 資源。

[Azure Arc Enabled Kubernetes (preview) ](../../azure-arc/kubernetes/overview.md) 可讓客戶在 Azure 內部或外部附加和設定 Kubernetes 叢集。 將 Kubernetes 叢集附加至 Azure Arc 時，它會出現在 Azure 入口網站中，並具有 Azure Resource Manager 識別碼和受控識別。 叢集會附加至標準 Azure 訂用帳戶 (位於資源群組中)，且可以如同任何其他 Azure 資源接收標籤。

本主題概要說明服務提供者如何使用 Azure Arc 啟用的伺服器和 Azure Arc 啟用的 Kubernetes (預覽) ，以可調整的方式來管理客戶的混合式環境，並在所有受控客戶租使用者之間查看。

> [!TIP]
> 雖然我們指的是本主題中的服務提供者和客戶，但本指南也適用于 [使用 Azure Lighthouse 來管理多個](../concepts/enterprise.md)租使用者的企業。

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>使用已啟用 Azure Arc 的伺服器大規模管理混合式伺服器

如果您是服務提供者，您可以在 Azure 外部管理內部部署 Windows Server 或 Linux 機器，而您的客戶已使用 [Azure Connected Machine 代理程式](../../azure-arc/servers/agent-overview.md)連接到其訂用帳戶。

在 Azure 入口網站中為委派的訂用帳戶查看資源時，您會看到這些連線的電腦標示為 [ **Azure Arc**]。您可以使用 Azure 結構（例如 Azure 原則和標記）來管理這些連線的電腦，就像您管理客戶 Azure 資源的方式一樣。 您也可以跨客戶租使用者工作，以同時管理所有連線的混合式機器。

例如，您可以 [確保跨客戶的混合式電腦套用相同的原則集](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md)。 您也可以使用 Azure 資訊安全中心來監視所有客戶的混合式環境中的合規性，或 [使用 Azure 監視器直接從混合式電腦將資料收集](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) 到 Log Analytics 工作區。 [虛擬機器擴充](../../azure-arc/servers/manage-vm-extensions.md) 功能可以部署至非 Azure Windows 和 Linux vm，以簡化客戶的混合式機器管理。

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes-preview"></a>使用 Azure Arc 啟用的 Kubernetes (預覽版，大規模管理混合式 Kubernetes 叢集) 

> [!NOTE]
> Azure Arc 啟用的 Kubernetes 目前為預覽狀態。 目前不建議用於生產工作負載。

您可以 [使用 Azure Arc 來管理已連線到客戶訂用](../../azure-arc/kubernetes/connect-cluster.md)帳戶的 Kubernetes 叢集，就像是在 Azure 中執行一樣。

如果您的客戶已建立 [服務主體帳戶](../../azure-arc/kubernetes/create-onboarding-service-principal.md)，讓 Kubernetes 叢集上線 Azure Arc，您就可以存取此服務主體帳戶來登入及管理叢集。 當包含服務主體帳戶的訂用帳戶 [上線至 Azure Lighthouse](onboard-customer.md)時，管理租使用者中已授與「Kubernetes 叢集 Azure Arc 上線」 Azure 內建角色的使用者可以完成這項作業。

您可以針對已連線的叢集使用 Gitops) 將 [來部署設定](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) 和 [Helm 圖](../../azure-arc/kubernetes/use-gitops-with-helm.md) 。

您也可以使用 Azure 監視器監視已連線的叢集，並 [使用 Azure 原則大規模](../../azure-arc/kubernetes/use-azure-policy.md)套用叢集設定。

## <a name="next-steps"></a>後續步驟

- 探索 [Azure Arc GitHub 存放庫](https://github.com/microsoft/azure_arc)中的 jumpstarts 和範例。 
- 瞭解 [Azure Arc 啟用的伺服器的支援案例](../../azure-arc/servers/overview.md#supported-scenarios)。
- 瞭解 [Azure Arc 所支援的 Kubernetes 發行](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions)版。
- 瞭解如何 [大規模部署原則](policy-at-scale.md)。
- 瞭解如何 [大規模使用 Azure 監視器記錄](monitor-at-scale.md)。

