---
title: 備份中心總覽
description: 本文提供 Azure 備份中心的總覽。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: e7577f302b9a137f5f3c257dc35d0fdf339f69a4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994574"
---
# <a name="overview-of-backup-center"></a>備份中心總覽

備份中心在 Azure 中提供 **單一的整合管理體驗** ，可讓企業大規模管理、監視、操作及分析備份。 因此，它與 Azure 的原生管理體驗一致。

備份中心的一些主要優點包括：

* **管理備份的單一管理平臺** –「備份中心」是設計成可在大型且分散的 Azure 環境中運作。 您可以使用備份中心，有效率地管理跨越多個工作負載類型、保存庫、訂用帳戶、區域和 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) 租使用者的備份。
* 以**資料來源為中心的管理**–「備份中心」提供以您要備份的資料來源為中心的視圖和篩選 (例如，vm 和資料庫) 。 這可讓資源擁有者或備份管理員監視和操作專案的備份，而不需要將焦點放在要備份專案的保存庫上。 這項設計的主要功能是能夠依據資料來源的特定屬性（例如 datasource 訂用帳戶、datasource 資源群組和 datasource 標記）篩選 views。 例如，如果您的組織遵循將不同的標籤指派給屬於不同部門的 Vm，您可以使用 [備份中心] 根據要備份之基礎 Vm 的標記來篩選備份資訊，而不需要將焦點放在保存庫的標籤上。
* **連線體驗** –「備份中心」提供對現有 Azure 服務的原生整合，以大規模進行管理。 例如，「備份中心」會使用 [Azure 原則](https://docs.microsoft.com/azure/governance/policy/overview) 體驗來協助您管理您的備份。 它也會利用 [Azure 活頁簿](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview) 和 [Azure 監視器記錄](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) 來協助您查看有關備份的詳細報告。 因此，您不需要學習任何新的原則，就能使用備份中心所提供的各種功能。

## <a name="supported-scenarios"></a>支援的案例

* Azure VM 備份和適用於 PostgreSQL 的 Azure 資料庫 Server 備份目前支援「備份中心」。
* 如需支援和不支援案例的詳細清單，請參閱 [支援矩陣](backup-center-support-matrix.md) 。

## <a name="get-started"></a>開始使用

若要開始使用 [備份中心]，請在 Azure 入口網站中搜尋 [ **備份中心** ]，然後流覽至 [ **備份中心] ([預覽]) ** 儀表板。

![備份中心搜尋](./media/backup-center-overview/backup-center-search.png)

您看到的第一個畫面是 **總覽**。 它包含兩個磚– **工作** 和 **備份實例**。

![備份中心磚](./media/backup-center-overview/backup-center-overview-widgets.png)

在 [ **工作** ] 磚中，您可以取得過去24小時內，所有備份資產所觸發的所有備份和還原相關工作的摘要查看。 您可以查看已完成、失敗且正在進行中的作業數目的相關資訊。 選取此磚中的任何數位，可讓您針對特定資料來源類型、作業類型和狀態，查看更多作業的相關資訊。

在 [ **備份實例** ] 圖格中，您可以取得整個備份資產的所有備份實例的摘要查看。 例如，您可以查看處於虛刪除狀態的備份實例數目，相較于仍然設定保護的實例數目。 選取此磚中的任何數位，可讓您針對特定資料來源類型和保護狀態，查看有關備份實例的詳細資訊。

請遵循下列步驟來瞭解備份中心提供的不同功能，以及您可以如何使用這些功能來有效率地管理您的備份資產。

## <a name="next-steps"></a>下一步

* [監視和操作備份](backup-center-monitor-operate.md)
* [管理您的備份資產](backup-center-govern-environment.md)
* [取得您的備份見解](backup-center-obtain-insights.md)
* [使用備份中心執行動作](backup-center-actions.md)
