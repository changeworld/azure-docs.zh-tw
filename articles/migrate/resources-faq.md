---
title: Azure 遷移常見問題解答
description: 獲取有關 Azure 遷移服務的常見問題的解答。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: c85c5c6e11beb0178139dad152f56f420b2ac26f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926720"
---
# <a name="azure-migrate-common-questions"></a>Azure 遷移：常見問題

本文回答了有關 Azure 遷移的常見問題。 如果在閱讀本文後有疑問，可以在[Azure 遷移論壇](https://aka.ms/AzureMigrateForum)中發佈這些問題。 您還可以查看以下文章：

- 有關[Azure 遷移設備的問題](common-questions-appliance.md)
- 有關[發現、評估和依賴項視覺化](common-questions-discovery-assessment.md)的問題

## <a name="what-is-azure-migrate"></a>什麼是 Azure Migrate？

Azure 遷移提供了一個中心中心，用於跟蹤本地應用和工作負荷以及私有和公共雲 VM 到 Azure 的發現、評估和遷移。 中心提供用於評估和遷移以及協力廠商 ISV 服務的 Azure 遷移工具。 [深入了解](migrate-services-overview.md)。

## <a name="what-can-i-do-with-azure-migrate"></a>使用 Azure 遷移可以執行什麼操作？

使用 Azure 遷移可發現、評估和將本地基礎結構、應用程式和資料移轉到 Azure。 Azure 遷移支援本地 VMware VM、超虛擬機器、物理伺服器、其他虛擬化 VM、資料庫、Web 應用和虛擬桌面的評估和遷移。 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure 遷移和 Azure 網站恢復之間的區別是什麼？

[Azure 遷移](migrate-services-overview.md)提供了一個集中式中心，用於評估和遷移到 Azure。 

[Azure 網站恢復](../site-recovery/site-recovery-overview.md)是災害復原解決方案。 

Azure 遷移：伺服器遷移工具使用某些後端網站恢復功能來提升和轉移某些本地電腦。

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure 遷移：伺服器評估和 MAP 工具組之間的區別是什麼？

伺服器評估提供評估，以説明遷移就緒，並評估遷移到 Azure 的工作負載。 [Microsoft 評估和規劃 （MAP） 工具組](https://www.microsoft.com/download/details.aspx?id=7826)可説明完成其他任務，包括較新版本的 Windows 用戶端和伺服器作業系統的遷移規劃以及軟體使用方式跟蹤。 對於這些方案，請繼續使用 MAP 工具組。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>伺服器評估和網站恢復部署規劃器之間的區別是什麼？

伺服器評估是一個遷移規劃工具。 網站恢復部署規劃器是災害復原規劃工具。

根據您要執行的操作選擇工具：

- **計畫本地遷移到 Azure：** 如果計畫將本機伺服器遷移到 Azure，請使用伺服器評估進行遷移規劃。 伺服器評估評估本地工作負載，並提供説明您遷移的指導和工具。 遷移計畫實施後，可以使用 Azure 遷移：伺服器遷移等工具將電腦遷移到 Azure。
- **將災害復原計畫到 Azure：** 如果計畫通過網站恢復從本地設置為 Azure，請使用網站恢復部署規劃器。 部署規劃器針對本地環境提供針對網站恢復的深入、特定于網站的評估，以便進行災害復原。 它提供與災害復原相關的建議，如複製和容錯移轉。

## <a name="how-does-server-migration-work-with-site-recovery"></a>伺服器遷移如何處理網站恢復？

- 如果使用 Azure 遷移：伺服器遷移來執行本地 VMware VM 的無*代理*遷移，則遷移是 Azure 遷移的本機遷移，並且不使用網站恢復。
- 如果使用 Azure 遷移：伺服器遷移來執行*基於代理*的 VMware VM 遷移，或者遷移 Hyper-VM 或物理伺服器，則 Azure 遷移：伺服器遷移使用 Azure 網站恢復複製引擎。

## <a name="which-geographies-are-supported"></a>支援哪些地理位置？

- **VMware VM**：查看 VMware VM 的 Azure 遷移[支援地理位置](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)。
- **超 VM**：查看超級 VM 的 Azure 遷移[支援地理位置](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v)。

## <a name="how-do-i-get-started"></a>如何開始使用？

確定所需的工具，然後將該工具添加到 Azure 遷移專案。 

要添加 ISV 工具或移動：

1. 開始獲得許可證，或註冊免費試用版，根據工具策略。 這些工具會根據 ISV 或工具授權模型進行授權。
2. 每個工具中都會有連線至 Azure Migrate 的選項。 按照工具說明和文檔將工具與 Azure 遷移連接。

可以從 Azure 遷移專案中、跨 Azure 和其他工具跟蹤遷移過程。

## <a name="how-do-i-delete-a-project"></a>如何刪除專案？

瞭解如何[刪除專案](how-to-delete-project.md)。 

## <a name="next-steps"></a>後續步驟

閱讀[Azure 遷移概述](migrate-services-overview.md)。
