---
title: 實體安全性 - Azure 專用 HSM | Microsoft Docs
description: Azure 專用 HSM 裝置在資料中心內的實體安全性相關資訊
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ede1af4625d06af4e280eda86d09ae1db3dfdfd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "70881021"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Azure 專用 HSM 實體安全性

Azure 專用 HSM 可幫助您符合金鑰儲存的進階安全性需求。 它在整個生命週期中遵循嚴格的安全措施進行管理，以滿足客戶需求。

## <a name="security-through-procurement"></a>藉由採購實現安全性

Microsoft 遵循安全的採購流程。 我們管理監管鏈，並確保訂購和出貨的特定裝置是送達我們資料中心的裝置。 這些裝置採用竄改事件塑膠後蓋。 它們會儲存在安全的存放區，直到委託在資料中心的資料資源庫中。  包含 HSM 裝置的機架視為高度業務衝擊 (HBI)。 這些裝置一律處於鎖定狀態，並且受到視訊監視。

## <a name="security-through-deployment"></a>透過部署實現安全性

HSM 與相關網路元件一起安裝在機架中。 安裝後，必須先加以設定，然後才能將其作為 Azure 專用 HSM 服務的一部分。 此設定活動是由經過背景檢查的 Microsoft 員工執行。 「Just In Time」(JIT) 管理限制只能由正確的員工進行存取，並且僅能於必須時進行存取。 使用的程序和系統也會確保記錄與 HSM 裝置相關的所有活動。

## <a name="security-in-operations"></a>作業中的安全性

HSM 是硬體設備 (實際 HSM 是設備中的 PCI 卡)，因此，可能會出現元件層級問題。 潛在問題包括但不限於風扇和電源故障。 此類事件將需要維修或中斷/修復活動，以便更換任何可交換的元件。

### <a name="component-replacement"></a>元件更換

在裝置佈建並由客戶管理時，熱插拔電源是唯一可以更換的元件。 此元件位於安全性界限之外，而且不會造成竄改事件。 票證系統用於授權 Microsoft 工程師存取 HBI 機架背面。 處理票證時，會發出臨時的實體金鑰。 此金鑰可讓工程師存取裝置，並可讓他們交換受影響的元件。 當裝置未分配給客戶時，將完成任何其他存取 (也就是可能導致篡改事件的)，進而將安全性和可用性的風險降至最低。  

### <a name="device-replacement"></a>裝置更換

在整個裝置發生故障的情況下，會遵循與元件故障期間所使用的類似程序。 如果客戶無法將裝置歸零，或裝置處於未知狀態，則將會移除資料承載裝置，並放置在機架內的銷毀箱中。 放置在箱中的裝置將以受控管且安全的方式銷毀。 HBI 機架中沒有任何資料承載裝置會離開 Microsoft 資料中心。

### <a name="other-rack-access-activities"></a>其他機架存取活動

如果 Microsoft 工程師必須存取 HSM 裝置使用的機架 (例如，網路裝置維護)，將使用標準安全程序來存取 HBI 安全機架。 所有存取將受到視訊監視。 HSM 裝置已經過 [FIPS 140-2 Level 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) 驗證，因此，任何對 HSM 裝置的未授權存取都會傳送訊號給客戶，而且資料將歸零。

## <a name="logical-level-security-considerations"></a>邏輯層級安全性考量

HSM 會佈建至客戶建立的虛擬網路。 這是客戶的私人 IUP 位址空間。  此設定提供寶貴的邏輯網路層級隔離，並確保只能由客戶存取。 這表示所有邏輯層級安全控制是客戶的責任。

## <a name="next-steps"></a>後續步驟

建議在裝置佈建、應用程式設計或部署之前，先充分了解服務的所有重要概念，例如高可用性、安全性以及支援能力。

* [高可用性](high-availability.md)
* [網路功能](networking.md)
* [支援能力](supportability.md)
* [監視](monitoring.md)
* [部署架構](deployment-architecture.md)