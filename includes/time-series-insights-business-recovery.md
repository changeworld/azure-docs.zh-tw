---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 07/09/2020
ms.openlocfilehash: f25c335c568c112c05f81df51d69e83aeff423e2
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553471"
---
## <a name="business-disaster-recovery"></a>業務災害復原

本節說明可讓應用程式和服務繼續執行的 Azure 時間序列深入解析功能，即使發生嚴重損壞 (又稱為 *企業* 嚴重損壞修復) 也是一樣。

### <a name="high-availability"></a>高可用性

作為 Azure 服務，Azure 時間序列深入解析使用 Azure 區域層級的冗余來提供某些 *高可用性* 功能。 例如，Azure 會透過 Azure 的 *跨區域可用性* 功能來支援嚴重損壞修復功能。

透過 Azure (提供的額外高可用性功能，也適用于任何 Azure 時間序列深入解析的實例) 包括：

- **容錯移轉**： Azure 提供 [異地複寫和負載平衡](/azure/architecture/resiliency/recovery-loss-azure-region)。
- **資料還原** 和 **儲存體** 復原： Azure 提供 [數個選項來保留及復原資料](/azure/architecture/resiliency/recovery-data-corruption)。
- **Azure Site Recovery**： Azure 會透過 [Azure Site Recovery](../articles/site-recovery/index.yml)提供 Site Recovery 功能。
- **Azure 備份**： [Azure 備份](../articles/backup/backup-architecture.md) 支援 Azure vm 的內部部署和雲端備份。

請確定您已啟用相關的 Azure 功能，以為您的裝置和使用者提供全球、跨區域的高可用性。

> [!NOTE]
> 如果 Azure 設定為啟用跨區域可用性，Azure 時間序列深入解析不需要額外的跨區域可用性設定。

### <a name="iot-and-event-hubs"></a>IoT 和事件中樞

某些 Azure IoT 服務也包含內建的商務嚴重損壞修復功能：

- [Azure IoT 中樞高可用性嚴重損壞修復](../articles/iot-hub/iot-hub-ha-dr.md)，其中包括內部區域內的冗余
- [Azure 事件中樞原則](../articles/event-hubs/event-hubs-geo-dr.md)
- [Azure 儲存體複寫](../articles/storage/common/storage-redundancy.md)

將 Azure 時間序列深入解析與其他服務整合可提供額外的災難復原機會。 例如，傳送至事件中樞的遙測可能會保存到備份 Azure Blob 儲存體資料庫。

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

有幾種方式可讓您的 Azure 時間序列深入解析資料、應用程式和服務繼續執行，即使它們已中斷也一樣。 

不過，您可能會判斷您的 Azure 時間序列環境的完整備份複本也是必要的，原因如下：

- 作為 *容錯移轉實例* ，專門用於 Azure 時間序列深入解析將資料和流量重新導向至
- 保留資料和審核資訊

一般而言，複製 Azure 時間序列深入解析環境的最佳方式，是在備份 Azure 區域中建立第二個 Azure 時間序列深入解析環境。 事件也會從您的主要事件來源傳送到此次要環境。 請確定您使用的是第二個專用取用者群組。 遵循該來源的商務嚴重損壞修復指導方針（如先前所述）。

若要建立重複的環境：

1. 在第二個區域中建立環境。 如需詳細資訊，請參閱 [Azure 入口網站中的建立新的 Azure 時間序列深入解析環境](../articles/time-series-insights/time-series-insights-get-started.md)。
1. 建立您事件來源的第二個專用取用者群組。
1. 將該事件來源連線到新環境。 請確定您指定的是第二個專用取用者群組。
1. 請參閱 Azure 時間序列深入解析 [IoT 中樞](../articles/time-series-insights/how-to-ingest-data-iot-hub.md) 和 [事件中樞](../articles/time-series-insights/concepts-access-policies.md) 檔。

如果發生事件：

1. 如果您的主要區域在災難事件期間受到影響，請將作業重新路由至備份 Azure 時間序列深入解析環境。
1. 使用您的第二個區域來備份和復原所有 Azure 時間序列深入解析遙測和查詢資料。

> [!IMPORTANT]
> 如果發生容錯移轉：
> 
> * 可能也會發生延遲。
> * 可能會在訊息處理過程中發生短暫的尖峰，因為作業會重新路由。
> 
> 如需詳細資訊，請參閱 [Azure 時間序列深入解析中的緩和延遲](../articles/time-series-insights/time-series-insights-environment-mitigate-latency.md)。