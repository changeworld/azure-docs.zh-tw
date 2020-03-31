---
title: 排除 Azure IoT 中心 500xxx 內部錯誤
description: 瞭解如何修復 500xxx 內部錯誤
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271040"
---
# <a name="500xxx-internal-errors"></a>500xxx 內部錯誤

本文介紹了**500xxx內部錯誤**的原因和解決方案。

## <a name="symptoms"></a>徵狀

您對 IoT 中心的請求失敗，出現以 500 和/或某種"伺服器錯誤"開頭的錯誤。 一些可能性包括：

* **500001 伺服器錯誤**： IoT 中心遇到伺服器端問題。

* **500008 通用超時**：IoT 中心在超時之前無法完成連接請求。

* **服務不可用（無錯誤代碼）：IoT**中心遇到內部錯誤。

* **內部伺服器錯誤（無錯誤代碼）：IoT**中心遇到內部錯誤。

## <a name="cause"></a>原因

500xxx 錯誤回應的原因可能有很多。 在所有情況下，問題很可能是暫時性的。 在 IoT 中樞小組努力維護 [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/) 的同時，小部分的 IoT 中樞節點可能會偶爾遇到暫時性錯誤。 當您的裝置嘗試連線到有問題的節點時，您會收到這個錯誤。

## <a name="solution"></a>解決方法

要緩解 500xxx 錯誤，請從設備發出重試。 若要[自動管理重試](./iot-hub-reliability-features-in-sdks.md#connection-and-retry)，請確定您使用最新版的 [Azure IoT SDK](./iot-hub-devguide-sdks.md)。 如需暫時性錯誤處理和重試的最佳做法，請參閱[暫時性錯誤處理](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults)。  如果問題仍然存在，請檢查[資源運行狀況](./iot-hub-monitor-resource-health.md#use-azure-resource-health)和[Azure 狀態](https://status.azure.com/)，以查看 IoT 中心是否有已知問題。 您還可以使用[手動容錯移轉功能](./tutorial-manual-failover.md)。 如果沒有已知問題且問題仍然存在，[請聯繫支援](https://azure.microsoft.com/support/options/)進一步調查。
