---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137019"
---
您現在可以使用[私人端點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)，從虛擬網路內部的伺服器將資料安全地備份到復原服務保存庫。 私人端點會針對您的保存庫使用 VNET 位址空間中的 IP。 您在虛擬網路內的資源與保存庫之間的網路流量，會透過您的虛擬網路和 Microsoft 骨幹網路上的私人連結來傳輸。 這可避免資料在公用網際網路暴露。 私人端點可用於備份和還原在您的 Azure VM 中執行的 SQL 和 SAP Hana 資料庫， 也可以用於使用 MARS 代理程式的內部部署伺服器。

Azure VM 備份不需要網際網路連線，因此不需要私人端點來允許網路隔離。

>[!NOTE]
> 此功能目前為早期使用狀態。 如果您有興趣使用私人端點進行 Azure 備份，請填寫[此問卷](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)，並傳送電子郵件至 azbackupnetsec@microsoft.com 與我們連線。 能否使用這項功能，會依 Azure 備份服務的核准為依據。
