---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: b8941bb4fa77cb0008cb7271681e972bc21d6588
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659538"
---
您現在可以使用[私人端點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)，從虛擬網路內部的伺服器將資料安全地備份到復原服務保存庫。 私人端點會針對您的保存庫使用 VNET 位址空間中的 IP。 您在虛擬網路內的資源與保存庫之間的網路流量，會透過您的虛擬網路和 Microsoft 骨幹網路上的私人連結來傳輸。 這可避免資料在公用網際網路暴露。 私人端點可用於備份和還原在您的 Azure VM 中執行的 SQL 和 SAP Hana 資料庫， 也可以用於使用 MARS 代理程式的內部部署伺服器。

Azure VM 備份不需要網際網路連線，因此不需要私人端點來允許網路隔離。

請在[這裡](https://docs.microsoft.com/azure/backup/private-endpoints)詳讀 Azure 備份的私人端點詳細資料。
