---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: b9e275cbdaf264df3d0a40f032693fd93553e520
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84317472"
---
### <a name="database-tier"></a>資料庫層

資料庫層包含應用程式的資料庫實例。 資料庫可以是 Oracle DB、Oracle RAC 或 Oracle Exadata 資料庫系統。 

如果選擇使用 Oracle DB，可以透過 Azure Marketplace 上提供的 Oracle DB 映射，在 Azure 上部署資料庫實例。 或者，您可以使用 Azure 與 OCI 之間的互連，將 Oracle DB 部署在 OCI 的 PaaS 模型中。

若為 Oracle RAC，您可以在 PaaS 模型中使用 OCI。 建議您使用雙節點的 RAC 系統。 雖然您可以在 IaaS 模型中的 Azure CloudSimple 上部署 Oracle RAC，但是 Oracle 並不支援此設定。 請參閱 [適用于授權雲端環境的 Oracle 方案](http://www.oracle.com/us/corporate/pricing/authorized-cloud-environments-3493562.pdf)。

最後，針對 Exadata 系統，請使用 OCI 互連，並在 OCI 中部署 Exadata 系統。 上述架構圖表顯示跨兩個子網部署在 OCI 中的 Exadata 系統。

針對生產案例，請在兩個可用性區域間部署資料庫的多個實例 (如果在 Azure) 中部署，或在 OCI) 中 (兩個可用性網域。 使用 Oracle Active Data Guard 來同步處理主要和待命資料庫。

資料庫層只會接收來自仲介層的要求。 如果在 OCI 中部署資料庫，建議您將網路安全性群組設定 (安全性清單) ，只允許來自仲介層的埠1521要求，以及來自防禦伺服器的埠22（基於系統管理原因）。

針對部署在 OCI 中的資料庫，您必須使用動態路由閘道來設定個別的虛擬雲端網路， () DRG.4 連線到您的 FastConnect 電路。