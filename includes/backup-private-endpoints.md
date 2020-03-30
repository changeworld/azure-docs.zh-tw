---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137019"
---
現在，您可以使用[專用終結點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)安全地將資料從虛擬網路中的伺服器備份到恢復服務保存庫。 專用終結點使用來自 VNET 位址空間的 IP 來用於保存庫。 虛擬網路中的資源和保存庫之間的網路流量通過虛擬網路和 Microsoft 骨幹網路上的專用鏈路傳輸。 這消除了公共互聯網的曝光。 專用終結點可用於備份和還原在 Azure VM 中運行的 SQL 和 SAP HANA 資料庫。 它還可用於使用 MARS 代理的本機伺服器。

Azure VM 備份不需要互聯網連接，因此不需要專用終結點來允許網路隔離。

>[!NOTE]
> 此功能目前正在早期使用。 如果您有興趣使用專用終結點進行 Azure 備份，請填寫[此調查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)併發送電子郵件至我們這裡。 azbackupnetsec@microsoft.com 使用此功能的功能須經 Azure 備份服務批准。
