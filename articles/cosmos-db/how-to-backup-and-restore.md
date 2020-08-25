---
title: 如何從備份還原 Azure Cosmos DB 資料
description: 本文將說明如何從備份還原 Azure Cosmos DB 資料、如何連絡 Azure 支援人員來還原資料，以及還原資料後應採取的步驟。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9956ca0ca9c0957557e7ee74883a75c074ff22f8
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797967"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中從備份還原資料

如果您不小心刪除資料庫或容器，您可以提出 [支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 或 [呼叫 Azure 支援](https://azure.microsoft.com/support/options/) ，以從自動線上備份還原資料。 Azure 支援僅適用于所選方案，例如 **標準**、 **開發人員**和方案（高於這些方案）。 Azure 支援不適用於**基本**方案。 若要深入了解不同的支援方案，請參閱 [Azure 支援方案](https://azure.microsoft.com/support/plans/)頁面。

若要還原特定的備份快照集，Azure Cosmos DB 需要該資料在該快照的備份週期持續時間內為可用狀態。

## <a name="request-a-restore"></a>要求還原

要求還原之前，您應該有下列詳細資料：

* 準備好您的訂用帳戶識別碼。

* 視資料遭到意外刪除或修改的情況而定，您可能須準備其他資訊。 建議您預先準備好資料，以降低資料來回傳送時，可能對某些具時效性案例造成的損害。

* 如果整個 Azure Cosmos DB 帳戶遭到刪除，您需要提供已刪除帳戶的名稱。 如果您用已刪除帳戶的名稱建立另一個帳戶，請將此情況告知支援小組，如此才能判斷要選擇的正確帳戶。 建議您針對每個已刪除的帳戶提出不同的支援票證，因為這樣可將還原的狀態的混淆降至最低。

* 如果一個或多個資料庫遭到刪除，您應提供 Azure Cosmos 帳戶及 Azure Cosmos 資料庫名稱，並指出是否有使用相同名稱的新資料庫存在。

* 如果一個或多個容器遭到刪除，您應該提供 Azure Cosmos 帳戶名稱、資料庫名稱和容器名稱， 並指出是否有使用相同名稱的容器存在。

* 如果您不小心刪除或損毀資料，請在8小時內聯絡 [Azure 支援](https://azure.microsoft.com/support/options/) ，讓 Azure Cosmos DB 的團隊可以協助您從備份還原資料。 **在您建立支援要求以還原資料之前，請務必將您帳戶 [的備份保留期增加](online-backup-and-restore.md) 到至少七天。最好是在此事件的8小時內增加保留期。** 如此一來，Azure Cosmos DB 支援小組將擁有足夠的時間來還原您的帳戶。

除了 Azure Cosmos 帳戶名稱、資料庫名稱、容器名稱之外，您還必須指定可將資料還原至的時間點。 愈精確愈好，如此可協助我們判斷該時間的最佳可用備份。 **也請務必以 UTC 指定時間。**

下列螢幕擷取畫面說明如何使用 Azure 入口網站，為要還原資料的容器 (集合/圖表/資料表) 建立支援要求。 提供其他詳細資料，例如資料類型、還原目的和資料遭到刪除的時間，以協助我們優先處理此要求。

:::image type="content" source="./media/how-to-backup-and-restore/backup-support-request-portal.png" alt-text="使用 Azure 入口網站建立備份支援要求":::

## <a name="post-restore-actions"></a>還原後的動作

還原資料之後，您會收到有關新帳戶名稱 (格式通常會是 `<original-name>-restored1`) 及帳戶還原時間點的通知。 已還原帳戶中會有相同的已佈建輸送量和編製索引原則，且位於與原始帳戶相同的區域。 身為訂用帳戶管理員或共同管理員的使用者，可以看到還原的帳戶。

還原資料之後，您應檢查及驗證已還原帳戶中的資料，並確定其包含您預期的版本。 如果一切都沒問題，應使用 [Azure Cosmos DB 變更摘要](change-feed.md)或 [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) 將資料遷移回原始帳戶。

建議您在完成資料遷移後立即刪除容器或資料庫。 如果沒有刪除已還原的資料庫或容器，則會產生要求單位、儲存體和輸出的成本。

## <a name="next-steps"></a>後續步驟

接下來，您可以使用下列文章來了解如何將資料遷移回原始帳戶：

* 若要提出還原要求，請[從 Azure 入口網站提出票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以連絡 Azure 支援
* [使用 Cosmos DB 變更摘要](change-feed.md)將資料移至 Azure Cosmos DB。

* [使用 Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) 將資料移至 Azure Cosmos DB。
