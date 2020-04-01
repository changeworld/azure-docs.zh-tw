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
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: f9b948714f72ba02a100d9941721f073953bf22a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473247"
---
# <a name="supported-platforms"></a>支援的平台 

## <a name="virtual-machines--servers"></a>虛擬機器/伺服器<a name="vm-server"></a>

安全中心支援不同類型的混合環境中的虛擬機器/伺服器:

* 只 Azure
* Azure 與本地端
* Azure 和其他雲
* Azure、其他雲和本地

對於在 Azure 訂閱上啟動的 Azure 環境,Azure 安全中心將自動發現在訂閱中部署的 IaaS 資源。

> [!NOTE]
> 要接收全套安全功能,必須安裝 Azure 安全中心使用的[紀錄分析代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent),並[正確設定該代理以將資料傳送到 Azure 安全中心](security-center-enable-data-collection.md#manual-agent)。

以下各節列出了 Azure 安全中心使用的[日誌分析代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)可以運行的支援伺服器作業系統。

### <a name="windows-server-operating-systems"></a>Windows 伺服器作業系統<a name="os-windows"></a>

|OS|Azure 安全中心支援|支援與微軟後衛ATP整合|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

要瞭解有關上面列出的 Windows 作業系統支援的功能,請參閱[虛擬機器/伺服器支援的功能](security-center-services.md#vm-server-features)。

### <a name="windows-operating-systems"></a>視窗作業系統<a name="os-windows (non-server)"></a>

Azure 安全中心與 Azure 服務整合,以監視和保護基於 Windows 的虛擬機器。

### <a name="linux-operating-systems"></a>Linux 作業系統<a name="os-linux"></a>

64 位元

* CentOS 6 和 7
* Amazon Linux 2017.09
* 甲骨文Linux 6和甲骨文Linux 7
* Red Hat Enterprise Linux Server 6 和 7
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
* SUSE Linux Enterprise Server 12

32 位元
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS 和 16.04 LTS

> [!NOTE]
> 由於支援的 Linux 作業系統清單在不斷變化,如果您願意,[請按一下此處](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems)查看支援版本的最新清單,以防自本主題上次發表以來發生更改。

要瞭解有關上面列出的 Linux 作業系統支援的功能,請參閱[虛擬機器/伺服器支援的功能](security-center-services.md#vm-server-features)。

### <a name="managed-virtual-machine-services"></a>託管虛擬機器服務<a name="virtual-machine"></a>

虛擬機也是在客戶訂閱中創建的,作為某些 Azure 託管服務的一部分,例如 Azure 庫伯奈斯 (AKS)、Azure 數據塊等。 這些虛擬機也由 Azure 安全中心發現,日誌分析代理可以根據上面列出的支援的[Windows/Linux 操作系統](#os-windows)進行安裝和配置。

### <a name="cloud-services"></a>雲服務<a name="cloud-services"></a>

還支援在雲端服務中運行的虛擬機器。 只監視在生產位置中執行的雲端服務 Web 角色和背景工作角色。 若要深入了解雲端服務，請參閱 [Azure 雲端服務概觀](../cloud-services/cloud-services-choose-me.md)。

還支援保護駐留在 Azure 堆疊中的虛擬機器。 有關安全中心與 Azure 堆疊整合的詳細資訊,請參閱[將 Azure 堆疊虛擬機器裝到安全中心](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)。

## <a name="next-steps"></a>後續步驟

- 瞭解[安全性中心如何收集資料與紀錄分析代理](security-center-enable-data-collection.md)程式 。
- 瞭解[安全性中心如何管理與保護資料](security-center-data-security.md)。
- 瞭解如何[規劃與瞭解採用 Azure 安全中心的設計注意事項](security-center-planning-and-operations-guide.md)。
- 瞭解[可用於不同雲端環境的功能](security-center-services.md)。
- 詳細瞭解[Azure 安全中心中 Windows 和 Linux 電腦的威脅保護](threat-protection.md#windows-machines)。
