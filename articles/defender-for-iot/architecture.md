---
title: 適用于 IoT 的 Azure Defender 架構
description: 瞭解適用于 IoT 的 Azure Defender 架構和資訊流程。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: rkarlin
ms.openlocfilehash: 4189cb8628db58343c816535836af82825014b7e
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096312"
---
# <a name="azure-defender-for-iot-architecture"></a>適用于 IoT 的 Azure Defender 架構

本文說明 Defender for IoT 解決方案的功能性系統架構。

## <a name="defender-for-iot-components"></a>適用于 IoT 的 Defender 元件

適用于 IoT 的 Defender 可連接到 Azure 雲端以及內部部署元件。 解決方案是針對具有多個遠端位置的大型和地理位置分散式環境中的擴充性而設計的。 此解決方案可依國家/地區、區域、業務單位或區域，啟用多層的分散式架構。 

適用于 IoT 的 Azure Defender 包含下列元件： 

**雲端連線部署**

- 適用于 IoT 的 Azure Defender 感應器 VM 或設備
- Azure 入口網站雲端管理和整合至 Azure Sentinel
- 本機網站管理的內部部署管理主控台
- 內嵌的安全性代理程式 (選擇性) 

** (離線) 部署的空調**

- 適用于 IoT 的 Azure Defender 感應器 VM 或設備
- 本機網站管理的內部部署管理主控台


![適用于 IoT 的 Defender 架構](./media/architecture/defender-iot-security-architecture.png)

### <a name="azure-defender-for-iot-sensors"></a>適用于 IoT 的 Azure Defender 感應器

Defender for IoT 感應器會探索和持續監視網路裝置。 感應器會使用被動 (無代理程式) 監視 IoT 和 OT 裝置來收集 ICS 網路流量。 
 
無代理程式技術是專為 IoT 和內部網路所建立，可在連線到網路的幾分鐘內，提供 IoT 的深度可見度和風險。 由於網路和網路裝置的非侵入性、網路流量分析 (NTA) 方法，因此對網路和網路裝置有零的效能影響。 
 
利用專利、IoT 及可感知的行為分析和第7層的深層封包檢查 (DPI) ，它可讓您分析超越傳統的簽章式解決方案，以立即偵測出 advanced IoT 和威脅 (例如根據異常或未經授權的活動無檔案惡意程式碼) 。 
  
Defender for IoT 感應器會連線至 SPAN 埠或網路點，並立即開始執行 IoT 和網路流量的 DPI。 
 
資料收集、處理、分析和警示會直接在感應器上進行。 這最適合用於頻寬較低或高延遲連線的位置，因為只會將中繼資料傳送到管理主控台。

感應器包含五個分析偵測引擎。 引擎會根據即時和預先記錄的流量分析來觸發警示。 以下是可用的引擎： 

#### <a name="protocol-violation-detection-engine"></a>通訊協定違規偵測引擎
通訊協定違規偵測引擎會識別違反 ICS 通訊協定規格的封包結構和域值的使用，例如： Modbus 例外狀況，以及起始過時的函式程式碼警示。

#### <a name="policy-violation-detection-engine"></a>原則違規偵測引擎
使用機器學習服務時，原則違規偵測引擎會警示使用者與基準行為的任何偏差，例如未授權使用特定的函式程式碼、存取特定物件，或變更裝置設定。 例如： DeltaV 軟體版本變更，以及未授權的 PLC 程式設計警示。 具體來說，原則違規引擎會以具決定性的狀態和轉換順序將 ICS 網路模型化，並使用稱為工業有限狀態模型 (IFSM) 的專利技術。 原則違規偵測引擎會建立 ICS 網路的基準，因此平臺需要較短的學習期間來建立網路基準，而不是一般的數學方法或分析，原本是針對它開發，而不是使用網路。

#### <a name="industrial-malware-detection-engine"></a>產業惡意程式碼偵測引擎
產業惡意程式碼偵測引擎會識別指出已知惡意程式碼（例如 Conficker、黑色能源、Havex、WannaCry、NotPetya 和 Triton）存在的行為。 

#### <a name="anomaly-detection-engine"></a>異常偵測引擎
異常偵測引擎會偵測到不尋常的機器對機器 (M2M) 通訊和行為。 藉由以具決定性的狀態和轉換順序將 ICS 網路模型化，平臺需要比原本為其開發的一般數學方法或分析更短的學習期間，而不是。 它也會以較短的誤報來更快偵測異常狀況。 異常偵測引擎警示包括過多的 SMB 登入嘗試，以及 PLC 掃描偵測到的警示。

#### <a name="operational-incident-detection"></a>操作事件偵測
作業事件偵測會偵測操作問題，例如間歇性連線能力，這可能表示設備故障的早期徵兆。 例如，裝置被懷疑中斷連線 (沒有回應) ，而且 Siemens S7 stop PLC 命令已傳送警示。


### <a name="management-consoles"></a>管理主控台
透過兩個管理入口網站來管理跨混合式環境的 Azure Defender for IoT： 
- 感應器主控台
- 內部部署管理主控台
- Azure 入口網站

