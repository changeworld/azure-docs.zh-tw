---
title: 適用於 IoT 的 Azure Defender
description: 了解適用於 IoT 的 Azure Defender
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2f41aac085fef62e0356a93a07823f21d7ba3667
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448341"
---
# <a name="introduction-to-azure-defender-for-iot"></a>適用於 IoT 的 Azure Defender 簡介

整合安全性管理，並在混合式雲端工作負載和您的 Azure IoT 解決方案之間啟用端對端威脅偵測和分析。

適用於 IoT 的 Azure Defender 提供：

- **資產探索和網路對應**，包括裝置製造商、裝置類型，以及裝置在網路上通訊的方式等詳細資料
- **弱點管理**，包括 CVE、開啟連接埠和未經授權的網際網路連線的相關資訊
- **持續的威脅監視**，附有即時警示可指出任何異常或未經授權的活動，例如針對性攻擊或惡意程式碼

[專用文件](https://docs.microsoft.com/azure/asc-for-iot/overview)會提供完整的詳細資料。

## <a name="availability"></a>可用性
|層面|詳細資料|
|----|:----|
|版本狀態：|正式上市 (GA)|
|定價：|需要 [Azure Defender](security-center-pricing.md)|
|必要的角色和權限：|機器 NSG 的寫入權限|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>適用於 IoT 的 Azure Defender 可保護哪些裝置？
藉由結合資訊安全中心、Azure Defender 和 CyberX 無代理程式技術的功能，您可以保護：

- 透過 IoT 中樞連線的**綠地裝置**。 這可讓組織透過單一整合解決方案同時保護網際網路原生的新式裝置與傳統 ICS/SCADA 裝置，以加速推動其 IoT 方案。
    - 將新裝置上線，並在您的工作負載 (分葉裝置、Microsoft Edge 裝置、IoT 中樞) 間套用安全性原則，以確保能符合安全性標準以及改善安全性態勢。

- 營運技術 (OT) 環境中使用的**非受控棕地裝置**，例如製造、建築物管理系統 (BMS)、生命科學、能源與水務公用事業、石油與天然氣，以及物流。 
    - 為了保護非受控裝置，適用於 IoT 的 Azure Defender 會使用內部部署感應器進行被動、非侵入式的網路流量分析 (NTA)，對 OT 環境的效能不會有任何影響。 
    - 此外也包含特殊化 IoT/OT 通訊協定的深入理解 (結合了具專利的 IoT/OT 感知行為分析和機器學習)，讓您無須設定任何規則或簽章，而可在幾分鐘或數小時 (不需數天或數週) 內完成一般部署。 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>適用於 IoT 的 Azure Defender 與 Azure Sentinel 的整合
為了啟用整合 IT/OT 安全性監視和控管，適用於 IoT 的 Azure Defender 原本即與 [Azure Sentinel](../sentinel/overview.md) 整合。

SecOps 小組可以：

- 透過 Sentinel 隨附的 OT 特定 SOAR 劇本，快速偵測及因應 IoT/OT 威脅
- 受益於持續更新的 IoT/OT 特定威脅情報，此情報由 Microsoft 的內部 IoT/OT 威脅情報小組 Section 52 提供
- 整合適用於 IoT 的 Azure Defender 與現有的 SOC 工作流程和第三方安全性工具，例如 Splunk、IBM QRadar 和 ServiceNow


## <a name="next-steps"></a>後續步驟

在本文中，您已了解 Azure 資訊安全中心中適用於 IoT 的 Azure Defender。 如需詳細資訊，請參閱

- [適用於 IoT 的 Azure 資訊安全中心的簡介](../asc-for-iot/overview.md)
