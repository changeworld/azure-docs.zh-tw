---
title: 用於佈線的車輛消耗模型 |微軟 Azure 地圖
description: 在本文中，您將瞭解用於在 Microsoft Azure 地圖中路由的車輛消耗模型。
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190240"
---
# <a name="consumption-model"></a>耗用模型

路由服務提供一組參數，用於詳細描述車輛特定的消耗模型。
視 **vehicleEngineType** 的值而定，支援兩種主要的耗用模型：_燃燒_和_電子_。 在同一請求中指定屬於不同模型的參數是不對的。 此外，消費模型參數不能與以下**行駛模式**值一起使用：_自行車_和_行人_。

## <a name="parameter-constraints-for-consumption-model"></a>耗用模型的參數限制式

在這兩個消耗模型中，指定參數時都有一些依賴關係。 這意味著，顯式指定某些參數可能需要指定一些其他參數。 以下是需要注意的這些依賴項：

* 所有參數都需要由使用者指定 **constantSpeedConsumption**。 如果未指定**常量速度消耗**，則指定任何其他消耗模型參數是錯誤的。 **車輛重量**參數是此要求的例外。
* **加速度效率和****減速效率**必須始終指定為對（即兩者或無）。
* 如果指定 **accelerationEfficiency** 和 **decelerationEfficiency**，其值的產品必不能大於 1 (以防永恆運動)。
* **上坡效率和****下坡效率**必須始終指定為對（即兩者或無）。
* 如果指定 **uphillEfficiency** 和 **downhillEfficiency**，其值的產品必不能大於 1 (以防永恆運動)。
* 如果使用者指定 \*__Efficiency__ 參數，則也必須指定 **vehicleWeight**。 若 **vehicleEngineType** 為 _combustion_，也必須指定 **fuelEnergyDensityInMJoulesPerLiter**。
* **maxChargeInkWh**和**電流 ChargeInkWh**必須始終指定為對（即兩者或無）。

> [!NOTE]
> 如果僅指定 **constantSpeedConsumption**，沒有為耗用計算考慮其他耗用層面，如坡度和載具加速。

## <a name="combustion-consumption-model"></a>燃燒耗用模型

**vehicleEngineType** 設為_燃燒_時，會使用燃燒耗用模型。
屬於此模型的參數清單如下。 請參閱 [參數] 區段，取得詳細說明。

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>電子耗用模型

**vehicleEngineType** 設為_電子_時，會使用電子耗用模型。
屬於此模型的參數清單如下。 請參閱 [參數] 區段，取得詳細說明。

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>合理的耗用參數值

可以拒絕一組特定的消耗參數，即使該參數集可能滿足所有顯式要求。 當特定參數的值或多個參數的值的組合被視為導致不合理的消耗值量級時，就會發生這種情況。 如果發生這種情況，最有可能表示輸入錯誤，因為原先已適當處理以容納耗用參數的所有合理值。 如果已拒絕一組特定的耗用參數，則伴隨而來的錯誤訊息會包含原因的文字說明。
參數的詳細說明含有兩種模型合理值的範例。
