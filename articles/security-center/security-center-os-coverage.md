---
title: Azure 資訊安全中心支援的平臺 |Microsoft Docs
description: 本檔提供 Azure 資訊安全中心所支援的平臺清單。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: f02e45804234fc8f8aa2b966db7ddce5efc9cfd1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998827"
---
# <a name="supported-platforms"></a>支援的平台 

此頁面會顯示 Azure 資訊安全中心所支援的平臺和環境。

## <a name="combinations-of-environments"></a>環境的組合 <a name="vm-server"></a>

Azure 資訊安全中心在不同類型的混合式環境中支援虛擬機器和伺服器：

* 僅限 Azure
* Azure 和內部部署
* Azure 和其他雲端
* Azure、其他雲端和內部部署

針對在 Azure 訂用帳戶上啟用的 Azure 環境，Azure 資訊安全中心會自動探索在訂用帳戶內部署的 IaaS 資源。

## <a name="supported-operating-systems"></a>支援的作業系統

資訊安全中心取決於 [Log Analytics 代理程式](../azure-monitor/platform/agents-overview.md#log-analytics-agent)。 確定您的電腦正在執行此代理程式支援的其中一個作業系統，如下列頁面所述：

* [適用于 Windows 的 Log Analytics 代理程式支援的作業系統](../azure-monitor/platform/agents-overview.md#supported-operating-systems)
* [適用于 Linux 的 Log Analytics 代理程式支援的作業系統](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

也請確定您的 Log Analytics 代理程式已[正確設定，以將資料傳送至「安全性中心](security-center-enable-data-collection.md#manual-agent)」

> [!TIP]
> 若要深入瞭解 Windows 和 Linux 上可用的特定資訊安全中心功能，請參閱 [機器的功能涵蓋範圍](security-center-services.md)。

## <a name="managed-virtual-machine-services"></a>受管理的虛擬機器服務 <a name="virtual-machine"></a>

虛擬機器也會在客戶訂用帳戶中建立，作為某些 Azure 受控服務的一部分，例如 Azure Kubernetes (AKS) 、Azure Databricks 等等。 安全性中心也會探索這些虛擬機器，而且如果有支援的作業系統，就可以安裝和設定 Log Analytics 代理程式。

## <a name="cloud-services"></a>雲端服務 <a name="cloud-services"></a>

也支援在雲端服務中執行的虛擬機器。 只監視在生產位置中執行的雲端服務 Web 角色和背景工作角色。 若要深入了解雲端服務，請參閱 [Azure 雲端服務概觀](../cloud-services/cloud-services-choose-me.md)。

也支援 Azure Stack 中的 Vm 保護。 如需資訊安全中心與 Azure Stack 整合的詳細資訊，請參閱將 [您的 Azure stack 虛擬機器上線至資訊安全中心](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)。

## <a name="next-steps"></a>後續步驟

- 瞭解 [安全性中心如何使用 Log Analytics 代理程式來收集資料](security-center-enable-data-collection.md)。
- 瞭解 [安全性中心如何管理和保護資料](security-center-data-security.md)。
- 瞭解如何 [規劃及瞭解採用 Azure 資訊安全中心的設計考慮](security-center-planning-and-operations-guide.md)。