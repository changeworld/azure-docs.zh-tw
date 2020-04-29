---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81539315"
---
您現在可以使用[私人端點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)，從虛擬網路內部的伺服器將資料安全地備份到復原服務保存庫。 私人端點會針對您的保存庫使用 VNET 位址空間中的 IP。 您在虛擬網路內的資源與保存庫之間的網路流量，會透過您的虛擬網路和 Microsoft 骨幹網路上的私用連結來傳輸。 這可免除公用網際網路的暴露。 私人端點可用於備份和還原您的 SQL 和 SAP Hana 在 Azure Vm 中執行的資料庫。 它也可以用於使用 MARS 代理程式的內部部署伺服器。

Azure VM 備份不需要網際網路連線，因此不需要私用端點來允許網路隔離。

>[!NOTE]
> 這項功能目前僅提供有限的可用性，並可在美國中西部、美國中南部、美國西部2及美國東部（其他 Azure 區域的可用性）中取得。 如果您有興趣使用 Azure 備份的私用端點，請填寫[這份問卷](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)並寄送電子郵件給我們。 azbackupnetsec@microsoft.com 使用這項功能的功能，可能會受到 Azure 備份服務的核准。
