---
title: 檢查 Azure IoT 中樞服務和資源健康狀態 |Microsoft Docs
description: 使用 Azure 服務健康狀態和 Azure 資源健康狀態來監視您的 IoT 中樞
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548470"
---
# <a name="check-iot-hub-service-and-resource-health"></a>檢查 IoT 中樞服務和資源健康狀態

Azure IoT 中樞與 [Azure 服務健康狀態服務](../service-health/overview.md) 整合，讓您能夠監視 IoT 中樞服務和個別 IoT 中樞的服務層級健康情況。 您也可以設定警示，以在 IoT 中樞服務或 IoT 中樞的狀態變更時收到通知。 Azure 服務健康狀態服務是三個較小服務的組合： Azure 資源健康狀態、Azure 服務健康狀態和 Azure 狀態頁面。 本文中的各節將更詳細地說明每個服務及其與 IoT 中樞之間的關聯性。

Azure 服務健康狀態服務可協助您監視服務層級的事件，例如中斷和升級，可能會影響 IoT 中樞服務和個別 IoT 中樞的可用性。 IoT 中樞也會與 Azure 監視器整合，以提供 IoT 中樞平臺計量和 IoT 中樞資源記錄，讓您用來監視特定 IoT 中樞上發生的操作錯誤和狀況。 若要深入瞭解，請參閱 [監視 IoT 中樞](monitor-iot-hub.md)。

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>使用 Azure 資源健康狀態檢查 IoT 中樞的健康情況

Azure 資源健康狀態是 Azure 服務健康狀態服務的一部分，可追蹤個別資源的健康情況。 您可以直接從入口網站檢查 IoT 中樞的健康情況狀態。

若要使用入口網站查看 IoT 中樞的狀態和狀態歷程記錄，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至 Azure 入口網站中的 IoT 中樞。

1. 在左窗格的 [ **支援 + 疑難排解** ] 底下，選取 [ **資源健康狀態** ]。

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="IoT 中樞的資源健康情況頁面":::

若要深入瞭解 Azure 資源健康狀態以及如何解讀健康情況資料，請參閱 Azure 服務健康狀態檔中的 [資源健康狀態總覽](../service-health/resource-health-overview.md) 。

您也可以選取 [ **新增資源健康狀態警示** ]，以設定當 IoT 中樞的健康狀態變更時要觸發的警示。 若要深入瞭解，請參閱 Azure 服務健康狀態檔中的 [設定服務健康情況事件的警示](../service-health/alerts-activity-log-service-notifications-portal.md) 和相關主題。

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>使用 Azure 服務健康狀態檢查訂用帳戶中的 IoT 中樞健康情況

使用 Azure 服務健康狀態，您就可以檢查訂用帳戶中所有 IoT 中樞的健康情況狀態。

若要檢查您 IoT 中樞的健康情況，請依照下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 流覽至 **服務健康** 情況  >  **資源健康狀態** 。

3. 從下拉式方塊中選取您的訂用帳戶，然後選取 [ **IoT 中樞** ] 作為資源類型。

若要深入瞭解 Azure 服務健康狀態以及如何解讀健康情況資料，請參閱 Azure 服務健康狀態檔中的 [服務健康狀態總覽](../service-health/service-health-overview.md) 。

若要瞭解如何使用 Azure 服務健康狀態設定警示，請參閱 Azure 服務健康狀態檔中的設定 [服務健康狀態事件的警示](../service-health/alerts-activity-log-service-notifications-portal.md) 和相關主題。

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>依 Azure 狀態頁面上的區域檢查 IoT 中樞服務的健康情況

若要以全球區域查看 IoT 中樞和其他服務的狀態，您可以使用 [ [Azure 狀態] 頁面](https://status.azure.com/status)。 如需有關 Azure 狀態頁面的詳細資訊，請參閱 Azure 服務健康狀態檔中的 [azure 狀態總覽](../service-health/azure-status-overview.md) 。

## <a name="next-steps"></a>下一步

* 如需 Azure 服務健康狀態、Azure 資源健康狀態和 Azure 狀態頁面的詳細資訊，請參閱 [Azure 服務健康狀態 Service](../service-health/overview.md) 。
* 如需監視 Azure IoT 中樞的說明，請參閱 [監視 Azure IoT 中樞](monitor-iot-hub.md) 。
