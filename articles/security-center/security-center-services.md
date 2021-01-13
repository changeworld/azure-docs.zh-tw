---
title: 根據 OS、機器類型和雲端來分類的 Azure 資訊安全中心功能
description: 了解根據其 OS、類型和雲端部署可使用哪些 Azure 資訊安全中心功能。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/24/2020
ms.author: memildin
ms.openlocfilehash: f3d1918eafe8d61d9c5a7db3f29b83b5412914c0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179554"
---
# <a name="feature-coverage-for-machines"></a>機器適用的功能涵蓋範圍

下列兩個索引標籤顯示 Windows 和 Linux 虛擬機器和伺服器適用的 Azure 資訊安全中心功能。

## <a name="supported-features-for-virtual-machines-and-servers"></a>虛擬機器和伺服器支援的功能 <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows 機器**](#tab/features-windows)

|**功能**|**Azure 虛擬機器**|**Azure 虛擬機器擴展集**|**已啟用 Azure Arc 的機器**|**需要 Azure Defender**
|----|:----:|:----:|:----:|:----:|
|[適用於端點的 Microsoft Defender 整合](security-center-wdatp.md)|✔</br>(在支援的版本上)|✔</br>(在支援的版本上)|✔|是|
|[虛擬機器行為分析 (和安全性警示)](alerts-reference.md)|✔|✔|✔|是|
|[無檔案安全性警示](alerts-reference.md#alerts-windows)|✔|✔|✔|是|
|[以網路為基礎的安全性警示](other-threat-protections.md#network-layer)|✔|✔|-|是|
|[Just-In-Time 虛擬機器存取](security-center-just-in-time.md)|✔|-|-|是|
|[原生弱點評量](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|是|
|[檔案完整性監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|是|
|[自適性應用程式控制](security-center-adaptive-application.md)|✔|-|✔|是|
|[網路地圖](security-center-network-recommendations.md#network-map)|✔|✔|-|是|
|[自適性網路強化](security-center-adaptive-network-hardening.md)|✔|-|-|是|
|[法規遵循儀表板和報表](security-center-compliance-dashboard.md)|✔|✔|✔|是|
|針對 Docker 裝載的 IaaS 容器上提供建議和威脅防護|-|-|-|是|
|缺少 OS 修補程式的評量|✔|✔|✔|Azure：否<br><br>已啟用 Arc：是|
|安全性設定錯誤的評量|✔|✔|✔|Azure：否<br><br>已啟用 Arc：是|
|[端點保護評量](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure：否<br><br>已啟用 Arc：是|
|磁碟加密評量|✔</br>(僅限[支援的案例](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|否|
|第三方弱點評量|✔|-|✔|否|
|[網路安全性評量](security-center-network-recommendations.md)|✔|✔|-|否|


### <a name="linux-machines"></a>[**Linux 機器**](#tab/features-linux)

|**功能**|**Azure 虛擬機器**|**Azure 虛擬機器擴展集**|**已啟用 Azure Arc 的機器**|**需要 Azure Defender**
|----|:----:|:----:|:----:|:----:|
|[適用於端點的 Microsoft Defender 整合](security-center-wdatp.md)|-|-|-|是|
|[虛擬機器行為分析 (和安全性警示)](./azure-defender.md)|✔</br>(在支援的版本上)|✔</br>(在支援的版本上)|✔|是|
|[無檔案安全性警示](alerts-reference.md#alerts-windows)|-|-|-|是|
|[以網路為基礎的安全性警示](other-threat-protections.md#network-layer)|✔|✔|-|是|
|[Just-In-Time 虛擬機器存取](security-center-just-in-time.md)|✔|-|-|是|
|[原生弱點評量](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|是|
|[檔案完整性監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|是|
|[自適性應用程式控制](security-center-adaptive-application.md)|✔|-|✔|是|
|[網路地圖](security-center-network-recommendations.md#network-map)|✔|✔|-|是|
|[自適性網路強化](security-center-adaptive-network-hardening.md)|✔|-|-|是|
|[法規遵循儀表板和報表](security-center-compliance-dashboard.md)|✔|✔|✔|是|
|針對 Docker 裝載的 IaaS 容器上提供建議和威脅防護|✔|✔|✔|是|
|缺少 OS 修補程式的評量|✔|✔|✔|Azure：否<br><br>已啟用 Arc：是|
|安全性設定錯誤的評量|✔|✔|✔|Azure：否<br><br>已啟用 Arc：是|
|[端點保護評量](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|否|
|磁碟加密評量|✔</br>(僅限[支援的案例](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|否|
|第三方弱點評量|✔|-|✔|否|
|[網路安全性評量](security-center-network-recommendations.md)|✔|✔|-|否|

--- 


> [!TIP]
>若要試驗僅適用於 Azure Defender 的功能，您可以註冊 30 天的試用版。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。


## <a name="supported-endpoint-protection-solutions"></a>支援的端點保護解決方案<a name="endpoint-supported"></a>

下表提供的矩陣指出：

 - 您是否可以使用 Azure 資訊安全中心安裝適用於您的每個解決方案。
 - 資訊安全中心可以探索哪些端點保護解決方案。 如果探索到此清單中有端點保護解決方案，資訊安全中心就不會建議安裝。

若要深入了解每一個保護的建議產生時機，請參閱[端點保護評量和建議](security-center-endpoint-protection.md)。

| Endpoint Protection| 平台 | 資訊安全中心安裝 | 資訊安全中心探索 |
|------|------|-----|-----|
| Microsoft Defender 防毒軟體| Windows Server 2016 或更新版本| 否，內建於 OS| 是 |
| System Center Endpoint Protection (Microsoft 反惡意程式碼軟體) | Windows Server 2012 R2、2012、2008 R2 (請參閱下列附註) | 透過延伸模組 | 是 |
| 趨勢科技 – Deep Security | Windows Server 系列  | 否 | 是 |
| Symantec v12.1.1100+| Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Linux 伺服器系列  | 否 | 是 * *\** _ |
| Sophos V9+| Linux 伺服器系列  | 否 | 是 _*\**_  |

 _*\**_ 目前僅限與受保護訂用帳戶相關聯的 Log Analytics 工作區可提供涵蓋範圍狀態和支援資料。 其不會反映在 Azure 資訊安全中心入口網站中。

> [!NOTE]
> 在 Windows Server 2008 R2 虛擬機器上偵測 System Center Endpoint Protection (SCEP) 需要在 PowerShell (v3.0 或更新的版本) 之後安裝 SCEP。



## <a name="feature-support-in-government-clouds"></a>政府雲端中的功能支援

| 服務/功能 | US Gov | 中國 Gov |
|------|:----:|:----:|
|[Just-In-Time 虛擬機器存取](security-center-just-in-time.md) (1)|✔|✔|
|[檔案完整性監視](security-center-file-integrity-monitoring.md) (1)|✔|✔|
|[自適性應用程式控制](security-center-adaptive-application.md) (1)|✔|✔|
|[自適性網路強化](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Docker 主機強化](harden-docker-hosts.md) (1)|✔|✔|
|[機器的整合式弱點評估](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[適用於端點的 Microsoft Defender](harden-docker-hosts.md) (1)|✔|-|
|[連接 AWS 帳戶](quickstart-onboard-aws.md) (1)|-|-|
|[連接 GCP 帳戶](quickstart-onboard-gcp.md) (1)|-|-|
|[連續匯出](continuous-export.md)|✔|✔ (2)|
|[工作流程自動化](workflow-automation.md)|✔|✔|
|[建議豁免規則](exempt-resource.md)|-|-|
|[重複警示歸併規則](alerts-suppression-rules.md)|✔|✔|
|[安全性警示的電子郵件通知](security-center-provide-security-contact-details.md)|✔|✔|
|[資產詳細目錄](asset-inventory.md)|✔|✔|
|[適用於 App Service 的 Azure Defender](defender-for-app-service-introduction.md)|-|-|
|[適用於儲存體的 Azure Defender](defender-for-storage-introduction.md)|✔|-|
|[適用於 SQL 的 Azure Defender](defender-for-sql-introduction.md)|✔|✔ (2)|
|[適用於 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)|-|-|
|[適用於 Resource Manager 的 Azure Defender](defender-for-resource-manager-introduction.md)|-|-|
|[適用於 DNS 的 Azure Defender](defender-for-dns-introduction.md)|-|-|
|[適用於 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md)|✔|✔|
|[適用於容器登錄的 Azure Defender](defender-for-container-registries-introduction.md)|✔ (2)|✔ (2)|
|||

(1) 需要 *適用於伺服器的 Azure Defender**

(2) 部份


## <a name="next-steps"></a>後續步驟

- 了解[資訊安全中心如何使用 Log Analytics 代理程式來收集資料](security-center-enable-data-collection.md)。
- 了解[資訊安全中心如何管理和保護資料](security-center-data-security.md)。
- 檢閱[支援資訊安全中心的平台](security-center-os-coverage.md)。