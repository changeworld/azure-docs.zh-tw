---
title: 瞭解安全、受 Azure 管理的工作站-Azure Active Directory
description: 瞭解安全、受 Azure 管理的工作站，並瞭解其重要性。
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
ms.openlocfilehash: 4298518dae9afdf197321073846fa1ab9262ec0a
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601379"
---
# <a name="understand-secure-azure-managed-workstations"></a>瞭解安全、受 Azure 管理的工作站

安全、隔離的工作站對於機密角色（例如系統管理員、開發人員和重要服務操作員）的安全性至關重要。 如果用戶端工作站安全性遭到入侵，許多安全性控制和保證可能會失敗或無效。

本檔說明建立安全工作站（通常稱為特殊許可權存取工作站 (PAW) 所需的內容）。 本文也包含設定初始安全性控制的詳細指示。 本指南說明雲端技術如何管理此服務。 它依賴 Windows 10RS5 引進的安全性功能，Microsoft Defender 進階威脅防護 (ATP) 、Azure Active Directory 和 Microsoft Intune。

> [!NOTE]
> 本文說明安全工作站的概念及其重要性。 如果您已經熟悉該概念，而且想要跳至部署，請造訪 [部署安全工作站](howto-azure-managed-workstation.md)。

## <a name="why-secure-workstation-access-is-important"></a>為什麼安全的工作站存取很重要

雲端服務的快速採用，以及從任何地方工作的能力，都已建立新的惡意探索方法。 藉由在系統管理員工作的裝置上利用弱式安全性控制，攻擊者可以存取特殊許可權資源。

