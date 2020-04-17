---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539315"
---
現在,您可以使用[專用終結點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)安全地將數據從虛擬網路中的伺服器備份到恢復服務保管庫。 專用終結點使用來自 VNET 位址空間的 IP 來用於保管庫。 虛擬網路中的資源和保管庫之間的網路流量透過虛擬網路和Microsoft骨幹網路上的專用鏈路傳輸。 這消除了公共互聯網的曝光。 專用終結點可用於備份和還原在 Azure VM 中運行的 SQL 和 SAP HANA 資料庫。 它還可用於使用 MARS 代理的本地伺服器。

Azure VM 備份不需要互聯網連接,因此不需要專用終結點來允許網路隔離。

>[!NOTE]
> 此功能目前可用性有限,在美國中西部、美國中南部、美國西部 2 和美國東部(後續其他 Azure 區域的可用性)可用。 如果您有興趣使用專用終結點進行 Azure 備份,請填寫[此調查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)並發送電子郵件azbackupnetsec@microsoft.com至我們這裡。 使用此功能的功能須經 Azure 備份服務批准。
