---
title: Azure 資訊安全中心所支援平台 | Microsoft Docs
description: 本文件提供 Azure 資訊安全中心所支援的平台清單。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 68cb738ae6e4689a0356ea56c1de2d383ea83ad6
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449946"
---
# <a name="supported-platforms"></a>支援的平台 

此頁面會顯示 Azure 資訊安全中心支援的平台和環境。

## <a name="combinations-of-environments"></a>環境的組合 <a name="vm-server"></a>

Azure 資訊安全中心支援不同混合式環境類型上的虛擬機器和伺服器：

* 僅限 Azure
* Azure 和內部部署
* Azure 和其他雲端
* Azure、其他雲端及內部部署環境

針對在 Azure 訂用帳戶上啟用的 Azure 環境，Azure 資訊安全中心會自動探索在訂用帳戶內部署的 IaaS 資源。

## <a name="supported-operating-systems"></a>支援的作業系統

資訊安全中心相依於 [Log Analytics 代理程式](../azure-monitor/platform/agents-overview.md#log-analytics-agent)。 請確定您的機器正在執行此代理程式支援的其中一個作業系統，如下列頁面所述：

* [Windows 版 Log Analytics 代理程式支援的作業系統](../azure-monitor/platform/agents-overview.md#supported-operating-systems)
* [Linux 版 Log Analytics 代理程式支援的作業系統](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

此外，請確定您的 Log Analytics 代理程式已[正確設定，以便將資料傳送至資訊安全中心](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> 若要深入了解 Windows 和 Linux 上可用的特定資訊安全中心功能，請參閱[機器適用的功能涵蓋範圍](security-center-services.md)。

## <a name="managed-virtual-machine-services"></a>受控虛擬機器服務 <a name="virtual-machine"></a>

虛擬機器也會在客戶訂用帳戶中建立為 Azure 管理的部分服務，例如 Azure Kubernetes (AKS)、Azure Databricks 等等。 資訊安全中心也會探索這些虛擬機器，並在支援的 OS 可供使用時，安裝和設定 Log Analytics 代理程式。

## <a name="cloud-services"></a>雲端服務 <a name="cloud-services"></a>

也支援在雲端服務中執行的虛擬機器。 只監視在生產位置中執行的雲端服務 Web 角色和背景工作角色。 若要深入了解雲端服務，請參閱 [Azure 雲端服務概觀](../cloud-services/cloud-services-choose-me.md)。

也支援 Azure Stack 中的 VM 保護。 如需資訊安全中心與 Azure Stack 整合的詳細資訊，請參閱[將 Azure Stack 虛擬機器上架至資訊安全中心](quickstart-onboard-machines.md)。 

## <a name="next-steps"></a>後續步驟

- 了解[資訊安全中心如何使用 Log Analytics 代理程式來收集資料](security-center-enable-data-collection.md)。
- 了解[資訊安全中心如何管理和保護資料](security-center-data-security.md)。
- 了解如何[規劃及了解採用 Azure 資訊安全中心的設計考量](security-center-planning-and-operations-guide.md)。