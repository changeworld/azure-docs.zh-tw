---
title: Azure 資訊安全中心提供支援的功能 |Microsoft Docs
description: 本檔提供 Azure 資訊安全中心所支援的服務清單。
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394675"
---
# <a name="feature-coverage-for-machines"></a>機器的功能涵蓋範圍

下表顯示可用於虛擬機器和伺服器的 Azure 資訊安全中心功能。

## 支援的虛擬機器和伺服器功能<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows 機器](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**功能**|**Azure 虛擬機器**|**Azure 虛擬機器擴展集**|**非 Azure 機器**|**定價**
|[Microsoft Defender ATP 整合](security-center-wdatp.md)|✔</br>（支援的版本）|✔</br>（支援的版本）|✔|標準|
|[虛擬機器行為分析（和安全性警示）](threat-protection.md)|✔|✔|✔|建議（免費） </br></br> 安全性警示（標準）|
|[Fileless 安全性警示](alerts-reference.md#alerts-windows)|✔|✔|✔|標準|
|[以網路為基礎的安全性警示](threat-protection.md#network-layer)|✔|✔|-|標準|
|[即時 VM 存取](security-center-just-in-time.md)|✔|-|-|標準|
|[原生弱點評估](built-in-vulnerability-assessment.md)|✔|-|-|標準|
|[檔案完整性監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|標準|
|[自動調整應用程式控制項](security-center-adaptive-application.md)|✔|-|✔|標準|
|[網路地圖](security-center-network-recommendations.md#network-map)|✔|✔|-|標準|
|[彈性網路強化](security-center-adaptive-network-hardening.md)|✔|-|-|標準|
|自適性網路控制措施|✔|✔|-|標準|
|[法規合規性儀表板 & 報告](security-center-compliance-dashboard.md)|✔|✔|✔|標準|
|Docker 託管 IaaS 容器上的建議和威脅防護|-|-|-|標準|
|缺少 OS 修補程式評估|✔|✔|✔|免費|
|安全性錯誤的評估|✔|✔|✔|免費|
|[Endpoint protection 評估](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|免費|
|磁片加密評估|✔|✔|-|免費|
|協力廠商的弱點評估|✔|-|-|免費|
|[網路安全性評量](security-center-network-recommendations.md)|✔|✔|-|免費|


### <a name="linux-machines"></a>[Linux 機器](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**功能**|**Azure 虛擬機器**|**Azure 虛擬機器擴展集**|**非 Azure 機器**|**定價**
|[Microsoft Defender ATP 整合](security-center-wdatp.md)|-|-|-|標準|
|[虛擬機器行為分析（和安全性警示）](security-center-alerts-iaas.md)|✔</br>（支援的版本）|✔</br>（支援的版本）|✔|建議（免費） </br></br> 安全性警示（標準）|
|[Fileless 安全性警示](alerts-reference.md#alerts-windows)|-|-|-|標準|
|[以網路為基礎的安全性警示](threat-protection.md#network-layer)|✔|✔|-|標準|
|[即時 VM 存取](security-center-just-in-time.md)|✔|-|-|標準|
|[原生弱點評估](built-in-vulnerability-assessment.md)|✔|-|-|標準|
|[檔案完整性監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|標準|
|[自動調整應用程式控制項](security-center-adaptive-application.md)|✔|-|✔|標準|
|[網路地圖](security-center-network-recommendations.md#network-map)|✔|✔|-|標準|
|[彈性網路強化](security-center-adaptive-network-hardening.md)|✔|-|-|標準|
|自適性網路控制措施|✔|✔|-|標準|
|[法規合規性儀表板 & 報告](security-center-compliance-dashboard.md)|✔|✔|✔|標準|
|Docker 託管 IaaS 容器上的建議和威脅防護|✔|✔|✔|標準|
|缺少 OS 修補程式評估|✔|✔|✔|免費|
|安全性錯誤的評估|✔|✔|✔|免費|
|[Endpoint protection 評估](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|免費|
|磁片加密評估|✔|✔|-|免費|
|協力廠商的弱點評估|✔|-|-|免費|
|[網路安全性評量](security-center-network-recommendations.md)|✔|✔|-|免費|

--- 


> [!TIP]
>若要試驗僅適用于標準定價層的功能，免費層使用者可以註冊30天的試用版。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。


## 支援的端點保護解決方案<a name="endpoint-supported"></a>

下表提供的矩陣指出：

 - 您是否可以使用 Azure 資訊安全中心安裝適用於您的每個解決方案。
 - 資訊安全中心可以探索哪些端點保護解決方案。 如果探索到此清單中的 endpoint protection 解決方案，資訊安全中心不建議安裝一個。

如需每一項保護何時產生建議的相關資訊，請參閱[Endpoint Protection 評估和建議](security-center-endpoint-protection.md)。

| Endpoint Protection| 平台 | 資訊安全中心安裝 | 資訊安全中心探索 |
|------|------|-----|-----|
| Windows Defender (Microsoft 反惡意程式碼軟體)| Windows Server 2016| 否，內建於 OS| 是 |
| System Center Endpoint Protection (Microsoft 反惡意程式碼軟體) | Windows Server 2012 R2、2012、2008 R2 (請參閱下列附註) | 透過延伸模組 | 是 |
| 趨勢微–所有版本 * | Windows Server 系列  | 否 | 是 |
| Symantec v12.1.1100+| Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Linux 伺服器系列  | 否 | 是 **\*** |
| Sophos V9 +| Linux 伺服器系列  | 否 | 是 **\***  |

 **\*** 涵蓋範圍狀態和支援的資料目前僅適用于與受保護的訂用帳戶相關聯的 Log Analytics 工作區。 它不會反映在 Azure 資訊安全中心入口網站中。

> [!NOTE]
> - 在 Windows Server 2008 R2 虛擬機器上偵測 System Center Endpoint Protection (SCEP) 需要安裝 SCEP (PowerShell 3.0 或更高版本)。
> - 深度安全性代理程式支援偵測趨勢微保護。  不支援 OfficeScan 代理程式。


## <a name="next-steps"></a>後續步驟

- 瞭解[資訊安全中心如何收集資料和 Log Analytics 代理程式](security-center-enable-data-collection.md)。
- 瞭解[資訊安全中心如何管理和保護資料](security-center-data-security.md)。
- 了解如何[規劃及了解採用 Azure 資訊安全中心的設計考量](security-center-planning-and-operations-guide.md)。
- 查看[支援安全中心的平臺](security-center-os-coverage.md)。
- [在 Azure 資訊安全中心中深入瞭解 Windows 和 Linux 電腦的威脅防護](threat-protection.md#windows-machines)。
- 尋找[Azure 資訊安全中心](faq-general.md)的常見問題。