---
title: 使用 Azure Site Recovery 將伺服器和 Vm 遷移至 Azure
description: 說明如何使用 Azure Site Recovery 服務，將內部部署和 Azure IaaS Vm 遷移至 Azure。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: aaf01dcd63c21e4741456f4f7fccaf22b8fbfffc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281305"
---
# <a name="about-migration"></a>關於移轉

使用 Azure Migrate 服務，將 Vm 和伺服器遷移至 Azure，而不是 Azure Site Recovery 服務。 [深入瞭解](../migrate/migrate-services-overview.md)Azure Migrate。


## <a name="why-use-azure-migrate"></a>為何使用 Azure Migrate？

使用 Azure Migrate 進行遷移可提供許多優點：
 
 
- Azure Migrate 提供用於探索、評估和遷移至 Azure 的集中式中樞。
- 使用 Azure Migrate 透過 Azure Migrate 工具、其他 Azure 服務及協力廠商工具，提供互通性和未來的擴充性。
- Azure Migrate：伺服器遷移工具是專為伺服器遷移至 Azure 而設計的用途。 它已針對遷移優化。 您不需要瞭解與遷移不直接相關的概念和案例。 
- 從 VM 開始複寫時，沒有任何工具使用費用可供遷移180天。 這讓您有時間完成遷移。 您只需支付複寫所使用的儲存體和網路資源，以及在測試遷移期間所耗用的計算費用。
- 針對 VMware Vm，除了代理程式型遷移之外，Azure Migrate 還提供無代理程式遷移。
- 我們會為 Azure Migrate 的新遷移功能排定優先順序：僅限伺服器遷移工具。

## <a name="when-to-use-site-recovery"></a>何時使用 Site Recovery？

應該使用 Site Recovery：

- 針對內部部署機器到 Azure 的嚴重損壞修復。
- 針對 Azure Vm 在 Azure 區域之間的嚴重損壞修復。

雖然我們建議使用 Azure Migrate 將內部部署伺服器遷移至 Azure，但如果您已經使用 Site Recovery 開始遷移旅程，您可以繼續使用它來完成您的遷移。  

## <a name="next-steps"></a>後續步驟

> [查看](../migrate/resources-faq.md)有關 Azure Migrate 的常見問題。
