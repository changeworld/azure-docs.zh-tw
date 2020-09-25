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
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: cece53544bfc8287f6ef542575d92b05032991f0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318036"
---
# <a name="feature-coverage-for-machines"></a>機器的功能涵蓋範圍

下列兩個索引標籤會顯示適用于 Windows 和 Linux 虛擬機器和伺服器的 Azure 資訊安全中心功能。

## <a name="supported-features-for-virtual-machines-and-servers"></a>虛擬機器和伺服器的支援功能 <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows 機器**](#tab/features-windows)

|**功能**|**Azure 虛擬機器**|**Azure 虛擬機器擴展集**|**非 Azure 機器**|**需要 Azure Defender**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP 整合](security-center-wdatp.md)|✔</br> (支援的版本) |✔</br> (支援的版本) |✔|是|
|[虛擬機器行為分析 (和安全性警示) ](alerts-reference.md)|✔|✔|✔|是|
|[無檔案安全性警示](alerts-reference.md#alerts-windows)|✔|✔|✔|是|
|[以網路為基礎的安全性警示](other-threat-protections.md#network-layer)|✔|✔|-|是|
|[Just-In-Time 虛擬機器存取](security-center-just-in-time.md)|✔|-|-|是|
|[原生弱點評定](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|是|
|[檔案完整性監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|是|
|[自適性應用程式控制](security-center-adaptive-application.md)|✔|-|✔|是|
|[網路地圖](security-center-network-recommendations.md#network-map)|✔|✔|-|是|
|[自適性網路強化](security-center-adaptive-network-hardening.md)|✔|-|-|是|
|[法規合規性儀表板 & 報表](security-center-compliance-dashboard.md)|✔|✔|✔|是|
|Docker 託管 IaaS 容器上的建議和威脅防護|-|-|-|是|
|遺漏 OS 修補程式評估|✔|✔|✔|Azure：免費<br><br>非 Azure：是|
|安全性錯誤配置評估|✔|✔|✔|Azure：否<br><br>非 Azure：是|
|[Endpoint protection 評定](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure：否<br><br>非 Azure：是|
|磁片加密評估|✔</br>適用于 [支援案例](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios) 的 () |✔|-|免費|
|協力廠商弱點評定|✔|-|-|免費|
|[網路安全性評定](security-center-network-recommendations.md)|✔|✔|-|免費|


### <a name="linux-machines"></a>[**Linux 機器**](#tab/features-linux)

|**功能**|**Azure 虛擬機器**|**Azure 虛擬機器擴展集**|**非 Azure 機器**|**需要 Azure Defender**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP 整合](security-center-wdatp.md)|-|-|-|是|
|[虛擬機器行為分析 (和安全性警示) ](security-center-alerts-iaas.md)|✔</br> (支援的版本) |✔</br> (支援的版本) |✔|是|
|[無檔案安全性警示](alerts-reference.md#alerts-windows)|-|-|-|是|
|[以網路為基礎的安全性警示](other-threat-protections.md#network-layer)|✔|✔|-|是|
|[Just-In-Time 虛擬機器存取](security-center-just-in-time.md)|✔|-|-|是|
|[原生弱點評定](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|是|
|[檔案完整性監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|是|
|[自適性應用程式控制](security-center-adaptive-application.md)|✔|-|✔|是|
|[網路地圖](security-center-network-recommendations.md#network-map)|✔|✔|-|是|
|[自適性網路強化](security-center-adaptive-network-hardening.md)|✔|-|-|是|
|[法規合規性儀表板 & 報表](security-center-compliance-dashboard.md)|✔|✔|✔|是|
|Docker 託管 IaaS 容器上的建議和威脅防護|✔|✔|✔|是|
|遺漏 OS 修補程式評估|✔|✔|✔|Azure：免費<br><br>非 Azure：是|
|安全性錯誤配置評估|✔|✔|✔|Azure：免費<br><br>非 Azure：是|
|[Endpoint protection 評定](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|免費|
|磁片加密評估|✔</br>適用于 [支援案例](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios) 的 () |✔|-|免費|
|協力廠商弱點評定|✔|-|-|免費|
|[網路安全性評定](security-center-network-recommendations.md)|✔|✔|-|免費|

--- 


> [!TIP]
>若要試驗僅適用于 Azure Defender 的功能，您可以註冊30天的試用版。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。


## <a name="supported-endpoint-protection-solutions"></a>支援的 endpoint protection 解決方案 <a name="endpoint-supported"></a>

下表提供的矩陣指出：

 - 您是否可以使用 Azure 資訊安全中心安裝適用於您的每個解決方案。
 - 資訊安全中心可以探索哪些端點保護解決方案。 如果探索到來自這份清單的 endpoint protection 解決方案，則安全性中心不會建議您安裝一個。

如需針對每個保護產生建議的詳細資訊，請參閱 [Endpoint Protection 評定和建議](security-center-endpoint-protection.md)。

| Endpoint Protection| 平台 | 資訊安全中心安裝 | 資訊安全中心探索 |
|------|------|-----|-----|
| Microsoft Defender 防毒軟體| Windows Server 2016 或更新版本| 否，內建於 OS| 是 |
| System Center Endpoint Protection (Microsoft 反惡意程式碼軟體) | Windows Server 2012 R2、2012、2008 R2 (請參閱下列附註) | 透過延伸模組 | 是 |
| 趨勢微-深層安全性 | Windows Server 系列  | 否 | 是 |
| Symantec v12.1.1100+| Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Linux 伺服器系列  | 否 | 是的 **\*** |
| Sophos V9 +| Linux 伺服器系列  | 否 | 是的  **\***  |

 **\*** 涵蓋範圍狀態和支援資料目前僅適用于與您受保護的訂閱相關聯的 Log Analytics 工作區。 它不會反映在 Azure 資訊安全中心入口網站中。

> [!NOTE]
> 在 Windows Server 2008 R2 虛擬機器上偵測 System Center Endpoint Protection (SCEP) 需要在 PowerShell (v3.0 或更新版本) 之後安裝 SCEP。


## <a name="next-steps"></a>後續步驟

- 瞭解 [安全性中心如何收集資料和 Log Analytics 代理程式](security-center-enable-data-collection.md)。
- 瞭解 [安全性中心如何管理和保護資料](security-center-data-security.md)。
- 檢查 [支援安全性中心的平臺](security-center-os-coverage.md)。