有許可權的誤用和供應鏈攻擊是攻擊者用來入侵組織的前五種方法之一。 它們也是根據 [Verizon 威脅報告](https://enterprise.verizon.com/resources/reports/dbir/)和 [安全情報報告](https://aka.ms/sir)，在2018中報告的第二個最常偵測到的策略。

大部分的攻擊者都遵循下列步驟：

1. 偵察以找出一種方式，通常是產業特有的。
1. 用來收集資訊的分析，並找出滲透可辨識為低價值工作站的最佳方式。
1. 持續性以尋找 [橫向](https://en.wikipedia.org/wiki/Network_Lateral_Movement)移動的方式。
1. 機密和機密資料的遭到外泄。

在偵察期間，攻擊者經常會滲透看似低風險或低估的裝置。 他們使用這些易受攻擊的裝置來找出橫向移動的機會，以及尋找系統管理使用者和裝置。 在他們取得特殊許可權使用者角色的存取權之後，攻擊者就能找出高價值資料，並成功竊取該資料。

![一般入侵模式](./media/concept-azure-managed-workstation/typical-timeline.png)

本檔說明可協助保護您的電腦不受這類橫向攻擊的解決方案。 解決方案會將管理和服務與較不具價值的生產力裝置隔離，而在可以滲透可存取機密雲端資源的裝置之前，請先中斷鏈。 解決方案會使用屬於 Microsoft 365 企業版 stack 一部分的原生 Azure 服務：

* 適用于裝置管理的 Intune，以及應用程式和 Url 的安全清單
* 裝置設定、部署和重新整理的 Autopilot
* 用於使用者管理、條件式存取和多重要素驗證的 Azure AD
* Windows 10 (裝置健康情況證明和使用者體驗的目前版本) 
* 適用于雲端管理的 endpoint protection、偵測和回應的 Defender ATP
* Azure AD PIM 來管理授權和及時 (JIT) 特殊許可權存取資源
* 用於監視和警示的 Log Analytics 和 Sentinel

## <a name="who-benefits-from-a-secure-workstation"></a>誰從安全的工作站受益？

使用安全工作站時，所有使用者和操作員都能獲得説明。 入侵電腦或裝置的攻擊者可以模擬所有快取的帳戶。 登入裝置時，可能也會使用認證和權杖。 這項風險有助於保護用於特殊許可權角色的裝置，包括系統管理許可權。 具有特殊許可權帳戶的裝置是橫向移動和許可權提升攻擊的目標。 這些帳戶可用於各種不同的資產，例如：

* 內部部署或雲端式系統的系統管理員
* 重要系統的開發人員工作站
* 具有高曝光性的社交媒體帳戶管理員
* 高度機密的工作站，例如 SWIFT 付款終端機
* 工作站處理交易秘密

若要降低風險，您應該針對使用這些帳戶的特殊許可權工作站，執行更高的安全性控制。 如需詳細資訊，請參閱 [Azure Active Directory 功能部署指南](../fundamentals/active-directory-deployment-checklist-p2.md)、 [Microsoft 365 藍圖](https://aka.ms/o365secroadmap)和 [保護特殊許可權存取藍圖](https://aka.ms/sparoadmap)) 。

## <a name="why-use-dedicated-workstations"></a>為何要使用專用工作站？

雖然可以將安全性新增至現有的裝置，但最好是從安全的基礎開始。 若要將您的組織保持在最佳的位置以維持高安全性等級，請從您知道安全的裝置著手，並執行一組已知的安全性控制。

透過電子郵件和網頁流覽進行的攻擊媒介數目不斷增加，讓您難以確定可以信任裝置。 本指南假設專用工作站與標準生產力、流覽和電子郵件隔離。 從裝置移除生產力、網頁流覽和電子郵件可能會對生產力造成負面影響。 不過，這種保護措施通常可在作業工作未明確要求的情況下接受，而安全性事件的風險則很高。

> [!NOTE]
> 此處的網頁瀏覽指的是可成為高風險活動之任意網站的一般存取權。 這類流覽與使用網頁瀏覽器來存取 Azure、Microsoft 365、其他雲端提供者和 SaaS 應用程式等服務的少數知名系統管理網站截然不同。

內含專案策略藉由增加防止攻擊者取得存取敏感性資產的控制項數目和類型，來加強安全性。 本文所述的模型使用階層式許可權設計，並限制特定裝置的系統管理許可權。

## <a name="supply-chain-management"></a>供應鏈管理

安全工作站的必要項是供應鏈解決方案，您可以在其中使用稱為「根信任目錄」的受信任工作站。 在信任硬體的選取範圍內，必須考慮的技術應該包含新式膝上型電腦中包含的下列技術： 

* [信賴平臺模組 (TPM) 2。0](/windows-hardware/design/device-experiences/oem-tpm)
* [BitLocker 磁碟機加密](/windows-hardware/design/device-experiences/oem-bitlocker)
* [UEFI 安全開機](/windows-hardware/design/device-experiences/oem-secure-boot)
* [透過 Windows Update 散發的驅動程式和固件](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [虛擬化和 HVCI 已啟用](/windows-hardware/design/device-experiences/oem-vbs)
* [驅動程式和應用程式 HVCI 就緒](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [DMA i/o 保護](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [系統防護](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [新式待命](/windows-hardware/design/device-experiences/modern-standby)

針對此解決方案，根信任目錄將會使用 [Microsoft Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) 技術來部署，而硬體符合現代化的技術需求。 為了保護工作站，Autopilot 可讓您利用 Microsoft OEM 優化的 Windows 10 裝置。 這些裝置從製造商進入已知的良好狀態。 Autopilot 可以將 Windows 裝置轉換成「企業就緒」狀態，而不是重新製作可能不安全裝置的映射。 它會套用設定和原則、安裝應用程式，甚至變更 Windows 10 的版本。 例如，Autopilot 可能會將裝置的 Windows 安裝從 Windows 10 專業版變更為 Windows 10 企業版，讓它可以使用先進的功能。

![安全工作站層級](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>裝置角色和設定檔

本指南參考數個安全性設定檔和角色，可協助您為使用者、開發人員和 IT 人員建立更安全的解決方案。 這些設定檔會針對可從增強或安全工作站獲益的一般使用者，平衡可用性和風險。 此處提供的設定是根據業界接受的標準。 本指南說明如何強化 Windows 10，並減少裝置或使用者入侵的相關風險。 為了充分利用新式硬體技術和信任裝置的根目錄，我們將使用 [裝置健康情況證明](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643)，其會從 **高安全性** 設定檔開始啟用。 這項功能的存在，可確保攻擊者在裝置早期開機期間無法持續。 它是藉由使用原則和技術來協助管理安全性功能和風險。
![安全工作站層級](./media/concept-azure-managed-workstation/seccon-levels.png)

* **基本安全性** -受管理的標準工作站提供了一個不錯的起點，讓您能夠在家庭和小型企業中使用。 這些裝置會在 Azure AD 中註冊，並使用 Intune 進行管理。 此設定檔允許使用者執行任何應用程式，並流覽任何網站。 應啟用反惡意程式碼解決方案（例如 [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) ）。

* **增強的安全性** –這個進入層級的受保護解決方案適合家庭使用者、小型企業使用者和一般開發人員。

   增強的工作站是以原則為基礎的方式，可提高低安全性設定檔的安全性。 它提供安全的方式來處理客戶資料，同時也使用電子郵件和網頁流覽等生產力工具。 您可以使用稽核原則和 Intune 來監視增強的工作站，以進行使用者行為和設定檔的使用。 您可以使用 Windows10 (1809) 腳本來部署增強的工作站設定檔，它會利用 advanced [威脅防護 (ATP) ](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)來利用先進的惡意程式碼防護。

* **高安全性** –減少工作站的受攻擊面最有效的方法，就是移除自行管理工作站的能力。 移除本機系統管理許可權是改善安全性的步驟，但如果未正確執行，可能會影響生產力。 在增強式安全性設定檔上建立的高安全性設定檔，有一項重大變更：移除本機系統管理員。此設定檔是專為高階設定檔使用者所設計：主管、薪資和敏感性資料使用者、服務和流程的核准者。

   高安全性使用者需要更受控制的環境，但仍能以簡單易用的體驗來執行電子郵件和網頁流覽等活動。 使用者預期會有 cookie、我的最愛及其他快速鍵等功能可運作。 不過，這些使用者可能不需要修改或偵測其裝置的能力。 它們也不需要安裝驅動程式。 高安全性設定檔是使用高安全性-Windows10 (1809) 腳本進行部署。

* **特製** 化–攻擊者是以開發人員和 IT 系統管理員為目標，因為他們可以改變攻擊者感興趣的系統。 特製化工作站藉由管理本機應用程式和限制網站，來擴充高安全性工作站的原則。 它也會限制高風險的生產力功能，例如 ActiveX、JAVA、browser 外掛程式和其他 Windows 控制項。 您可以使用 DeviceConfiguration_NCSC Windows10 (1803) SecurityBaseline 腳本來部署此設定檔。

* **安全** -入侵系統管理帳戶的攻擊者，可能會因數據遭竊、資料改變或服務中斷而造成重大的業務損害。 在此強化狀態下，工作站會啟用所有安全性控制和原則，以限制本機應用程式管理的直接控制。 安全的工作站沒有生產力工具，因此裝置更難入侵。 它會封鎖網路釣魚攻擊最常見的向量：電子郵件和社交媒體。 安全的工作站可以使用安全的工作站 Windows10 (1809) SecurityBaseline 腳本來部署。

   ![受保護的工作站](./media/concept-azure-managed-workstation/secure-workstation.png)

   安全工作站提供具有明確應用程式控制和應用程式防護的強化工作站系統管理員。 工作站使用 credential guard、device guard 和惡意探索防護來保護主機免于惡意行為。 所有本機磁片也會以 BitLocker 加密。

* **獨立** -這個自訂的離線案例代表頻譜的極端端。 在此情況下，不會提供任何安裝腳本。 您可能需要管理需要不支援或未修補之舊版作業系統的商務關鍵功能。 例如，高價值的生產線或生命支援系統。 由於安全性很重要，且雲端服務無法使用，因此您可以手動或使用隔離的 Active Directory 樹系架構（例如增強的安全性系統管理員環境 (ESAE) ）來管理和更新這些電腦。 在這些情況下，請考慮移除基本 Intune 和 ATP 健康情況檢查以外的所有存取權。

   * [Intune 網路通訊需求](/intune/network-bandwidth-use)
   * [ATP 網路通訊需求](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>後續步驟

[部署安全的 Azure 管理工作站](howto-azure-managed-workstation.md)。
