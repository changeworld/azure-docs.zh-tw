---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: df577ab2b5e9658fd55324c8fd6fd008621b4d46
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545878"
---
本文包含 Azure 虛擬機器的安全建議。 遵循這些建議,以説明履行我們共享責任模型中描述的安全義務。 這些建議還將説明您提高 Web 應用解決方案的整體安全性。 有關 Microsoft 如何履行服務提供者職責的詳細資訊,請參閱[雲計算的共享責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

Azure 安全中心可以自動解決本文的某些建議。 Azure 安全中心是 Azure 中資源的第一道防線。 它定期分析 Azure 資源的安全狀態,以識別潛在的安全漏洞。 然後,它建議如何解決漏洞。 有關詳細資訊,請參閱[Azure 安全中心中的安全建議](../articles/security-center/security-center-recommendations.md)。

有關 Azure 安全中心的一般資訊,請參閱[什麼是 Azure 安全中心?](../articles/security-center/security-center-intro.md)

## <a name="general"></a>一般

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 生成自定義 VM 映射時,請應用最新更新。 | 在創建映射之前,請安裝作業系統和將成為映像一部分的所有應用程式的最新更新。  | - |
| 讓 VM 保持最新狀態。 | 可以使用 Azure 自動化中的[「更新管理」](../articles/automation/automation-update-management.md)解決方案管理 Azure 中的 Windows 和 Linux 電腦的作業系統更新。 | [是](../articles/security-center/security-center-apply-system-updates.md) |
| 備份 VM。 | [Azure 備份](../articles/backup/backup-overview.md)有助於保護應用程序數據,並且運營成本最低。 應用程式錯誤可能導致資料損毀，而人為錯誤可能會將 Bug 導入應用程式中。 Azure 備份可保護運行 Windows 和 Linux 的 VM。 | - |
| 使用多個 VM 提高彈性和可用性。 | 如果 VM 執行的應用程式必須高度可用,請使用多個 VM 或[可用性集](../articles/virtual-machines/windows/manage-availability.md)。 | - |
| 採用業務連續性和災難恢復 (BCDR) 戰略。 | Azure 網站恢復允許您從旨在支援業務連續性的不同選項中進行選擇。 它支援不同的複製和故障轉移方案。 有關詳細資訊,請參閱[有關網站恢復](../articles/site-recovery/site-recovery-overview.md)。 | - |

## <a name="data-security"></a>資料安全性

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 加密作業系統磁碟。 | [Azure 磁碟加密](../articles/security/azure-security-disk-encryption-overview.md)可説明您加密 Windows 和 Linux IaaS VM 磁碟。 如果沒有必要的密鑰,加密磁碟的內容是不可讀的。 磁碟加密可保護儲存的數據免受未經授權的訪問,否則,如果複製磁碟,則有可能進行訪問。| [是](../articles/security-center/security-center-apply-disk-encryption.md) |
| 加密數據磁碟。 | [Azure 磁碟加密](../articles/security/azure-security-disk-encryption-overview.md)可説明您加密 Windows 和 Linux IaaS VM 磁碟。 如果沒有必要的密鑰,加密磁碟的內容是不可讀的。 磁碟加密可保護儲存的數據免受未經授權的訪問,否則,如果複製磁碟,則有可能進行訪問。| -  |
| 限制已安裝的軟體。 | 將已安裝的軟體限制為成功應用解決方案所需的軟體。 本指南有助於減少解決方案的攻擊面。 | - |
| 使用防病毒或反惡意軟體。 | 在 Azure 中,可以使用來自安全供應商(如 Microsoft、賽門鐵克、趨勢科技和卡巴斯基)的反惡意軟體。 該軟體有助於保護您的 VM 免受惡意文件、廣告軟體和其他威脅。 您可以根據應用程式工作負載部署 Microsoft 反惡意軟體。 使用基本安全預設配置或高級自定義配置。 有關詳細資訊,請參閱適用於[Azure 雲端服務和虛擬機器的 Microsoft 反惡意軟體](../articles/security/azure-security-antimalware.md)。 | - |
| 安全地存儲金鑰和機密。 | 通過向應用程式擁有者提供安全的集中管理選項,簡化機密和密鑰的管理。 此管理降低了意外洩露或洩漏的風險。 Azure 密鑰保管庫可以安全地將密鑰存儲在經過 FIPS 140-2 級 2 認證的硬體安全模組 (HSM) 中。 如果需要使用 FIP 140.2 等級 3 來儲存金鑰和機密,則可以使用[Azure 專用 HSM](../articles/dedicated-hsm/overview.md)。 | - |

## <a name="identity-and-access-management"></a>身分識別和存取管理 

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 集中 VM 身份驗證。 | 您可以使用[Azure 活動目錄身份驗證](../articles/active-directory/develop/authentication-scenarios.md)集中化 Windows 和 Linux VM 的身份驗證。 | - |

## <a name="monitoring"></a>監視

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 監視 VM。 | 可以使用[VM 的 Azure 監視器](../articles/azure-monitor/insights/vminsights-overview.md)監視 Azure VM 和虛擬機縮放集的狀態。 VM 的效能問題可能會導致服務中斷，違反可用性的安全性原則。 | - |

## <a name="networking"></a>網路功能

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 限制對管理埠的訪問。 | 攻擊者掃描公共雲 IP 範圍以訪問開放管理埠,並嘗試「簡單」攻擊,如常見密碼和已知的未修補漏洞。 您可以使用及時[(JIT) VM 訪問](../articles/security-center/security-center-just-in-time.md)來鎖定 Azure VM 的入站流量,從而減少攻擊風險,同時在需要時提供與 VM 的輕鬆連接。 | - |
| 限制網路訪問。 | 網路安全組允許您限制網路訪問並控制暴露終結點的數量。 關於詳細資訊,請參閱[建立、變更或移除網路安全群組](../articles/virtual-network/manage-network-security-group.md)。 | - |

## <a name="next-steps"></a>後續步驟

請諮詢應用程式供應商以瞭解其他安全要求。 有關開發安全應用程式的詳細資訊,請參閱[安全開發文件](../articles/security/fundamentals/abstract-develop-secure-apps.md)。
