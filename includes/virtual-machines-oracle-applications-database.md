---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361527"
---
### <a name="database-tier"></a>資料庫層

資料庫層包含應用程式的資料庫實例。 該資料庫可以是 Oracle 資料庫、Oracle RAC 或 Oracle Exadata 資料庫系統。 

如果選擇使用 Oracle DB，則資料庫實例可以通過 Azure 應用商店上可用的 Oracle 資料庫映射部署在 Azure 上。 或者，您可以使用 Azure 和 OCI 之間的互連在 OCI 上的 PaaS 模型中部署 Oracle DB。

對於 Oracle RAC，您可以在 IaaS 模型中的 Azure 雲簡單部署 Oracle RAC，或在 PaaS 模型中的 OCI 中部署 Oracle RAC。 建議您使用雙節點 RAC 系統。 

最後，對於 Exadata 系統，請使用 OCI 互連並在 OCI 中部署 Exadata 系統。 上面的體系結構圖顯示了在 OCI 中跨兩個子網部署的 Exadata 系統。

對於生產方案，跨兩個可用性區域（如果在 Azure 中部署）或兩個可用性域（在 OCI 中）部署資料庫的多個實例。 使用 Oracle 活動資料防護同步主資料庫和備用資料庫。

資料庫層僅接收來自中介層的請求。 建議您設置一個網路安全性群組（如果在 OCI 中部署資料庫時的安全清單），以僅允許出於管理原因允許來自堡壘伺服器的埠 1521 和埠 22 的請求。

對於在 OCI 中部署的資料庫，必須使用連接到 FastConnect 電路的動態路由閘道 （DRG） 設置單獨的虛擬雲網路。