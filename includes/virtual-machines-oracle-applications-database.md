---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: b9e275cbdaf264df3d0a40f032693fd93553e520
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84317472"
---
### <a name="database-tier"></a>資料庫層

資料庫層包含應用程式的資料庫實例。 資料庫可以是 Oracle DB、Oracle RAC 或 Oracle Exadata 資料庫系統。 

如果選擇使用 Oracle DB，則可以透過 Azure Marketplace 上提供的 Oracle DB 映射，將資料庫實例部署在 Azure 上。 或者，您可以使用 Azure 和 OCI 之間的互連，將 Oracle DB 部署到 OCI 上的 PaaS 模型。

若是 Oracle RAC，您可以在 PaaS 模型中使用 OCI。 建議使用兩個節點的 RAC 系統。 雖然您可以在 IaaS 模型的 Azure CloudSimple 上部署 Oracle RAC，但 Oracle 並不支援這種設定。 請參閱[適用于授權雲端環境的 Oracle 程式](http://www.oracle.com/us/corporate/pricing/authorized-cloud-environments-3493562.pdf)。

最後，對於 Exadata 系統，請使用 OCI 互連，並在 OCI 中部署 Exadata 系統。 上述架構圖顯示跨兩個子網部署于 OCI 中的 Exadata 系統。

針對生產案例，請跨兩個可用性區域（如果在 Azure 中部署）或兩個可用性網域（在 OCI 中），部署資料庫的多個實例。 使用 Oracle Active Data Guard 來同步處理主要和待命資料庫。

資料庫層只會從仲介層接收要求。 建議您設定網路安全性群組（如果在 OCI 中部署資料庫，則為安全性清單），只允許來自仲介層的埠1521上的要求，以及來自防禦伺服器的埠22，以因應系統管理的原因。

針對部署在 OCI 中的資料庫，您必須使用連線至 FastConnect 線路的動態路由閘道（DRG.4）來設定個別的虛擬雲端網路。