---
title: Azure IoT Edge 安全性管理員-Azure IoT Edge
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76548691"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge 安全性管理員

Azure IoT Edge 安全性管理員是有完整界限的安全性核心，它透過將安全晶片硬體抽象化，來保護 IoT Edge 裝置與其所有元件。 安全性管理員是強化安全性的焦點，並提供技術整合點給原始設備製造商（OEM）。

![Azure IoT Edge 安全性管理員](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge 安全性管理員的目標是保護 IoT Edge 裝置與所有固有軟體作業的完整性。 安全性管理員會從信任硬體的基礎硬體根（如果有的話）轉換信任，以啟動 IoT Edge 執行時間並監視進行中的作業。  IoT Edge 安全性管理員是搭配可用安全晶片硬體運作的軟體，有助於提供最高安全性保證。  

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

IoT Edge 安全性守護程式負責 IoT Edge 安全性管理員的邏輯作業。 它代表 IoT Edge 裝置之受信任計算基礎的重要部分。

### <a name="design-principles"></a>設計原則

IoT Edge 安全性精靈會遵循兩個核心原則：達到最大的作業完整性，並且盡可能降低膨脹和變換。

#### <a name="maximize-operational-integrity"></a>將運作完整性最大化

IoT Edge 的安全性背景程式會在任何指定的信任硬體根的防禦功能內，以最高的完整性運作。 透過適當的整合，根信任硬體會靜態地以及在執行階段測量及監視安全性精靈，以防止竄改。

實體存取一向對於 IoT 裝置構成威脅。 硬體根信任在防禦 IoT Edge 安全性精靈的完整性中扮演重要角色。  硬體根信任有兩種種類：

* 適用於敏感性資訊 (如祕密和密碼編譯金鑰) 保護的安全元素。
* 適用於祕密保護 (如金鑰) 與敏感性工作負載 (如計量和計費) 的安全性保護區。

有兩種執行環境是使用硬體根信任：

* 標準或豐富的執行環境（REE），其依賴安全元素的使用來保護機密資訊。
* 信賴的執行環境（也就是使用安全記憶體保護區技術來保護機密資訊，並提供軟體執行的保護）。

針對使用安全記憶體保護區作為硬體根信任的裝置，IoT Edge 安全性守護程式內的機密邏輯應該在記憶體保護區內。  安全性背景程式的非敏感性部分可以不在該範圍外。  在任何情況下，原始設計製造商（ODM）和原始設備製造商（OEM）應從其 HSM 延伸信任，以測量及防禦開機和執行時間的 IoT Edge 安全性守護程式的完整性。

#### <a name="minimize-bloat-and-churn"></a>將膨脹和變動最小化

IoT Edge 安全性精靈的另一個核心原則是將變換最小化。  針對最高層級的信任，IoT Edge 安全性精靈可與裝置硬體根信任緊密耦合，並以原生程式碼的方式運作。  這些類型的實踐通常會透過硬體根信任的安全更新路徑（而不是作業系統提供的更新機制）更新 daemon 軟體，這在某些情況下可能會很困難。  雖然建議對 IoT 裝置進行安全性更新，但過多的更新需求或大型更新裝載可以透過許多方式擴充威脅面。  範例包括跳過更新以將作業可用性最大化，或根信任硬體太受限而無法處理大型更新承載。  因此，IoT Edge 安全性守護程式的設計，是為了讓使用量和受信任的計算基底變小，並將更新需求降到最低而簡單。

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge 安全性精靈的架構

![Azure IoT Edge 安全性精靈](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge 安全性守護程式會利用任何可用的硬體根信任技術來強化安全性。  當硬體技術提供信任的執行環境時，也允許在標準/豐富執行環境（REE）和信任的執行環境（t）之間進行分割世界作業。 角色特定的介面可讓 IoT Edge 的主要元件，確保 IoT Edge 裝置及其作業的完整性。

#### <a name="cloud-interface"></a>雲端介面

雲端介面可讓 IoT Edge 的安全性背景程式存取雲端服務（例如雲端補充），使其成為安全性更新之類的裝置安全性。  例如，IoT Edge 的安全性守護程式目前使用此介面來存取裝置身分識別生命週期管理的 Azure IoT 中樞裝置布建[服務](https://docs.microsoft.com/azure/iot-dps/)。  

#### <a name="management-api"></a>管理 API

IoT Edge security daemon 提供管理 API，在建立/啟動/停止/移除 IoT Edge 模組時，IoT Edge 代理程式會呼叫它。 安全性守護程式會儲存所有作用中模組的「註冊」。 這些註冊將模組的身分識別對應到模組的某些屬性。 例如，這些模組屬性包含在容器中執行之進程的處理序識別碼（pid），以及 docker 容器內容的雜湊。

這些屬性是由工作負載 API 所使用（如下所述），以確認呼叫端已獲授權可進行動作。

管理 API 是特殊許可權的 API，只能從 IoT Edge 代理程式呼叫。  由於 IoT Edge security daemon 會啟動並啟動 IoT Edge 代理程式，因此它會確認 IoT Edge 代理程式未遭到篡改，然後可以為 IoT Edge 代理程式建立隱含的註冊。 工作負載 API 所使用的相同證明程式也會將管理 API 的存取限制為只有 IoT Edge 代理程式。

#### <a name="container-api"></a>容器 API

容器 API 會與用於模組管理的容器系統互動，例如 Moby 或 Docker。

#### <a name="workload-api"></a>工作負載 API

所有模組都可存取工作負載 API。 它會提供身分識別證明，可能是 HSM 根簽署權杖或 X509 憑證，以及對應至模組的信任配套。 信任組合包含模組應該信任之所有其他伺服器的 CA 憑證。

IoT Edge security daemon 會使用證明程式來保護此 API。 當模組呼叫此 API 時，安全性守護程式會嘗試尋找身分識別的註冊。 如果成功，它會使用註冊的屬性來測量模組。 如果測量流程的結果符合註冊，則會產生新的身分識別證明。 對應的 CA 憑證 (信任組合) 會傳回到模組。  模組使用此憑證來連線到 IoT 中樞、其他模組或用來啟動伺服器。 當簽署的權杖或憑證接近到期日時，模組會負責要求新的憑證。

### <a name="integration-and-maintenance"></a>整合和維護

Microsoft [在 GitHub 上維護 IoT Edge 安全性精靈](https://github.com/Azure/iotedge/tree/master/edgelet) \(英文\) 的主要程式碼基底。

#### <a name="installation-and-updates"></a>安裝和更新

IoT Edge 安全性精靈的安裝和更新是透過作業系統的套件管理系統來管理。 IoT Edge 裝置應該有硬體根信任，並且通常會透過安全性開機和更新管理系統來為精靈的完整性提供額外的強化。 裝置製造商應根據其各自的裝置功能來探索這些途徑。

#### <a name="versioning"></a>版本控制

IoT Edge 執行階段會追蹤並回報 IoT Edge 安全性精靈的版本。 版本是回報為 IoT Edge 代理程式模組報告屬性 (property) 的 *runtime.platform.version* 屬性 (attribute)。

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>硬體安全性模組平台抽象層 (HSM PAL)

HSM PAL 將所有根信任硬體抽象化，以將 IoT Edge 開發人員或使用者從其複雜性中分離。  其中包含應用程式開發介面（API）和交易網域通訊程式的組合，例如標準執行環境與安全記憶體保護區之間的通訊。  HSM PAL 的實際實作取決於使用中的特定安全硬體而定。 它的存在使幾乎任何安全晶片硬體都能使用。

## <a name="secure-silicon-root-of-trust-hardware"></a>安全晶片根信任硬體

必須有安全晶片才能在 IoT Edge 裝置硬體內固定信任。  安全晶片有各種類型，包括信賴平台模組 (TPM)、內嵌安全性元素 (eSE)、ARM 信任區、Intel SGX 及自訂安全晶片技術。  建議在裝置中使用安全晶片根信任，以取得與 IoT 裝置的實體可存取性相關聯的威脅。

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge 安全性管理員整合和維護

對於自訂強化功能，IoT Edge 安全性管理員能夠識別並隔離負責維護 Azure IoT Edge 平台安全性和完整性的元件。 裝置製造商等協力廠商應該利用可和其裝置硬體搭配使用的自訂安全性功能。  請參閱＜後續步驟＞一節，其中的連結顯示如何在 Linux 和 Windows 平台上使用信賴平台模組 (TPM) 來強化 Azure IoT 安全性管理員。 這些範例是使用軟體或虛擬 TPM，但也直接套用至使用離散 TPM 裝置的情形。  

## <a name="next-steps"></a>後續步驟

閱讀部落格文章[保護智慧邊緣](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) \(英文\)。

[在 Linux 虛擬機器上使用虛擬 TPM](how-to-auto-provision-simulated-device-linux.md) 建立及佈建 IoT Edge 裝置。

[在 Windows 上建立並佈建模擬 TPM 的 IoT Edge 裝置](how-to-auto-provision-simulated-device-windows.md)。
