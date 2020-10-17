---
title: 針對 Azure IoT 中樞500xxx 內部錯誤進行疑難排解
description: 瞭解如何修正500xxx 內部錯誤
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f83ec4d09389d91c2dd427c9840885c0b829eafb
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150315"
---
# <a name="500xxx-internal-errors"></a>500xxx 內部錯誤

本文說明 **500Xxx 內部錯誤**的原因和解決方案。

## <a name="symptoms"></a>徵兆

您對 IoT 中樞的要求失敗，並出現錯誤，其開頭為500及/或某種類型的「伺服器錯誤」。 有一些可能的原因：

* **500001 ServerError**： IoT 中樞遇到伺服器端問題。

* **500008 GenericTimeout**： IoT 中樞在超時之前無法完成連線要求。

* **ServiceUnavailable (沒有錯誤碼) **： IoT 中樞發生內部錯誤。

* **InternalServerError (沒有錯誤碼) **： IoT 中樞發生內部錯誤。

## <a name="cause"></a>原因

500xxx 錯誤回應可能有許多原因。 在所有情況下，問題最可能是暫時性的。 在 IoT 中樞小組努力維護 [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/) 的同時，小部分的 IoT 中樞節點可能會偶爾遇到暫時性錯誤。 當您的裝置嘗試連線到有問題的節點時，您會收到這個錯誤。

## <a name="solution"></a>解決方案

若要減少500xxx 錯誤，請從裝置發出重試。 若要[自動管理重試](./iot-hub-reliability-features-in-sdks.md#connection-and-retry)，請確定您使用最新版的 [Azure IoT SDK](./iot-hub-devguide-sdks.md)。 如需暫時性錯誤處理和重試的最佳做法，請參閱[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)。  如果問題持續發生，請檢查 [資源健康狀態](./iot-hub-monitor-resource-health.md#use-azure-resource-health) 和 [Azure 狀態](https://status.azure.com/) ，以查看 IoT 中樞是否有已知的問題。 您也可以使用 [手動容錯移轉功能](./tutorial-manual-failover.md)。 如果沒有已知問題，且問題持續發生，請 [洽詢支援](https://azure.microsoft.com/support/options/) 人員以進一步調查。