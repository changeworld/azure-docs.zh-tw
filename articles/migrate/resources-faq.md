---
title: Azure 移轉常見問題解答
description: 獲取有關 Azure 遷移服務的常見問題的解答。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530312"
---
# <a name="azure-migrate-common-questions"></a>Azure 遷移:常見問題

本文回答了有關 Azure 遷移的常見問題。 如果在閱讀本文後有疑問,可以在[Azure 遷移論壇](https://aka.ms/AzureMigrateForum)中發佈這些問題。 您還可以檢視以下文章:

- 有關[Azure 移轉裝置的問題](common-questions-appliance.md)
- 有關[發現、評估和依賴項可視化](common-questions-discovery-assessment.md)的問題

## <a name="what-is-azure-migrate"></a>什麼是 Azure Migrate？

Azure 遷移提供了一個中心中心,用於跟蹤本地應用和工作負荷以及私有和公共雲 VM 到 Azure 的發現、評估和遷移。 中心提供用於評估和遷移以及第三方 ISV 服務的 Azure 遷移工具。 [深入了解](migrate-services-overview.md)。

## <a name="what-can-i-do-with-azure-migrate"></a>使用 Azure 遷移可以執行什麼操作?

使用 Azure 遷移可發現、評估和將本地基礎結構、應用程式和數據遷移到 Azure。 Azure 移植支援本地 VMware VM、超虛擬機器、實體伺服器、其他虛擬化 VM、資料庫、Web 應用和虛擬桌面的評估和遷移。 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure 遷移和 Azure 網站恢復之間的區別是什麼?

[Azure 遷移](migrate-services-overview.md)提供了一個集中式中心,用於評估和遷移到 Azure。 

[Azure 網站恢復](../site-recovery/site-recovery-overview.md)是災難恢復解決方案。 

Azure 遷移:伺服器遷移工具使用某些後端網站恢復功能來提升和轉移某些本地計算機。

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure 遷移:伺服器評估和 MAP 工具包之間的區別是什麼?

伺服器評估提供評估,以幫助遷移就緒,並評估遷移到 Azure 的工作負載。 [Microsoft 評估和規劃 (MAP) 工具組](https://www.microsoft.com/download/details.aspx?id=7826)可説明完成其他任務,包括較新版本的 Windows 用戶端和伺服器作業系統的遷移規劃以及軟體使用情況追蹤。 對於這些方案,請繼續使用 MAP 工具組。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>伺服器評估和站點恢復部署規劃器之間的區別是什麼?

伺服器評估是一個遷移規劃工具。 站點恢復部署規劃器是災難恢復規劃工具。

從您要執行的操作選擇工具:

- **計劃本地遷移到 Azure:** 如果計劃將本地伺服器遷移到 Azure,請使用伺服器評估進行遷移規劃。 伺服器評估評估本地工作負載,並提供説明您遷移的指導和工具。 遷移計劃實施後,可以使用 Azure 遷移:伺服器遷移等工具將電腦遷移到 Azure。
- **將災難恢復計畫到 Azure:** 如果計劃通過網站恢復從本地設置為 Azure,請使用網站恢復部署規劃器。 部署規劃器針對本地環境提供針對網站恢復的深入、特定於網站的評估,以便進行災難恢復。 它提供與災難恢復相關的建議,如複製和故障轉移。

## <a name="how-does-server-migration-work-with-site-recovery"></a>伺服器遷移如何處理網站恢復?

- 如果使用 Azure 遷移:伺服器遷移來執行本地 VMware VM 的無*代理*遷移,則遷移是 Azure 遷移的本機遷移,並且不使用網站恢復。
- 如果使用 Azure 遷移:伺服器遷移來執行*基於代理*的 VMware VM 遷移,或者遷移 Hyper-VM 或物理伺服器,則 Azure 遷移:伺服器遷移使用 Azure 網站恢復複製引擎。

## <a name="which-geographies-are-supported"></a>支援哪些地理位置?

查看[公共](migrate-support-matrix.md#supported-geographies-public-cloud)和政府[雲](migrate-support-matrix.md#supported-geographies-azure-government)的支援地理位置。

## <a name="how-do-i-get-started"></a>如何開始使用？

確定所需的工具,然後將該工具添加到 Azure 遷移專案。 

要新增 ISV 工具或移動:

1. 開始獲得許可證,或註冊免費試用版,根據工具策略。 這些工具會根據 ISV 或工具授權模型進行授權。
2. 每個工具中都會有連線至 Azure Migrate 的選項。 按照工具說明和文檔將工具與 Azure 移轉連接。

可以從 Azure 遷移專案中、跨 Azure 和其他工具跟蹤遷移過程。

## <a name="how-do-i-delete-a-project"></a>如何刪除專案?

瞭解如何[刪除項目](how-to-delete-project.md)。 

## <a name="next-steps"></a>後續步驟

閱讀[Azure 移轉概述](migrate-services-overview.md)。
