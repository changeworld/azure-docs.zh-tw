---
title: 主要優點
description: 瞭解適用于 IoT 的基本 Defender 概念。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 1ac87f98af555aae155a201cc20692c950fc7924
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838638"
---
# <a name="basic-concepts"></a>基本概念 

本文說明適用于 IoT 的 Azure Defender 的主要優點。

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>快速的非侵入式部署和被動監視

Defender for IoT 感應器會連線至 SPAN 埠或網路點，並立即開始透過被動 (無代理程式) 監視來收集 ICS 網路流量。 深層封包檢查 (DPI) 用來仔細分析來自串列和 Ethernet 控制網路設備的流量。 適用于 IoT 的 Defender 對對等網路沒有任何影響，因為它不是放在資料路徑中，而且不會主動掃描 OT 裝置。 

為了提供詳細資產資訊的即時快照，Defender for IoT 感應器會使用選擇性的作用中元件來補充被動監視。 此元件使用安全、廠商核准的命令，盡可能地查詢 Windows 和控制器裝置的資產詳細資料，或盡可能不常使用。

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>ICS 通訊協定、裝置和應用程式的內嵌知識

DPI 本身不足以找出通訊協定異常，並在細微層級識別裝置。 適用于 IoT 的 Defender 感應器可解決一些最大且最複雜的環境。 在所有產業部門中，已將超過1300個的網路分析為最新狀態。

## <a name="analytics-and-self-learning-engines"></a>分析和自我學習引擎

引擎透過連續監視和五種分析引擎來識別安全性問題，這些引擎結合了自我學習，以消除更新簽章或定義規則的需求。 引擎會使用 ICS 專屬的行為分析和資料科學，持續分析有沒有異常的網路流量。 這五個引擎為：

- **通訊協定違規偵測**：識別如何使用違反 ICS 通訊協定規格的封包結構和域值。

- **原則違規偵測**：識別原則違規，例如未授權的函式程式碼使用、特定物件的存取權，或資產設定的變更。

- **產業惡意程式碼偵測**：識別指出已知惡意程式碼（例如 Conficker、黑色能源、Havex、WannaCry 和 NotPetya）存在的行為。

- **異常偵測**：偵測不尋常的機器對機器 (M2M) 通訊和行為。 藉由以具決定性的狀態和轉換順序將 ICS 網路模型化，引擎會使用稱為工業有限狀態模型 (IFSM) 的專利技術。 解決方案需要比一般數學方法或分析更短的學習期間，原本是針對它開發，而不是使用。 它也會以較短的誤報來更快偵測異常狀況。

- 作業 **事件偵測**：識別像是間歇性連線能力的操作問題，這些問題可能表示設備故障的早期徵兆。

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>風險和弱點評估的網路流量分析

在業界的獨特之處，Defender for IoT 會使用專屬的網路流量分析 (NTA) 演算法，被動地識別所有網路和端點弱點，例如：

- 未授權的遠端存取連接
- Rogue 或未記載的裝置
- 弱式驗證
- 有弱點的裝置 (根據未修補的 Cve) 
- 子網之間的未經授權橋接器
- 弱式防火牆規則

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>用於調查、取證和威脅搜尋的資料採礦

平臺提供直覺的資料採礦介面，可讓您更精細地搜尋所有相關維度的歷程記錄流量。 範例包括時間週期、IP 位址、MAC 位址和埠。 您也可以根據函數代碼、通訊協定服務和模組，進行通訊協定特定的查詢。 完全精確度的 PCAPs 可供進一步向下切入分析。

## <a name="sensor-cloud-management-mode"></a>感應器雲端管理模式

感應器雲端管理模式會決定感應器偵測到的裝置、警示和其他資訊的顯示位置。

針對 **連線到雲端的感應器**，感應器所偵測到的資訊會顯示在感應器主控台中。 警示資訊是透過 IoT 中樞傳遞，並可與其他 Azure 服務共用，例如 Azure Sentinel。

針對 **本機連線的感應器**，感應器偵測的資訊會顯示在感應器主控台中。 如果感應器連線到內部部署管理主控台，則也會與其共用偵測資訊。

## <a name="air-gapped-networks"></a>空調網路

