---
title: 服務概觀
description: 深入瞭解適用于 IoT 功能和服務的 Defender，並瞭解適用于 IoT 的 Defender 如何提供全方位的 IoT 安全性。
services: defender-for-iot
ms.service: azure
documentationcenter: na
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2020
ms.openlocfilehash: 5b4ab207462955be3876dc1a25fae491e48a9cd2
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621705"
---
# <a name="welcome-to-azure-defender-for-iot"></a>歡迎使用適用于 IoT 的 Azure Defender

營運技術 (的) 網路可為社會的許多最重要層面提供許多功能。 但是這些技術中有許多都不是以安全性為考慮而設計，而且無法使用傳統的 IT 安全性控制來保護。 同時，物聯網 (IoT) 會透過數十億個連接的裝置來啟用新的創新浪潮，進而增加受攻擊面和風險。  

適用于 IoT 的 Azure Defender 是統一的安全性解決方案，用來識別 IoT/OT 裝置、弱點和威脅。 無論您是否需要保護現有的 IoT/OT 裝置，或建立新 IoT 創新的安全性，都可讓您保護整個 IoT/OT 環境。  

適用于 IoT 的 Azure Defender 提供兩組功能，以符合您的環境需求。

針對具有 IoT/OT 環境的終端使用者組織，適用于 IoT 的 Azure Defender 會提供無代理程式、網路層監視，以：

- 可以快速部署。
- 利用各式各樣的產業設備和 SOC 工具輕鬆整合。
- 對 IoT/OT 網路效能或穩定性沒有任何影響。 

平臺可以完全在內部部署或在 Azure 連線和混合式環境中部署。  

針對 IoT 裝置產生器，適用于 IoT 的 Azure Defender 也提供輕量的微代理程式，可支援標準的 IoT 作業系統，例如 Linux 和 RTO。 此輕量代理程式可協助確保從邊緣到雲端的 IoT/OT 計畫內建安全性。 它包含適用于彈性、可自訂部署的原始程式碼。 

## <a name="agentless-solution-for-organizations"></a>適用于組織的無代理程式解決方案 

較舊的 IoT 和 OT 裝置不支援代理程式，且通常未修補、設定不正確，且不會對 IT 小組隱藏。 這些品質讓想要深入瞭解公司網路的威脅執行者成為其目標。 

針對公司 IT 網路開發的傳統網路安全性監視工具無法解決這些環境，因為他們缺乏深入瞭解 IoT 和 OT 環境中的特殊通訊協定、裝置和機器對機器 (M2M) 行為。 

適用于 IoT 的 Azure Defender 中的無代理程式監視功能提供這些網路的可見度和安全性。 然後，您可以解決這些環境的重要考慮。 

### <a name="automatic-device-discovery"></a>自動裝置探索  

使用被動、無代理程式網路監視，以完整清查所有 IoT/OT 裝置、其詳細資料，以及它們的通訊方式，對 IoT/OT 網路的影響不會產生任何影響。  

### <a name="proactive-visibility-into-risk-and-vulnerabilities"></a>主動查看風險和弱點
 
識別 IoT/OT 環境中的風險和弱點。 例如，識別未修補的裝置、開啟的埠、未經授權的應用程式，以及未授權的連接。 您也可以識別裝置設定、PLC 程式碼和固件的變更。 

### <a name="iotot-threat-detection"></a>IoT/OT 威脅偵測  

使用特製化 IoT/OT 感知威脅情報和行為分析，偵測異常或未經授權的活動。 您甚至可以偵測靜態 IOCs 錯過的 advanced 威脅，像是零天的惡意程式碼、無檔案惡意程式碼，以及客廳的策略。 

### <a name="unified-security-management-across-iotot"></a>跨 IoT/OT 的統一安全性管理

整合至 Azure Sentinel，以取得整個組織的鳥觀點。 透過整合到您現有的工作流程（包括 Splunk、IBM QRadar 和 ServiceNow 等協力廠商工具），來實行整合的 IoT/OT 安全性治理。 

## <a name="agent-based-solution-for-device-builders"></a>裝置建立器的代理程式解決方案 

安全性是 IoT 實施者近乎普遍的考慮。 IoT 裝置具有端點監視、安全性狀態管理和威脅偵測的獨特需求，這些都具有高度特定的效能需求。 

適用于 IoT 的 Azure Defender 安全性代理程式可讓您直接在新的 IoT 裝置和 Azure IoT 專案中建立安全性。 微代理程式有彈性的部署選項，包括部署為二進位封裝或修改原始程式碼的能力。 而微型代理程式適用于標準 IoT 作業系統，例如 Linux 和 Azure RTO。  

適用于 IoT 的 Azure Defender 微型代理程式可讓您看到安全性狀態管理、威脅偵測，以及整合至 Microsoft 其他安全性工具以進行整合安全性管理的端點。 

### <a name="security-posture-management"></a>安全性狀態管理

主動監視 IoT 裝置的安全性狀態。 適用于 IoT 的 Azure Defender 會根據 CIS 基準來提供安全性狀態建議，以及裝置特定的建議。 取得作業系統安全性的可見度，包括作業系統設定、防火牆設定和許可權。 

### <a name="endpoint-iotot-threat-detection"></a>端點 IoT/OT 威脅偵測

偵測殭屍網路、暴力密碼破解嘗試、加密採礦人員和可疑網路活動等威脅。 建立自訂警示，以獨特組織中最重要的威脅為目標。 

### <a name="flexible-distribution-and-deployment-models"></a>彈性的散發和部署模型 

適用于 IoT 的 Azure Defender 微型代理套裝程式含原始程式碼，因此您可以將微代理程式併入至固件或自訂它，使其只包含您需要的內容。 它也可作為二進位封裝，或直接整合至其他 Azure IoT 解決方案。 

## <a name="see-also"></a>另請參閱

[適用于 IoT 的 Azure Defender 架構](architecture.md)