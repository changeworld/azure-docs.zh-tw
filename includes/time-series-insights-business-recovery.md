---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013890"
---
## <a name="business-disaster-recovery"></a>業務災害復原

本節介紹 Azure 時間序列見解的功能，即使發生災難（稱為*業務災害復原*），也能保持應用和服務的運行。

### <a name="high-availability"></a>高可用性

作為 Azure 服務，時序見解通過使用 Azure 區域級別的冗余提供某些*高可用性*功能。 例如，Azure 通過 Azure*的跨區域可用性*功能支援災害復原功能。

通過 Azure 提供的其他高可用性功能（以及任何時間序列見解實例都可用）包括：

- **容錯移轉**：Azure 提供[異地複製和負載平衡](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)。
- **資料恢復**和**存儲恢復**：Azure 提供了[幾個選項來保存和恢復資料](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)。
- **Azure 網站恢復**：Azure 通過[Azure 網站恢復](https://docs.microsoft.com/azure/site-recovery/)提供網站恢復功能。
- **Azure 備份** [：Azure 備份](https://docs.microsoft.com/azure/backup/backup-architecture)支援 Azure VM 的本地備份和雲內備份。

確保啟用相關的 Azure 功能，以便為設備和使用者提供全域的跨區域高可用性。

> [!NOTE]
> 如果 Azure 配置為啟用跨區域可用性，則 Azure 時間序列見解中不需要其他跨區域可用性配置。

### <a name="iot-and-event-hubs"></a>IoT 和事件中心

某些 Azure IoT 服務還包括內置的業務災害復原功能：

- [Azure IoT 中心高可用性災害復原](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)，包括區域內冗余
- [Azure 事件中心策略](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure 存儲冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

將時間序列見解與其他服務集成提供了額外的災害復原機會。 例如，發送到事件中心的遙測資料可能會保存到備份的 Azure Blob 存儲資料庫。

### <a name="time-series-insights"></a>時間序列深入解析

有幾種方法可以保持時間序列見解資料、應用和服務的運行，即使它們已中斷也是如此。 

但是，出於以下目的，您可以確定還需要 Azure 時間系列環境的完整備份副本：

- 作為故障*轉移實例*，專門用於時間序列見解，用於將資料和流量重定向到
- 保留資料和審核資訊

通常，複製時間序列見解環境的最佳方法是在備份 Azure 區域中創建第二個時間序列見解環境。 事件也會從主事件源發送到此輔助環境。 請確保使用第二個專用消費者組。 按照該源的業務災害復原指南進行操作，如前面所述。

要創建重複的環境，請進行：

1. 在第二個區域中建立環境。 有關詳細資訊，請閱讀[Azure 門戶中創建新的時序見解環境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)。
1. 建立您事件來源的第二個專用取用者群組。
1. 將該事件來源連線到新環境。 請確保指定第二個專用消費者組。
1. 查看時間序列見解[IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)中心和[事件中心](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)文檔。

如果發生事件：

1. 如果您的主要區域在災害事件期間受到影響，請將作業重新路由傳送到備份時間序列深入解析環境。
1. 使用第二個區域備份和恢復所有時間序列見解遙測和查詢資料。

> [!IMPORTANT]
> 如果發生容錯移轉：
> 
> * 也可能發生延遲。
> * 由於操作重新路由，消息處理可能會出現暫態峰值。
> 
> 有關詳細資訊，請閱讀["時間序列見解"中緩解延遲](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)。

