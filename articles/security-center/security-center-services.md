---
title: Azure 安全中心中支援的功能 |微軟文檔
description: 本文檔提供 Azure 安全中心支援的服務清單。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245235"
---
# <a name="feature-coverage-for-machines"></a>機器功能覆蓋率

下表顯示了可用於虛擬機器和伺服器的 Azure 安全中心功能。

## <a name="supported-features-for-virtual-machines-and-servers"></a>虛擬機器和伺服器受支援的功能<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows 機器](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**特徵**|**Azure 虛擬機器**|**Azure 虛擬機器擴展集**|**非 Azure 電腦**|**定價**
|[微軟後衛ATP集成](security-center-wdatp.md)|✔</br>（在支援的版本上）|✔</br>（在支援的版本上）|✔|標準|
|[虛擬機器行為分析（和安全警報）](threat-protection.md)|✔|✔|✔|建議（免費） </br></br> 安全警報（標準）|
|[無檔安全警報](alerts-reference.md#alerts-windows)|✔|✔|✔|標準|
|[基於網路的安全警報](threat-protection.md#network-layer)|✔|✔|-|標準|
|[即時 VM 訪問](security-center-just-in-time.md)|✔|-|-|標準|
|[本機漏洞評估](built-in-vulnerability-assessment.md)|✔|-|-|標準|
|[檔完整性監控](security-center-file-integrity-monitoring.md)|✔|✔|✔|標準|
|[自我調整應用程式控制](security-center-adaptive-application.md)|✔|-|✔|標準|
|[網路地圖](security-center-network-recommendations.md#network-map)|✔|✔|-|標準|
|[自我調整網路硬化](security-center-adaptive-network-hardening.md)|✔|-|-|標準|
|自適性網路控制措施|✔|✔|-|標準|
|[法規遵從性儀表板&報告](security-center-compliance-dashboard.md)|✔|✔|✔|標準|
|Docker 託管的 IaaS 容器上的建議和威脅保護|-|-|-|標準|
|缺少作業系統修補程式評估|✔|✔|✔|免費|
|安全配置錯誤評估|✔|✔|✔|免費|
|[端點保護評估](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|免費|
|磁片加密評估|✔|✔|-|免費|
|協力廠商漏洞評估|✔|-|-|免費|
|[網路安全評估](security-center-network-recommendations.md)|✔|✔|-|免費|


### <a name="linux-machines"></a>[Linux 機器](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**特徵**|**Azure 虛擬機器**|**Azure 虛擬機器擴展集**|**非 Azure 電腦**|**定價**
|[微軟後衛ATP集成](security-center-wdatp.md)|-|-|-|標準|
|[虛擬機器行為分析（和安全警報）](security-center-alerts-iaas.md)|✔</br>（在支援的版本上）|✔</br>（在支援的版本上）|✔|建議（免費） </br></br> 安全警報（標準）|
|[無檔安全警報](alerts-reference.md#alerts-windows)|-|-|-|標準|
|[基於網路的安全警報](threat-protection.md#network-layer)|✔|✔|-|標準|
|[即時 VM 訪問](security-center-just-in-time.md)|✔|-|-|標準|
|[本機漏洞評估](built-in-vulnerability-assessment.md)|✔|-|-|標準|
|[檔完整性監控](security-center-file-integrity-monitoring.md)|✔|✔|✔|標準|
|[自我調整應用程式控制](security-center-adaptive-application.md)|✔|-|✔|標準|
|[網路地圖](security-center-network-recommendations.md#network-map)|✔|✔|-|標準|
|[自我調整網路硬化](security-center-adaptive-network-hardening.md)|✔|-|-|標準|
|自適性網路控制措施|✔|✔|-|標準|
|[法規遵從性儀表板&報告](security-center-compliance-dashboard.md)|✔|✔|✔|標準|
|Docker 託管的 IaaS 容器上的建議和威脅保護|✔|✔|✔|標準|
|缺少作業系統修補程式評估|✔|✔|✔|免費|
|安全配置錯誤評估|✔|✔|✔|免費|
|[端點保護評估](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|免費|
|磁片加密評估|✔|✔|-|免費|
|協力廠商漏洞評估|✔|-|-|免費|
|[網路安全評估](security-center-network-recommendations.md)|✔|✔|-|免費|

--- 


> [!TIP]
>要試用僅在標準定價層上可用的功能，免費套餐使用者可以註冊 30 天試用版。 有關詳細資訊，請參閱[定價頁](https://azure.microsoft.com/pricing/details/security-center/)。


## <a name="supported-endpoint-protection-solutions"></a>支援的端點保護解決方案<a name="endpoint-supported"></a>

下表提供的矩陣指出：

 - 您是否可以使用 Azure 資訊安全中心安裝適用於您的每個解決方案。
 - 資訊安全中心可以探索哪些端點保護解決方案。 如果發現此清單中的終結點保護解決方案，安全中心不會建議安裝。

有關何時為每個保護生成建議的資訊，請參閱[端點保護評估和建議](security-center-endpoint-protection.md)。

| 端點保護| 平台 | 資訊安全中心安裝 | 資訊安全中心探索 |
|------|------|-----|-----|
| Windows Defender (Microsoft 反惡意程式碼軟體)| Windows Server 2016| 否，內建於 OS| 是 |
| System Center Endpoint Protection (Microsoft 反惡意程式碼軟體) | Windows Server 2012 R2、2012、2008 R2 (請參閱下列附註) | 透過延伸模組 | 是 |
| 趨勢科技 [ 所有版本] | Windows Server 系列  | 否 | 是 |
| Symantec v12.1.1100+| Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Linux 伺服器系列  | 否 | 是的**\*** |
| 索福斯 V9+| Linux 伺服器系列  | 否 | 是的**\***  |

 **\*** 覆蓋狀態和支援資料當前僅在與受保護訂閱關聯的日誌分析工作區中可用。 它不反映在 Azure 安全中心門戶中。

> [!NOTE]
> - 在 Windows Server 2008 R2 虛擬機器上偵測 System Center Endpoint Protection (SCEP) 需要安裝 SCEP (PowerShell 3.0 或更高版本)。
> - 深度安全代理支援檢測趨勢微保護。  不支援 OfficeScan 代理。


## <a name="next-steps"></a>後續步驟

- 瞭解[安全中心如何收集資料和日誌分析代理](security-center-enable-data-collection.md)。
- 瞭解[安全中心如何管理和保護資料](security-center-data-security.md)。
- 瞭解如何[規劃和瞭解採用 Azure 安全中心的設計注意事項](security-center-planning-and-operations-guide.md)。
- 查看[支援安全中心的平臺](security-center-os-coverage.md)。
- 詳細瞭解[Azure 安全中心中 Windows 和 Linux 電腦的威脅保護](threat-protection.md#windows-machines)。
- 查找[有關 Azure 安全中心的常見問題](faq-general.md)。