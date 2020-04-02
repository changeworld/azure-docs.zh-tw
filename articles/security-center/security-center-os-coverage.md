---
title: Azure 安全中心支援的平臺 |微軟文件
description: 本文檔提供 Azure 安全中心支援的平臺清單。
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
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521907"
---
# <a name="supported-platforms"></a>支援的平台 

此頁顯示 Azure 安全中心支援的平台和環境。

## <a name="combinations-of-environments"></a>環境組合<a name="vm-server"></a>

Azure 安全中心支援不同類型的混合環境中的虛擬機器和伺服器:

* 只 Azure
* Azure 與本地端
* Azure 和其他雲
* Azure、其他雲和本地

對於在 Azure 訂閱上啟動的 Azure 環境,Azure 安全中心將自動發現在訂閱中部署的 IaaS 資源。

## <a name="supported-operating-systems"></a>支援的作業系統

安全中心取決於[紀錄分析代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)程式 。 確保您的電腦運行此代理受支援的作業系統之一,如下頁所述:

* [適用於 Windows 支援的作業系統的紀錄分析代理](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Linux 支援的作業系統的紀錄分析代理](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

還確保您的紀錄分析代理已[正確設定為將資料傳送到安全中心](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> 要瞭解有關 Windows 與 Linux 上提供的特定安全中心功能的更多資訊,請參閱[電腦的功能覆蓋範圍](security-center-services.md)。

## <a name="managed-virtual-machine-services"></a>託管虛擬機器服務<a name="virtual-machine"></a>

虛擬機也是在客戶訂閱中創建的,作為某些 Azure 託管服務的一部分,例如 Azure 庫伯奈斯 (AKS)、Azure 數據塊等。 安全中心也會發現這些虛擬機器,如果支援的作業系統可用,則可以安裝和配置日誌分析代理。

## <a name="cloud-services"></a>雲服務<a name="cloud-services"></a>

還支援在雲端服務中運行的虛擬機器。 只監視在生產位置中執行的雲端服務 Web 角色和背景工作角色。 若要深入了解雲端服務，請參閱 [Azure 雲端服務概觀](../cloud-services/cloud-services-choose-me.md)。

還支援保護駐留在 Azure 堆疊中的 VM。 有關安全中心與 Azure 堆疊整合的詳細資訊,請參閱[將 Azure 堆疊虛擬機器裝到安全中心](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)。

## <a name="next-steps"></a>後續步驟

- 瞭解[安全性中心如何使用紀錄分析代理收集資料](security-center-enable-data-collection.md)。
- 瞭解[安全性中心如何管理與保護資料](security-center-data-security.md)。
- 瞭解如何[規劃與瞭解採用 Azure 安全中心的設計注意事項](security-center-planning-and-operations-guide.md)。