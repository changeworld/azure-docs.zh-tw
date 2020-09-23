---
title: 適用於 IoT 的 Azure Defender
description: 瞭解適用于 IoT 的 Azure Defender
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 67bb01092cb6171ae56b8ab8b308e0c2ea3bece0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934606"
---
# <a name="introduction-to-azure-defender-for-iot"></a>適用于 IoT 的 Azure Defender 簡介

統一安全性管理，並在混合式雲端工作負載和您的 Azure IoT 解決方案之間啟用端對端威脅偵測與分析。

適用于 IoT 的 Azure Defender 提供：

- **資產探索和網路對應**，包括裝置製造商等詳細資料、裝置類型，以及裝置如何在網路上通訊
- **弱點管理**，包括 cve、開放埠和未經授權的網際網路連線的相關資訊
- **持續威脅監視**，並提供即時警示，指出任何異常或未經授權的活動，例如目標攻擊或惡意程式碼

您可以在 [專用檔](https://docs.microsoft.com/azure/asc-for-iot/overview)中取得完整的詳細資料。

## <a name="availability"></a>可用性
|層面|詳細資料|
|----|:----|
|釋放狀態：|正式上市 (GA)|
|定價：|需要 [Azure Defender](security-center-pricing.md)|
|必要的角色和許可權：|電腦 Nsg 上的寫入權限|
|雲端：|![Yes](./media/icons/yes-icon.png) 商業雲端<br>![Yes](./media/icons/yes-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>適用于 IoT 的 Azure Defender 可以保護哪些裝置？
藉由結合安全性中心、Azure Defender 和 CyberX 無代理程式技術的功能，您可以保護：

- 透過 IoT 中樞連線的**Greenfield 裝置**。 這可讓組織透過單一整合解決方案保護新式、網際網路原生裝置和傳統 ICS/SCADA 裝置，以加速其 IoT 計畫。
    - 將新的裝置上線，並將安全性原則套用至您的工作負載 (分葉裝置、Microsoft Edge 裝置、IoT 中樞) ，以確保符合安全性標準並改善安全性狀態。

- 在營運技術中使用的**非受控棕色地帶裝置** () 環境，例如製造、建立管理系統 (BMS) 、生命科學、能源和水公用事業、石油 & 天然氣及物流。 
    - 為了保護未受管理的裝置，適用于 IoT 的 Azure Defender 會使用內部部署感應器進行被動、非侵入式的網路流量分析 (NTA) ，對面向環境的影響不會有任何影響。 
    - 此外，它也包含對特製化 IoT/OT 通訊協定的深入瞭解，並結合專利、IoT/可感知的行為分析和機器學習服務，以免除設定任何規則或簽章的需求，而不是幾天或幾周的時間來進行一般部署。 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>適用于 IoT 的 Azure Defender 與 Azure Sentinel 整合
為了啟用整合 IT/管理安全性監視和治理，適用于 IoT 的 Azure Defender 原生與 [Azure Sentinel](../sentinel/overview.md)整合。

SecOps 團隊可以：

- 透過 Sentinel 隨附的特定 SOAR 腳本，快速偵測和回應 IoT/OT 威脅
- 受益于連續更新52的 IoT/OT 特定威脅情報，由 Microsoft 的內部 IoT/OT 威脅情報團隊所提供
- 整合適用于 IoT 的 Azure Defender 與現有的 SOC 工作流程，以及協力廠商的安全性工具，例如 Splunk、IBM QRadar 和 ServiceNow


## <a name="next-steps"></a>下一步

在本文中，您已瞭解 Azure 資訊安全中心中適用于 IoT 的 Azure Defender。 如需詳細資訊，請參閱

- [適用於 IoT 的 Azure 資訊安全中心的簡介](../asc-for-iot/overview.md)
