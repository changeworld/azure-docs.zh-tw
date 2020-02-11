---
title: 使用 Azure IoT Central 建置醫療保健解決方案 | Microsoft Docs
description: 了解如何使用 Azure IoT Central 應用程式範本來建置醫療保健解決方案。
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 2aee5c56b267f9243b69a48dfd0f5f64196c5f23
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77021480"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>使用 Azure IoT Central 建置醫療保健解決方案 



了解如何透過 Azure IoT Central 使用應用程式範本來建置醫療保健解決方案。

## <a name="what-is-continuous-patient-monitoring-template"></a>什麼是持續性患者監視範本？

在醫療保健 IoT 空間中，「持續性患者監視」是降低再住院風險、更有效地管理慢性疾病，以及改善患者療效的關鍵促成因子之一。 持續性患者監視可以分成兩個主要類別：

1. **住院患者監視**：使用醫療穿戴式裝置和醫院中的其他裝置，護理小組得以監視患者的生命徵象及醫療狀況，而無須派護士一日查看患者數次。 護理小組可透過通知即時了解患者需要密切注意，並有效安排他們的時間。
1. **遠端患者監視**：藉由使用醫療穿戴式裝置和患者回報的結果 (PRO) 來監視院外患者，可以降低再住院的風險。 院方可收集慢性疾病患者和康復患者的資料，以確保患者遵守護理計劃，並在患者病情變得更嚴重之前，讓護理小組獲得病情惡化的警示。

此應用程式範本可用來建置兩種持續性患者監視的解決方案。 其優點包括：

* 順暢地將不同醫療穿戴式裝置連線至 IoT Central 執行個體。
* 監視及管理裝置，以確保它們保持良好狀態。
* 建立裝置資料的自訂規則，以觸發適當的警示。
* 將患者的健康情況資料匯出至 Azure API for FHIR 這個符合規範的資料存放區。
* 將彙總的深入解析匯出到現有或全新的商務應用程式。

>[!div class="mx-imgBorder"] 
>![CPM-dashboard](media/in-patient-dashboard.png)

## <a name="next-steps"></a>後續步驟

若要開始建置持續性患者監視解決方案：

* [部署應用程式範本](tutorial-continuous-patient-monitoring.md)
* [查看範例架構](concept-continuous-patient-monitoring-architecture.md)