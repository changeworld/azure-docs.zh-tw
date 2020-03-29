---
title: 瞭解安全、Azure 管理的工作站 - Azure 活動目錄
description: 瞭解安全、Azure 管理的工作站，並瞭解它們的重要性。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05a3a8cf14a591dd3037175e4eed5b5bd8d3096c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672667"
---
# <a name="understand-secure-azure-managed-workstations"></a>瞭解安全、Azure 管理的工作站

安全、隔離的工作站對於管理員、開發人員和關鍵服務操作員等敏感角色的安全至關重要。 如果用戶端工作站安全性受到威脅，許多安全控制和保證可能會失敗或無效。

本文檔介紹了構建安全工作站（通常稱為特權訪問工作站 （PAW） 所需的內容。 本文還包含設置初始安全控制項的詳細說明。 本指南介紹了基於雲的技術如何管理服務。 它依賴于 Windows 10RS5、Microsoft 防禦者高級威脅保護 （ATP）、Azure 活動目錄和 Microsoft Intune 中引入的安全功能。

> [!NOTE]
> 本文介紹了安全工作站的概念及其重要性。 如果您已經熟悉了這個概念，並希望跳到部署，請訪問[部署安全工作站](howto-azure-managed-workstation.md)。

## <a name="why-secure-workstation-access-is-important"></a>為什麼安全工作站訪問很重要

雲服務的快速採用和隨時隨地工作的能力創造了一種新的利用方法。 通過在管理員工作的設備上利用薄弱的安全控制，攻擊者可以存取權限資源。

