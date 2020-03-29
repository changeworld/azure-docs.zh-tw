---
title: Azure IoT 邊緣安全管理器 - Azure IoT 邊緣
description: 管理 IoT Edge 裝置安全性立場和安全性服務的完整性。
services: iot-edge
keywords: 安全性、安全性元素、enclave、TEE、IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548691"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge 安全性管理員

Azure IoT Edge 安全性管理員是有完整界限的安全性核心，它透過將安全晶片硬體抽象化，來保護 IoT Edge 裝置與其所有元件。 安全管理器是安全強化的協調中心，為原始設備製造商 （OEM） 提供技術集成點。

![Azure IoT Edge 安全性管理員](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge 安全性管理員的目標是保護 IoT Edge 裝置與所有固有軟體作業的完整性。 安全管理器將信任從信任硬體的基礎硬體根（如果可用）轉換為引導 IoT Edge 運行時並監視正在進行的操作。  IoT Edge 安全性管理員是搭配可用安全晶片硬體運作的軟體，有助於提供最高安全性保證。  

IoT Edge 安全性管理員的責任包括但不限於：

* Azure IoT Edge 裝置的啟動受保護且受測量。
* 佈建裝置身分識別，以及在適用的情況下轉換信任。
* 裝載及保護雲端服務 (如裝置佈建服務) 的裝置元件。
* 使用唯一身分識別安全地佈建 IoT Edge 模組。
* 透過公證服務作為硬體根信任的閘道管理員。
* 在執行階段監視 IoT Edge 作業的完整性。

IoT Edge 安全性管理員包含三項元件：

* IoT Edge 安全性精靈。
* 硬體安全性模組平台抽象層 (HSM PAL)。
* 硬體晶片根信任或 HSM，雖然是選擇性，但強烈建議包含它。

## <a name="the-iot-edge-security-daemon"></a>IoT Edge 安全性精靈

IoT Edge 安全守護進程負責 IoT Edge 安全管理器的邏輯操作。 它代表了 IoT Edge 設備的可信計算基礎的很大一部分。

### <a name="design-principles"></a>設計原則

IoT Edge 安全性精靈會遵循兩個核心原則：達到最大的作業完整性，並且盡可能降低膨脹和變換。

#### <a name="maximize-operational-integrity"></a>將運作完整性最大化

IoT Edge 安全守護程式在任何給定信任硬體的根的防禦能力範圍內以盡可能高的完整性運行。 透過適當的整合，根信任硬體會靜態地以及在執行階段測量及監視安全性精靈，以防止竄改。

實體存取一向對於 IoT 裝置構成威脅。 硬體根信任在防禦 IoT Edge 安全性精靈的完整性中扮演重要角色。  信任的硬體根有兩種：

* 適用於敏感性資訊 (如祕密和密碼編譯金鑰) 保護的安全元素。
* 適用於祕密保護 (如金鑰) 與敏感性工作負載 (如計量和計費) 的安全性保護區。

存在兩種執行環境來使用信任的硬體根：

* 依賴使用安全元素來保護敏感資訊的標準或豐富的執行環境 （REE）。
* 依賴使用安全安全區技術來保護敏感資訊並提供軟體執行保護的可信執行環境 （TEE）。

對於使用安全安全區作為信任硬體根的設備，IoT Edge 安全守護程式中的敏感邏輯應位於安全區內。  安全守護程式的非敏感部分可以不在 TEE 之外。  在任何情況下，原始設計製造商 （ODM） 和原始設備製造商 （OEM） 都應從 HSM 中建立信任，以在啟動和運行時測量和捍衛 IoT Edge 安全守護程式的完整性。

#### <a name="minimize-bloat-and-churn"></a>將膨脹和變動最小化

IoT Edge 安全性精靈的另一個核心原則是將變換最小化。  針對最高層級的信任，IoT Edge 安全性精靈可與裝置硬體根信任緊密耦合，並以原生程式碼的方式運作。  這些類型的實現通常通過信任的安全更新路徑的硬體根（而不是 OS 提供的更新機制）更新守護進程軟體，這在某些情況下可能具有挑戰性。  雖然建議對 IoT 設備進行安全續訂，但過多的更新要求或大型更新負載可能會以多種方式擴展威脅面。  範例包括跳過更新以將作業可用性最大化，或根信任硬體太受限而無法處理大型更新承載。  因此，IoT Edge 安全守護程式的設計簡潔明瞭，可保持佔用空間和受信任的計算基礎小，並最大限度地減少更新要求。

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge 安全性精靈的架構

![Azure IoT Edge 安全性精靈](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge 安全守護程式利用信任技術的任何可用硬體根進行安全強化。  當硬體技術提供受信任的執行環境時，它還允許在標準/富執行環境 （REE） 和受信任的執行環境 （TEE） 之間進行拆分世界操作。 特定于角色的介面使 IoT Edge 的主要元件能夠確保 IoT Edge 設備及其操作的完整性。

#### <a name="cloud-interface"></a>雲端介面

雲介面允許 IoT Edge 安全守護進程訪問雲服務，如雲對設備安全性（如安全續訂）的恭維。  例如，IoT Edge 安全守護程式當前使用此介面訪問 Azure IoT 中心[設備佈建服務](https://docs.microsoft.com/azure/iot-dps/)，用於設備標識生命週期管理。  

#### <a name="management-api"></a>管理 API

IoT Edge 安全守護程式提供了一個管理 API，在創建/啟動/停止/刪除 IoT Edge 模組時，IoT Edge 代理會調用該 API。 安全守護進程存儲所有活動模組的"註冊"。 這些註冊將模組的身分識別對應到模組的某些屬性。 例如，這些模組屬性包括在容器中運行的進程的進程識別碼 （pid） 和 docker 容器內容的雜湊。

這些屬性由工作負載 API（如下所述）用於驗證調用方是否授權執行操作。

管理 API 是一個特權 API，只能從 IoT 邊緣代理調用。  由於 IoT Edge 安全守護進程啟動並啟動 IoT Edge 代理，因此它驗證 IoT Edge 代理未被篡改，因此可以為 IoT Edge 代理創建隱式註冊。 工作負載 API 使用的相同證明過程也限制對管理 API 的訪問僅限於 IoT Edge 代理。

#### <a name="container-api"></a>容器 API

容器 API 與用於模組管理的容器系統（如 Moby 或 Docker）進行交互。

#### <a name="workload-api"></a>工作負載 API

所有模組均可訪問工作負載 API。 它提供身份證明，無論是作為 HSM 根簽名權杖還是 X509 證書，也提供模組的相應信任捆綁包。 信任組合包含模組應該信任之所有其他伺服器的 CA 憑證。

IoT Edge 安全守護進程使用證明過程來保護此 API。 當模組調用此 API 時，安全守護進程將嘗試查找標識的註冊。 如果成功，它會使用註冊的屬性來測量模組。 如果測量過程的結果與註冊匹配，則生成新的身份證明。 對應的 CA 憑證 (信任組合) 會傳回到模組。  模組使用此憑證來連線到 IoT 中樞、其他模組或用來啟動伺服器。 當簽名的權杖或證書即將過期時，模組有責任請求新證書。

### <a name="integration-and-maintenance"></a>整合和維護

Microsoft [在 GitHub 上維護 IoT Edge 安全性精靈](https://github.com/Azure/iotedge/tree/master/edgelet) \(英文\) 的主要程式碼基底。

#### <a name="installation-and-updates"></a>安裝和更新

IoT Edge 安全性精靈的安裝和更新是透過作業系統的套件管理系統來管理。 IoT Edge 裝置應該有硬體根信任，並且通常會透過安全性開機和更新管理系統來為精靈的完整性提供額外的強化。 設備製造商應根據各自的裝置功能探索這些途徑。

#### <a name="versioning"></a>版本控制

IoT Edge 執行階段會追蹤並回報 IoT Edge 安全性精靈的版本。 版本是回報為 IoT Edge 代理程式模組報告屬性 (property) 的 *runtime.platform.version* 屬性 (attribute)。

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>硬體安全性模組平台抽象層 (HSM PAL)

HSM PAL 將所有根信任硬體抽象化，以將 IoT Edge 開發人員或使用者從其複雜性中分離。  它包括應用程式開發介面 （API） 和跨域通信過程的組合，例如標準執行環境和安全安全區之間的通信。  HSM PAL 的實際實作取決於使用中的特定安全硬體而定。 它的存在使幾乎任何安全晶片硬體都能使用。

## <a name="secure-silicon-root-of-trust-hardware"></a>安全晶片根信任硬體

必須有安全晶片才能在 IoT Edge 裝置硬體內固定信任。  安全晶片有各種類型，包括信賴平台模組 (TPM)、內嵌安全性元素 (eSE)、ARM 信任區、Intel SGX 及自訂安全晶片技術。  考慮到與 IoT 設備的物理可訪問性相關的威脅，建議在設備中使用安全矽根信任。

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge 安全性管理員整合和維護

對於自訂強化功能，IoT Edge 安全性管理員能夠識別並隔離負責維護 Azure IoT Edge 平台安全性和完整性的元件。 裝置製造商等協力廠商應該利用可和其裝置硬體搭配使用的自訂安全性功能。  請參閱＜後續步驟＞一節，其中的連結顯示如何在 Linux 和 Windows 平台上使用信賴平台模組 (TPM) 來強化 Azure IoT 安全性管理員。 這些範例是使用軟體或虛擬 TPM，但也直接套用至使用離散 TPM 裝置的情形。  

## <a name="next-steps"></a>後續步驟

閱讀部落格文章[保護智慧邊緣](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) \(英文\)。

[在 Linux 虛擬機器上使用虛擬 TPM](how-to-auto-provision-simulated-device-linux.md) 建立及佈建 IoT Edge 裝置。

[在 Windows 上建立並佈建模擬 TPM 的 IoT Edge 裝置](how-to-auto-provision-simulated-device-windows.md)。