#### <a name="sensor-console"></a>感應器主控台
感應器偵測會顯示在感應器主控台中，可供您在網路地圖、資產清查和廣泛的報表中進行查看、調查及分析，例如風險評量報告、資料採礦查詢和攻擊媒介。 您也可以使用主控台來查看和處理感應器引擎偵測到的威脅、將資訊轉送至協力廠商系統、管理使用者等。

![適用于 IoT 的 Defender 感應器主控台](./media/architecture/sensor-console.png)

#### <a name="on-premises-management-console"></a>內部部署管理主控台
內部部署管理主控台可讓安全性作業中心 (SOC) 操作員管理及分析從多個感應器匯總到單一儀表板的警示，並提供對 OT 網路健康情況的整體觀點。

此架構在 SOC 層級提供完整的網路整合觀點、優化的警示處理，以及操作網路安全性的控制，以確保決策和風險管理保持完美。

除了多租使用者、監視、資料分析和集中式感應器遠端控制之外，管理主控台還提供額外的系統維護工具 (例如警示排除) ，以及針對每個遠端設備的完整自訂報告功能。 此可調整的架構支援網站層級的本機管理、區域層級，以及 SOC 內的全域管理。

您可以針對高可用性設定部署管理主控台，以提供備份主控台，定期接收復原所需的所有設定檔案的備份。 如果主要主控台失敗，本機網站管理設備將會自動容錯移轉，以與備份主控台同步處理，以維持可用性而不會中斷。

#### <a name="azure-portal"></a>Azure 入口網站

Azure 中的 Defender for IoT 入口網站可用來協助您：·  購買解決方案設備·  安裝和更新軟體·  將感應器上架到 Azure ·  更新威脅情報套件

## <a name="embedded-security-agent-built-in-mode"></a>Embedded security agent：內建模式

在內 **建** 模式中，當您選擇在 iot 中樞中開啟 **安全性** 選項時，會啟用 Defender for IoT。 內建模式提供即時監視、建議和警示，提供單一步驟的裝置可見度和無與倫比的安全性。 內建模式不需要在任何裝置上安裝代理程式，而且會在已記錄的活動上使用 advanced analytics 來分析及保護您的欄位裝置和 IoT 中樞。

## <a name="embedded-security-agent-enhanced-mode"></a>Embedded security agent：增強模式

在 **增強** 模式中，開啟 iot 中樞的 [ **安全性** ] 選項，並在裝置上安裝適用于 IoT 裝置代理程式的 Defender 之後，代理程式會從您的裝置收集、匯總及分析原始安全性事件。 原始安全性事件可以包含 IP 連線、進程建立、使用者登入，以及其他安全性相關資訊。 適用于 IoT 的 Defender 裝置代理程式也會處理事件匯總，以協助避免高的網路輸送量。 代理程式具有高度自訂性，可讓您將它們用於特定的工作，例如只傳送最快速 SLA 的重要資訊，或是將大量的安全性資訊和內容匯總到較大的區段，以避免產生更高的服務成本。

裝置代理程式和其他應用程式會使用 **Azure 傳送安全性訊息 SDK** 將安全性資訊傳送至 Azure IoT 中樞。 IoT 中樞會取得此資訊，並將其轉送至適用于 IoT 的 Defender 服務。

一旦啟用 Defender for IoT 服務，除了轉送的資料之外，IoT 中樞也會傳送其內部資料，以供 Defender 針對 IoT 進行分析。 此資料包含裝置雲端作業記錄、裝置身分識別和中樞設定。 所有這些資訊都有助於建立適用于 IoT 分析管線的 Defender。

適用于 IoT 分析管線的 Defender 也會從 Microsoft 和 Microsoft 合作夥伴內的各種來源接收額外的威脅情報串流。 適用于 IoT 的 Defender 整個分析管線適用于服務 (的每個客戶設定，例如自訂警示和傳送安全性訊息 SDK) 的使用。

流量分析管線，適用于 IoT 的 Defender 會合並所有資訊串流，以產生可採取動作的建議和警示。 此管線包含安全性研究人員和專家所建立的自訂規則，以及搜尋標準裝置行為和風險分析偏差的機器學習模型。

適用于 IoT 的 Defender 建議和警示 (分析管線輸出) 會寫入至每個客戶的 Log Analytics 工作區。 在工作區中包含未經處理的事件以及警示和建議，可讓您使用所偵測到的可疑活動的確切詳細資料，深入探討調查和查詢。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解 Defender for IoT 解決方案的基本架構和工作流程。 若要深入瞭解必要條件、如何在 IoT 中樞開始使用並啟用您的安全性解決方案，請參閱下列文章：

- [服務必要條件](service-prerequisites.md)
- [快速入門](getting-started.md)
- [設定您的解決方案](quickstart-configure-your-solution.md)
- [啟用 IoT 中樞的安全性](quickstart-onboard-iot-hub.md)
- [適用于 IoT 的 Defender 常見問題](resources-frequently-asked-questions.md)
- [適用于 IoT 的 Defender 安全性警示](concept-security-alerts.md)
