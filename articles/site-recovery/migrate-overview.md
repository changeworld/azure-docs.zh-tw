---
title: 比較 Azure Migrate 和 Site Recovery 以遷移至 Azure
description: 摘要說明使用 Azure Migrate 進行遷移的優點，而不是 Site Recovery。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: raynew
ms.openlocfilehash: 358efaa1493aa08fb76c9bb83e0e4289950e0969
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844315"
---
# <a name="migrating-to-azure"></a>遷移至 Azure

針對遷移，建議您使用 Azure Migrate 服務，將 Vm 和伺服器遷移至 Azure，而不是 Azure Site Recovery 服務。 [深入瞭解](../migrate/migrate-services-overview.md) Azure Migrate。


## <a name="why-use-azure-migrate"></a>為何使用 Azure Migrate？

使用 Azure Migrate 進行遷移可提供許多優點：
 
 
- Azure Migrate 提供集中式的中樞，可供探索、評量及遷移至 Azure。
- 使用 Azure Migrate 可透過 Azure Migrate 工具、其他 Azure 服務及協力廠商工具，提供互通性和未來的擴充性。
- Azure Migrate：伺服器遷移工具的目的是為了將伺服器遷移至 Azure 而建立。 它已針對遷移進行優化。 您不需要瞭解與遷移不直接相關的概念和案例。 
- 從 VM 的複寫開始，開始進行180天的遷移不會產生任何工具使用費用。 這讓您有時間完成遷移。 您只需針對複寫中使用的儲存體和網路資源支付費用，以及在測試遷移期間使用的計算費用。
- Azure Migrate 支援 Site Recovery 支援的所有遷移案例。 此外，對於 VMware Vm，Azure Migrate 提供無代理程式遷移選項。
- 我們會優先針對 Azure Migrate：伺服器遷移工具的新遷移功能。 這些功能不是 Site Recovery 的目標。

## <a name="when-to-use-site-recovery"></a>何時使用 Site Recovery？

應使用 Site Recovery：

- 適用于內部部署機器至 Azure 的災難復原。
- Azure Vm 在 Azure 區域之間的災難復原。

雖然我們建議使用 Azure Migrate 將內部部署伺服器遷移至 Azure，但如果您已開始使用 Site Recovery 的遷移旅程，您可以繼續使用它來完成您的遷移。  

## <a name="next-steps"></a>後續步驟

> [檢閱關於 Azure Migrate 的常見問題](../migrate/resources-faq.md)。
