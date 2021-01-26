---
title: 高可用性和災害復原
titleSuffix: Azure Digital Twins
description: 說明 Azure 和 Azure 數位 Twins 的功能，可協助您建立具有嚴重損壞修復功能的高可用性 Azure IoT 解決方案。
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3336a086fbe8f4291f752836a610cd80b773ec2d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790811"
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

## <a name="monitor-service-health"></a>監視服務健康情況

當 Azure 數位 Twins 實例容錯移轉和復原時，您可以使用 [Azure 服務健康狀態](../service-health/service-health-overview.md) 工具來監視處理常式。 服務健康狀態會追蹤不同區域和訂用帳戶的 Azure 服務健康狀態，並共用有關中斷和停機時間的服務影響通訊。

在容錯移轉事件期間，服務健康狀態可提供您的服務關閉和備份時的指示。

若要查看服務健康狀態事件 .。。
1. 在 Azure 入口網站中流覽至 [ [服務健康狀態](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) ] (您可以使用此連結，或使用入口網站搜尋列) 來搜尋它。
1. 使用左側功能表切換至 [健康情況歷程 *記錄* ] 頁面。
1. 尋找以 **Azure 數位 Twins** 開頭的 *問題名稱*，然後選取它。

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="顯示 [健康情況歷程記錄] 頁面 Azure 入口網站的螢幕擷取畫面。過去幾天內有幾個問題的清單，並且反白顯示稱為「Azure 數位 Twins-西歐」的問題。" lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. 如需中斷的一般資訊，請參閱 [ *摘要* ] 索引標籤。

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="[健康情況歷程記錄] 頁面上會醒目提示 [摘要] 索引標籤。此索引標籤會顯示一般資訊，例如受影響的資源、其區域及其訂用帳戶。" lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. 如需有關一段時間內問題的詳細資訊和更新，請參閱 [ *問題更新* ] 索引標籤。

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="在 [健康情況歷程記錄] 頁面上，會反白顯示 [問題更新] 索引標籤此索引標籤會顯示數個專案，顯示一天前的目前狀態。" lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


請注意，這項工具所顯示的資訊不是一個 Azure 數位實例所特有。 使用服務健康狀態來瞭解在特定區域或訂用帳戶中使用 Azure 數位 Twins 服務的情況之後，您可以使用 [資源健康狀態工具](troubleshoot-resource-health.md) 進一步監視特定的實例，並查看它們是否受到影響。

## <a name="best-practices"></a>最佳作法

如需 HA/DR 的最佳作法，請參閱本主題的下列 Azure 指引： 
* 《 [*Azure 商務持續性技術指南*](/azure/architecture/framework/resiliency/overview) 」一文說明可協助您思考商務持續性和嚴重損壞修復的一般架構。 
* [*Azure 應用程式的嚴重損壞修復和高可用性*](/azure/architecture/framework/resiliency/backup-and-recovery)白皮書提供 azure 應用程式策略的架構指引，以達成高可用性 (HA) 和嚴重損壞修復 (DR) 。

## <a name="next-steps"></a>後續步驟 

深入瞭解如何開始使用 Azure 數位 Twins 解決方案：
 
* [*什麼是 Azure Digital Twins？*](overview.md)
* [*快速入門：探索範例案例*](quickstart-adt-explorer.md)