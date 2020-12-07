---
title: 高可用性和災害復原
titleSuffix: Azure Digital Twins
description: 說明 Azure 和 Azure 數位 Twins 的功能，可協助您建立具有嚴重損壞修復功能的高可用性 Azure IoT 解決方案。
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ac75a5b0b59a06855b7ee88d971c269ca915e429
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763158"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure 數位 Twins 高可用性和嚴重損壞修復

復原 IoT 解決方案的重要考慮範圍是商務持續性和嚴重損壞修復。 針對 **高可用性設計 (HA) 和嚴重** 損壞 **修復 (DR)** 可協助您為解決方案定義和達成適當的執行時間目標。

本文討論 Azure 數位 Twins 服務特別提供的 HA 和 DR 功能。

Azure 數位 Twins 支援下列功能選項：
* *內部區域 HA* –內建的冗余，可提供服務的執行時間
* *跨區域 DR* –在非預期的資料中心失敗時容錯移轉至異地配對的 Azure 區域

您也可以參閱 [*最佳做法*](#best-practices) 一節，以取得針對 HA/DR 設計的一般 Azure 指引。

## <a name="intra-region-ha"></a>內部區域 HA
 
Azure 數位 Twins 會藉由在服務內執行冗余來提供內部區域 HA。 這會反映在 [服務 SLA](https://azure.microsoft.com/support/legal/sla/digital-twins) 中的執行時間。 **Azure 數位 Twins 解決方案的開發人員不需要額外的工作，即可利用這些 HA 功能。** 雖然 Azure 數位 Twins 提供相當高的執行時間保證，但仍可能預期發生暫時性失敗，如同任何分散式運算平臺一樣。 您應該在與雲端應用程式互動的元件內建適當的重試原則，以處理暫時性失敗。

## <a name="cross-region-dr"></a>跨區域 DR

在某些罕見的情況下，資料中心會因為電源故障或區域中的其他事件而導致延伸中斷。 這類事件很罕見，在這種情況下，上述的內部區域 HA 功能可能不會有説明。 Azure 數位 Twins 透過 Microsoft 起始的容錯移轉來解決此情況。

Microsoft **起始的容錯移轉** 是由 microsoft 在罕見的情況下，將所有 Azure 數位 Twins 實例從受影響的區域容錯移轉到對應的地理配對區域。 此程式是預設選項， (沒有任何方法可讓使用者退出宣告) ，也不需要使用者介入。 Microsoft 有權決定施行此選項的時機。 這項機制在使用者的實例容錯移轉之前，不需要使用者同意。

>[!NOTE]
> 某些 Azure 服務也提供一個稱為「 **客戶起始的容錯移轉**」的額外選項，可讓客戶只針對其實例起始容錯移轉，例如執行 DR 演練。 Azure 數位 Twins 目前 **不支援** 此機制。 

## <a name="best-practices"></a>最佳作法

如需 HA/DR 的最佳作法，請參閱本主題的下列 Azure 指引： 
* 《 [*Azure 商務持續性技術指南*](/azure/architecture/framework/resiliency/overview) 」一文說明可協助您思考商務持續性和嚴重損壞修復的一般架構。 
* [*Azure 應用程式的嚴重損壞修復和高可用性*](/azure/architecture/framework/resiliency/backup-and-recovery)白皮書提供 azure 應用程式策略的架構指引，以達成高可用性 (HA) 和嚴重損壞修復 (DR) 。

## <a name="next-steps"></a>後續步驟 

深入瞭解如何開始使用 Azure 數位 Twins 解決方案：
 
* [*什麼是 Azure Digital Twins？*](overview.md)
* [*快速入門：探索範例案例*](quickstart-adt-explorer.md)