如果您在有空調的環境中工作，適用于 IoT 的 Defender 中的內部部署管理主控台可提供關鍵 IoT 的即時觀點，以及所有設備的風險指標和警示。 與 SOC 工作流程和 runbook 緊密整合，可讓您輕鬆地排定風險降低的風險，以及跨網站的相互關聯。  

適用于 IoT 的 Defender 提供所有裝置的匯總視圖。 它也提供有關裝置的重要資訊，例如類型 (PLC、RTU、DC，以及更多) 、製造商、型號和固件修訂層級，以及警示資訊。  

適用于 IoT 的 Defender 可有效管理多個部署，以及全方位的網路整合觀點。 適用于 IoT 的 Defender 可將警示處理和操作網路安全性的控制優化。

內部部署管理主控台是一個以網頁為基礎的系統管理平臺，可讓您監視和控制全域感應器安裝的活動。 除了管理從已部署感應器接收的資料之外，內部部署管理主控台還可順暢地整合各種商務資源的資料： Cmdb、DNS、防火牆、Web Api 等等。

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="內部部署管理主控台顯示。":::

在使用內部部署管理主控台之前，建議您先熟悉感應器可用的概念、功能和功能。

## <a name="integrations"></a>整合

您可以透過與夥伴系統共用裝置和警示資訊，來擴充適用于 IoT 的 Defender 功能。 整合可協助企業橋接先前孤立的安全性解決方案，以大幅增強裝置可見度和威脅情報。 整合也可協助企業加速整個系統的回應，並更快速地降低風險。 

整合會降低複雜度，並將其與您現有的 SOC 工作流程和安全性堆疊整合，以消除其和接收器。 例如：

- Siem，例如 IBM QRadar、Splunk、ArcSight、LogRhythm 和 RSA NetWitness

- 安全性協調流程和票證系統，例如 ServiceNow 和 IBM 的復原能力

- 安全的遠端存取解決方案，例如 CyberArk 的特殊許可權會話管理員 (的 PSM) 和 BeyondTrust

-  (NAC) 系統（例如 Aruba ClearPass 和 Forescout）的安全網路存取控制

- 防火牆，例如 Fortinet 和 Check Point

## <a name="complete-protocol-support"></a>完整的通訊協定支援

除了內嵌的通訊協定支援，您還可以保護執行專屬和自訂通訊協定的 IoT 和 ICS 裝置，或與任何標準的通訊協定。 藉由使用範圍開放式開發環境 (NODE.JS) SDK，開發人員可以建立 dissector 外掛程式，以根據定義的通訊協定來解碼網路流量。 服務會分析流量，以提供完整的監視、警示和報告。 使用範圍：

- 展開 [可見度] 和 [控制]，而不需要升級至新版本。

- 以外部外掛程式的形式開發網站，以保護專屬資訊。

- 將警示、事件和通訊協定參數的文字當地語系化。

此外，您還可以使用專屬通訊協定警示來傳達資訊：

- 關於以專用範圍外掛程式中的通訊協定和基礎通訊協定為基礎的流量偵測。

- 關於來自所有通訊協定層的通訊協定欄位組合。 例如，在執行 MODBUS 的環境中，您可能會想要在感應器偵測到特定 IP 位址和乙太網路目的地上的記憶體暫存器有寫入命令時產生警示。 或者，您可能會想要在對特定 IP 位址執行任何存取時產生警示。

當符合範圍警示規則條件時，就會觸發警示。

此外，使用範圍自訂警示可讓您撰寫自己的警示標題和訊息。 已解析的通訊協定欄位和值可以內嵌在警示郵件內文中。

使用自訂、以條件為基礎的警示觸發和訊息，有助於找出特定的網路活動，並有效更新您的安全性、IT 和營運團隊。


## <a name="high-availability"></a>高可用性

在內部部署管理主控台中安裝高可用性設備，以增加 Defender for IoT 部署的復原能力。 高可用性部署可確保您的受控感應器會持續向作用中的內部部署管理主控台報告。

此部署會使用包含主要和次要設備的內部部署管理主控台配對來執行。

## <a name="localization"></a>當地語系化

許多主控台功能支援廣泛的語言。

## <a name="next-step"></a>後續步驟

[開始使用 Defender for IoT](getting-started.md)
