---
title: Azure 中虛擬機器的安全性建議
description: 針對 Azure 中的 Vm 套用這些建議，以協助滿足共同責任模型中所述的安全性義務，並提升部署的整體安全性。
author: msmbaldwin
manager: RKarlin
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.reviewer: cynthn
ms.openlocfilehash: 0bc56159d0e9a616d8da8dfe1ec3c128f60ef44a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342986"
---
# <a name="security-recommendations-for-virtual-machines-in-azure"></a>Azure 中虛擬機器的安全性建議


本文包含 Azure 虛擬機器的安全性建議。 遵循這些建議，以協助滿足我們的共同責任模型中所述的安全性義務。 這些建議也可協助您提升 web 應用程式解決方案的整體安全性。 如需 Microsoft 如何滿足服務提供者責任的詳細資訊，請參閱 [雲端運算的共同責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

Azure 資訊安全中心會自動解決本文的一些建議。 Azure 資訊安全中心是您在 Azure 中的資源的第一道防線。 這會定期分析 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 接著，它會建議如何解決這些弱點。 如需詳細資訊，請參閱 [Azure 資訊安全中心中的安全性建議](../security-center/security-center-recommendations.md)。

如需 Azure 資訊安全中心的一般資訊，請參閱 [什麼是 Azure 資訊安全中心？](../security-center/security-center-intro.md)。

## <a name="general"></a>一般

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 當您建立自訂的 VM 映射時，請套用最新的更新。 | 在您建立映射之前，請為作業系統和將成為映射一部分的所有應用程式安裝最新的更新。  | - |
| 讓 VM 保持最新狀態。 | 您可以使用 Azure 自動化中的 [更新管理](../automation/update-management/update-mgmt-overview.md) 解決方案，在 Azure 中管理 Windows 和 Linux 電腦的作業系統更新。 | [是](../security-center/security-center-apply-system-updates.md) |
| 備份您的 Vm。 | [Azure 備份](../backup/backup-overview.md) 可協助保護您的應用程式資料，並擁有較低的營運成本。 應用程式錯誤可能導致資料損毀，而人為錯誤可能會將 Bug 導入應用程式中。 Azure 備份保護執行 Windows 和 Linux 的 Vm。 | - |
| 使用多個 Vm 來提高復原能力和可用性。 | 如果您的 VM 執行必須高度可用的應用程式，請使用多個 Vm 或 [可用性設定組](../virtual-machines/windows/manage-availability.md)。 | - |
| 採用商務持續性和嚴重損壞修復 (BCDR) 策略。 | Azure Site Recovery 可讓您從針對支援商務持續性而設計的不同選項中進行選擇。 它支援不同的複寫和容錯移轉案例。 如需詳細資訊，請參閱  [關於 Site Recovery](../site-recovery/site-recovery-overview.md)。 | - |

## <a name="data-security"></a>資料安全性

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 加密作業系統磁片。 | [Azure 磁碟加密](../security/azure-security-disk-encryption-overview.md) 可協助您加密 Windows 和 LINUX IaaS VM 磁片。 如果沒有必要的金鑰，加密磁片的內容就無法讀取。 磁片加密可保護儲存的資料免于未經授權的存取，如果磁片已複製，則可能會發生這種情況。| [是](../security-center/security-center-apply-disk-encryption.md) |
| 加密資料磁片。 | [Azure 磁碟加密](../security/azure-security-disk-encryption-overview.md) 可協助您加密 Windows 和 LINUX IaaS VM 磁片。 如果沒有必要的金鑰，加密磁片的內容就無法讀取。 磁片加密可保護儲存的資料免于未經授權的存取，如果磁片已複製，則可能會發生這種情況。| -  |
| 限制已安裝的軟體。 | 將已安裝的軟體限制為成功套用解決方案所需的軟體。 此指導方針可協助減少您解決方案的攻擊面。 | - |
| 使用防毒軟體或反惡意程式碼。 | 在 Azure 中，您可以使用來自安全性廠商（例如 Microsoft、Symantec、Trend 微和 Kaspersky）的反惡意程式碼軟體。 此軟體可協助保護您的 Vm 免于惡意檔案、廣告軟體和其他威脅。 您可以根據您的應用程式工作負載部署 Microsoft Antimalware。 Microsoft Antimalware 僅適用于 Windows 電腦。 您可以使用基本的預設安全性或 advanced custom configuration。 如需詳細資訊，請參閱 [Azure 雲端服務和虛擬機器的 Microsoft Antimalware](../security/azure-security-antimalware.md)。 | - |
| 安全地儲存金鑰和秘密。 | 以安全且集中管理的選項提供您的應用程式擁有者，以簡化您的秘密和金鑰管理。 此管理可降低意外洩漏或流失的風險。 Azure Key Vault 可以安全地將金鑰儲存在硬體安全性模組中， (已通過 FIPS 140-2 層級2認證的 Hsm) 。 如果您需要使用 FIPs 140.2 層級3來儲存金鑰和秘密，您可以使用 [Azure 專用 HSM](../dedicated-hsm/overview.md)。 | - |

## <a name="identity-and-access-management"></a>身分識別和存取管理 

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 集中式 VM 驗證。 | 您可以使用 [Azure Active Directory authentication](../active-directory/develop/authentication-scenarios.md)，將 Windows 和 Linux vm 的驗證集中在一起。 | - |

## <a name="monitoring"></a>監視

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 監視您的 Vm。 | 您可以使用 [適用於 VM 的 Azure 監視器](../azure-monitor/insights/vminsights-overview.md) 來監視 Azure vm 和虛擬機器擴展集的狀態。 VM 的效能問題可能會導致服務中斷，違反可用性的安全性原則。 | - |

## <a name="networking"></a>網路功能

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 限制對管理埠的存取。 | 攻擊者會針對開啟的管理埠掃描公用雲端 IP 範圍，並嘗試「簡單」的攻擊，例如常見密碼和已知未修補的弱點。 您可以使用 [即時 (JIT) VM 存取](../security-center/security-center-just-in-time.md) 來鎖定 Azure vm 的輸入流量、降低暴露于攻擊的風險，同時在需要時輕鬆連線至 vm。 | - |
| 限制網路存取。 | 網路安全性群組可讓您限制網路存取，並控制公開端點的數目。 如需詳細資訊，請參閱 [建立、變更或刪除網路安全性群組](../virtual-network/manage-network-security-group.md)。 | - |

## <a name="next-steps"></a>後續步驟

請洽詢您的應用程式提供者，以瞭解其他安全性需求。 如需有關開發安全應用程式的詳細資訊，請參閱 [安全開發檔](../security/fundamentals/abstract-develop-secure-apps.md)。