特權濫用和供應鏈攻擊是攻擊者用來破壞組織的五大方法之一。 根據[Verizon威脅報告](https://enterprise.verizon.com/resources/reports/dbir/)[和安全情報報告](https://aka.ms/sir)，在2018年報導的事件中，它們也是第二大最常被檢測到的戰術。

大多數攻擊者都遵循以下步驟：

1. 偵察，以找到一種方法，往往特定于一個行業。
1. 分析以收集資訊並確定滲透被視為低價值工作站的最佳方式。
1. 堅持尋找[橫向移動的方法](https://en.wikipedia.org/wiki/Network_Lateral_Movement)。
1. 洩露機密和敏感性資料。

在偵察過程中，攻擊者經常潛入風險低或被低估的設備。 他們使用這些易受攻擊的設備來尋找橫向移動的機會，並查找管理使用者和設備。 在獲取特權使用者角色後，攻擊者將識別高價值資料並成功洩露該資料。

![典型的折衷模式](./media/concept-azure-managed-workstation/typical-timeline.png)

本文檔介紹了一種解決方案，可説明保護您的計算裝置免受此類橫向攻擊。 該解決方案將管理和服務與價值較低的生產力設備隔離開來，在能夠滲透訪問敏感雲資源的設備之前，該鏈條就打破了這一鏈條。 該解決方案使用作為 Microsoft 365 企業版棧一部分的本機 Azure 服務：

* 裝置管理功能以及應用程式和 URL 的安全清單
* 用於設備設置、部署和刷新的自動駕駛儀
* 用於使用者管理、條件訪問和多重要素驗證的 Azure AD
* 適用于設備運行狀況證明和使用者體驗的 Windows 10（當前版本）
* 維護 ATP，用於雲管理的端點保護、檢測和回應
* Azure AD PIM，用於管理授權和對資源的即時 （JIT） 特權訪問
* 日誌分析和用於監視和警報的哨兵

## <a name="who-benefits-from-a-secure-workstation"></a>誰從安全工作站中獲益？

所有使用者和操作員在使用安全工作站時都受益。 危害 PC 或設備的攻擊者可以類比所有緩存的帳戶。 登錄到設備時，它們也可能使用憑據和權杖。 這種風險使得保護用於特權角色（包括管理許可權）的設備非常重要。 具有特權帳戶的設備是橫向移動和特權升級攻擊的目標。 這些帳戶可用於各種資產，例如：

* 本地或基於雲的系統的管理員
* 關鍵系統的開發人員工作站
* 高曝光率的社交媒體帳戶管理員
* 高度敏感的工作站，如 SWIFT 支付終端
* 處理商業機密的工作站

為了降低風險，您應該對使用這些帳戶的特權工作站實施提升的安全控制。 有關詳細資訊，請參閱 Azure[活動目錄功能部署指南](../fundamentals/active-directory-deployment-checklist-p2.md)[、Office 365 路線圖](https://aka.ms/o365secroadmap)[和保護特權訪問路線圖](https://aka.ms/sparoadmap)）。

## <a name="why-use-dedicated-workstations"></a>為什麼要使用專用工作站？

雖然可以將安全性添加到現有設備，但最好從安全基礎開始。 要使您的組織處於保持高安全級別的最佳位置，請從您知道安全的設備開始，並實施一組已知的安全控制。

通過電子郵件和 Web 流覽的攻擊媒介數量不斷增加，因此越來越難以確定設備可以受信任。 本指南假定專用工作站與標準生產力、流覽和電子郵件隔離開來。 從設備中刪除生產力、Web 流覽和電子郵件可能會對工作效率產生負面影響。 但是，對於作業任務沒有明確要求且安全事件風險很高的情況，此保護通常是可以接受的。

> [!NOTE]
> 此處的 Web 流覽是指對任意網站的一般訪問，這些網站可能是高風險活動。 這種流覽與使用 Web 瀏覽器訪問少量已知管理網站以訪問 Azure、Office 365、其他雲供應商和 SaaS 應用程式截然不同。

遏制策略通過增加阻止攻擊者獲取敏感資產的控制數量和類型來增強安全性。 本文中描述的模型使用分層許可權設計，並將管理許可權限制為特定設備。

## <a name="supply-chain-management"></a>供應鏈管理

安全工作站的基本要素是供應鏈解決方案，即使用稱為"根信任"的可信工作站。 在選擇信任硬體根目錄時必須考慮的技術應包括現代筆記本電腦中包含的以下技術： 

* [受信任的平臺模組 （TPM） 2.0](/windows-hardware/design/device-experiences/oem-tpm)
* [BitLocker 磁碟機加密](/windows-hardware/design/device-experiences/oem-bitlocker)
* [UEFI 安全開機](/windows-hardware/design/device-experiences/oem-secure-boot)
* [通過 Windows 更新分發的驅動程式和固件](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [虛擬化和啟用 HVCI](/windows-hardware/design/device-experiences/oem-vbs)
* [驅動程式和應用 HVCI 就緒](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [DMA I/O 保護](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [系統防護裝置](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [新式待命](/windows-hardware/design/device-experiences/modern-standby)

對於此解決方案，將使用[Microsoft 自動駕駛儀](/windows/deployment/windows-autopilot/windows-autopilot)技術部署根信任，硬體滿足現代技術要求。 為了保護工作站，自動駕駛儀允許您利用 Microsoft OEM 優化的 Windows 10 設備。 這些設備處於製造商已知的良好狀態。 自動駕駛儀可以將 Windows 設備轉換為"業務就緒"狀態，而不是重新映射潛在的不安全設備。 它應用設置和策略，安裝應用，甚至更改 Windows 10 的版本。 例如，自動駕駛儀可能會將設備的 Windows 安裝從 Windows 10 專業版更改為 Windows 10 企業版，以便它可以使用高級功能。

![安全工作站級別](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>設備角色和設定檔

本指南引用了幾個安全設定檔和角色，這些設定檔和角色可説明您為使用者、開發人員和 IT 人員創建更安全的解決方案。 這些設定檔平衡了普通使用者的可用性和風險，這些使用者可以從增強型或安全工作站中受益。 此處提供的設置配置基於行業接受的標準。 本指南演示如何強化 Windows 10 並減少與設備或使用者危害相關的風險。 為了利用現代硬體技術和信任設備的根源，我們將使用[設備運行狀況證明](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643)，這是從**高安全性**設定檔開始啟用的。 此功能可確保攻擊者在設備的早期啟動期間不能持久。 它通過使用策略和技術來説明管理安全功能和風險。
![安全工作站級別](./media/concept-azure-managed-workstation/seccon-levels.png)

* **基本安全性**– 託管的標準工作站為大多數家庭和小型企業使用提供了良好的起點。 這些設備在 Azure AD 中註冊，並在 Intune 中進行管理。 此設定檔允許使用者運行任何應用程式並流覽任何網站。 應啟用像 Microsoft [Defender](https://www.microsoft.com/windows/comprehensive-security)這樣的反惡意軟體解決方案。

* **增強的安全性**– 此入門級受保護解決方案適用于家庭使用者、小型企業使用者和一般開發人員。

   增強的工作站是提高低安全性安全性的基於策略的方法。 它提供了處理客戶資料的安全方法，同時還使用電子郵件和 Web 流覽等生產力工具。 您可以使用稽核原則和 Intune 監視增強的工作站的使用者行為和設定檔使用方式。 使用 Windows10 （1809） 腳本部署增強的工作站設定檔，並使用[高級威脅防護 （ATP）](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)利用高級惡意軟體防護。

* **高安全性**– 減少工作站攻擊面的最有效方法是消除自管理工作站的能力。 刪除本地管理許可權是提高安全性的一個步驟，但如果實施不正確，可能會影響工作效率。 高安全性設定檔建立在增強的安全設定檔之上，並進行了大量更改：刪除本地管理員。此設定檔專為高知名度使用者設計：高管、工資和敏感性資料使用者、服務和流程審批人。

   高安全性使用者需要一個更具控制性的環境，同時仍然能夠以便於使用的體驗執行電子郵件和 Web 流覽等活動。 使用者期望功能，如 Cookie、我的最愛和其他快捷方式才能正常工作。 但是，這些使用者可能不需要能夠修改或調試其設備。 它們也不需要安裝驅動程式。 使用高安全性 - Windows10 （1809） 腳本部署高安全性設定檔。

* **專門**– 攻擊者以開發人員和 IT 管理員為目標，因為他們可以更改攻擊者感興趣的系統。 專用工作站通過管理本地應用程式和限制網站來擴展高安全性工作站的策略。 它還限制高風險的生產力功能，如 ActiveX、JAVA、瀏覽器外掛程式和其他 Windows 控制項。 使用 DeviceConfiguration_NCSC - Windows10 （1803） 安全基線腳本部署此設定檔。

* **安全**– 危害管理帳戶的攻擊者可能會因數據竊取、資料更改或服務中斷而造成重大業務損害。 在此強化狀態下，工作站啟用所有限制直接控制本地應用程式管理的安全控制和策略。 安全的工作站沒有生產力工具，因此設備更難妥協。 它阻止網路釣魚攻擊的最常見媒介：電子郵件和社交媒體。 安全工作站可以隨安全工作站 - Windows10 （1809） 安全基線腳本一起部署。

   ![安全工作站](./media/concept-azure-managed-workstation/secure-workstation.png)

   安全工作站為管理員提供經過強化的工作站，該工作站具有清晰的應用程式控制和應用程式防護功能。 工作站使用憑據保護、設備保護和漏洞利用防護來保護主機免受惡意行為的影響。 所有本地磁片也使用 BitLocker 進行加密。

* **隔離**– 此自訂離線方案表示頻譜的極端端。 此案例未提供安裝腳本。 您可能需要管理需要不受支援或未修補的舊版作業系統的業務關鍵型功能。 例如，高價值生產線或生命支援系統。 由於安全性至關重要且雲服務不可用，因此您可以手動管理和更新這些電腦，或者使用隔離的 Active Directory 林體系結構（如增強安全管理環境 （ESAE）） 來管理和更新這些電腦。 在這些情況下，請考慮刪除除基本 Intune 和 ATP 運行狀況檢查之外的所有訪問。

   * [Intune 網路通信要求](/intune/network-bandwidth-use)
   * [ATP 網路通信要求](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>後續步驟

[部署安全的 Azure 管理工作站](howto-azure-managed-workstation.md)。
