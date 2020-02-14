---
title: 路由的車輛耗用量模型 |Microsoft Azure 對應
description: 在本文中，您將瞭解 Microsoft Azure 對應中路由的車輛耗用量模型。
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190240"
---
# <a name="consumption-model"></a>耗用模型

路由服務會提供一組參數，以取得車輛特定耗用量模型的詳細描述。
視 **vehicleEngineType** 的值而定，支援兩種主要的耗用模型：_燃燒_和_電子_。 在相同的要求中指定屬於不同模型的參數是不正確的。 此外，耗用量模型參數不能與下列**travelMode**值搭配使用：_自行車_和_地下_。

## <a name="parameter-constraints-for-consumption-model"></a>耗用模型的參數限制式

在這兩種耗用量模型中，指定參數時，會有一些相依性。 也就是說，明確指定某些參數可能需要指定一些其他參數。 以下是要注意的相依性：

* 所有參數都需要由使用者指定 **constantSpeedConsumption**。 如果未指定**constantSpeedConsumption** ，則指定任何其他耗用量模型參數是錯誤的。 **除了 vehicleweight**參數是此需求的例外狀況。
* **accelerationEfficiency**和**decelerationEfficiency**必須一律指定為一組（也就是，兩者都是）。
* 如果指定 **accelerationEfficiency** 和 **decelerationEfficiency**，其值的產品必不能大於 1 (以防永恆運動)。
* **uphillEfficiency**和**downhillEfficiency**必須一律指定為成對（也就是，兩者都是）。
* 如果指定 **uphillEfficiency** 和 **downhillEfficiency**，其值的產品必不能大於 1 (以防永恆運動)。
* 如果使用者指定 \*__Efficiency__ 參數，則也必須指定 **vehicleWeight**。 若 **vehicleEngineType** 為 _combustion_，也必須指定 **fuelEnergyDensityInMJoulesPerLiter**。
* **maxChargeInkWh**和**currentChargeInkWh**必須一律指定為一組（也就是，兩者都是）。

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

一組特定的耗用量參數可能會遭到拒絕，即使該集合可以滿足所有明確的需求也一樣。 當特定參數的值或數個參數的值組合被視為導致不合理的耗用量值巨量時，就會發生此情況。 如果發生這種情況，最有可能表示輸入錯誤，因為原先已適當處理以容納耗用參數的所有合理值。 如果已拒絕一組特定的耗用參數，則伴隨而來的錯誤訊息會包含原因的文字說明。
參數的詳細說明含有兩種模型合理值的範例。
