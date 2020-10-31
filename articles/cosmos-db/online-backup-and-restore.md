---
title: 在 Azure Cosmos DB 中進行線上備份及隨選資料還原
description: 本文說明自動備份、隨選資料還原的運作方式、如何設定備份間隔和保留、如何在 Azure Cosmos DB 中支援資料還原的連絡人。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 43625a80df76ff35b8bb1804df5f5fd1524326c5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097527"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中進行線上備份及隨選資料還原
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 會自動地定期備份您的資料。 自動備份的進行不會影響資料庫作業的效能或可用性。 所有備份會儲存在另外的儲存體服務中，而且系統會全域複寫這些備份，以便為區域性災害提供復原功能。 當您不小心刪除或更新 Azure Cosmos 帳戶、資料庫或容器，並在之後需要復原資料時，自動備份會很有幫助。

## <a name="automatic-and-online-backups"></a>自動的線上備份

有了 Azure Cosmos DB，不只您的資料，還有資料的備份都一併具有高度備援性，可針對區域性災害進行復原。 下列步驟顯示 Azure Cosmos DB 如何執行資料備份：

* Azure Cosmos DB 會每隔4小時自動取得資料庫的完整備份，而且在任何時間點，預設只會儲存最新的兩個備份。 如果預設間隔不足以滿足您的工作負載，您可以從 Azure 入口網站變更備份間隔和保留期限。 您可以在 Azure Cosmos 帳戶建立期間或之後變更備份設定。 如果容器或資料庫遭到刪除，Azure Cosmos DB 會保留指定容器或資料庫的現有快照集30天。

* Azure Cosmos DB 會將這些備份儲存在 Azure Blob 儲存體中，而實際的資料會位於 Azure Cosmos DB 的本機。

* 為了保證低延遲，您的備份快照集會儲存在 Azure Blob 儲存體中，與目前寫入區域 (或 **其中一個** 寫入區域相同區域，以防您有多重區域寫入設定) 。 為了從區域性災害中復原，系統會再透過異地備援儲存體 (GRS)，將 Azure Blob 儲存體中的每個備份資料快照集複寫到另一個區域。 至於會將備份複寫到哪個區域，則取決於來源區域以及與來源區域相關聯的區域配對。 若要深入了解，請參閱 [Azure 區域的異地備援配對清單](../best-practices-availability-paired-regions.md)一文。 您無法直接存取此備份。 透過支援要求進行要求時，Azure Cosmos DB 小組會還原您的備份。

   下圖顯示三個主要實體分割區都在美國西部的 Azure Cosmos 容器，會備份到美國西部的遠端 Azure Blob 儲存體帳戶，然後再複寫到美國東部：

  :::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="GRS Azure 儲存體中所有 Cosmos DB 實體的定期完整備份" border="false":::

* 備份的進行不會影響應用程式的效能或可用性。 Azure Cosmos DB 會在背景中執行資料備份，既不會取用任何另外佈建的輸送量 (RU)，也不會影響資料庫的效能和可用性。

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>修改備份間隔和保留期限

Azure Cosmos DB 會每隔4小時自動取得資料的完整備份，而且在任何時間點，都會儲存最新的兩個備份。 這是預設選項，且不需要任何額外成本即可提供。 您可以在建立 Azure Cosmos 帳戶期間，或在建立帳戶之後，變更預設的備份間隔和保留期限。 備份組態是在 Azure Cosmos 帳戶層級設定，而且您必須在每個帳戶上設定。 設定帳戶的備份選項之後，就會將其套用至該帳戶內的所有容器。 目前您只能從 Azure 入口網站變更備份選項。

如果您不小心刪除或損毀資料，在 **建立支援要求以還原資料之前，請務必將您帳戶的備份保留期增加到至少七天。最好是在此事件的8小時內增加保留期。** 如此一來，Azure Cosmos DB 小組就有足夠的時間來還原您的帳戶。

使用下列步驟來變更現有 Azure Cosmos 帳戶的預設備份選項：

1. 登入 [Azure 入口網站。](https://portal.azure.com/)
1. 流覽至您的 Azure Cosmos 帳戶，然後開啟 [ **備份 & 還原** ] 窗格。 視需要更新備份間隔和備份保留期限。

   * **備份間隔** -這是 Azure Cosmos DB 嘗試進行資料備份的間隔。 備份需要非零的時間，而在某些情況下，可能會因為下游相依性而失敗。 Azure Cosmos DB 嘗試在設定的間隔內進行備份，但是不保證備份會在該時間間隔內完成。 您可以用小時或分鐘來設定此值。 備份間隔不能小於1小時且超過24小時。 當您變更此間隔時，新的間隔會從上次進行備份的時間開始生效。

   * **備份保留** -它代表每個備份的保留期間。 您可以在數小時或數天內進行設定。 最小保留期間不能小於備份間隔的兩倍（以小時為單位） () ，且不能大於720小時。

   * **保留的資料副本** -根據預設，系統會免費提供兩份資料備份複本。 如果您需要兩個以上的複本，則會產生額外的費用。 請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)中的已使用儲存體一節，以了解額外複本的確切價格。

   :::image type="content" source="./media/online-backup-and-restore/configure-backup-interval-retention.png" alt-text="GRS Azure 儲存體中所有 Cosmos DB 實體的定期完整備份" border="true":::

如果您在帳戶建立期間設定備份選項，就可以設定 **備份原則** ，也就是 **定期** 或 **連續** 。 定期原則可讓您設定備份間隔和備份保留期。 持續的原則目前僅供註冊之用。 Azure Cosmos DB 團隊將會評估您的工作負載，並核准您的要求。

:::image type="content" source="./media/online-backup-and-restore/configure-periodic-continuous-backup-policy.png" alt-text="GRS Azure 儲存體中所有 Cosmos DB 實體的定期完整備份" border="true":::

## <a name="request-data-restore-from-a-backup"></a>從備份要求資料還原

如果您不小心刪除資料庫或容器，您可以提出 [支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 或 [呼叫 Azure 支援](https://azure.microsoft.com/support/options/) ，以從自動線上備份還原資料。 Azure 支援僅適用于所選方案，例如 **標準** 、 **開發人員** 和方案（高於這些方案）。 Azure 支援不適用於 **基本** 方案。 若要深入了解不同的支援方案，請參閱 [Azure 支援方案](https://azure.microsoft.com/support/plans/)頁面。

若要還原特定的備份快照集，Azure Cosmos DB 需要該資料在該快照的備份週期持續時間內為可用狀態。
要求還原之前，您應該有下列詳細資料：

* 準備好您的訂用帳戶識別碼。

* 視資料遭到意外刪除或修改的情況而定，您可能須準備其他資訊。 建議您預先準備好資料，以降低資料來回傳送時，可能對某些具時效性案例造成的損害。

* 如果整個 Azure Cosmos DB 帳戶遭到刪除，您需要提供已刪除帳戶的名稱。 如果您用已刪除帳戶的名稱建立另一個帳戶，請將此情況告知支援小組，如此才能判斷要選擇的正確帳戶。 建議您針對每個已刪除的帳戶提出不同的支援票證，因為這樣可將還原的狀態的混淆降至最低。

* 如果一個或多個資料庫遭到刪除，您應提供 Azure Cosmos 帳戶及 Azure Cosmos 資料庫名稱，並指出是否有使用相同名稱的新資料庫存在。

* 如果一個或多個容器遭到刪除，您應該提供 Azure Cosmos 帳戶名稱、資料庫名稱和容器名稱， 並指出是否有使用相同名稱的容器存在。

* 如果您不小心刪除或損毀資料，請在8小時內聯絡 [Azure 支援](https://azure.microsoft.com/support/options/) ，讓 Azure Cosmos DB 的團隊可以協助您從備份還原資料。 **在您建立支援要求以還原資料之前，請務必將您帳戶 [的備份保留期增加](#configure-backup-interval-retention) 到至少七天。最好是在此事件的8小時內增加保留期。** 如此一來，Azure Cosmos DB 支援小組將擁有足夠的時間來還原您的帳戶。

除了 Azure Cosmos 帳戶名稱、資料庫名稱、容器名稱之外，您還必須指定可將資料還原至的時間點。 愈精確愈好，如此可協助我們判斷該時間的最佳可用備份。 **也請務必以 UTC 指定時間。**

下列螢幕擷取畫面說明如何使用 Azure 入口網站，為要還原資料的容器 (集合/圖表/資料表) 建立支援要求。 提供其他詳細資料，例如資料類型、還原目的和資料遭到刪除的時間，以協助我們優先處理此要求。

:::image type="content" source="./media/online-backup-and-restore/backup-support-request-portal.png" alt-text="GRS Azure 儲存體中所有 Cosmos DB 實體的定期完整備份":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>從備份還原資料的考慮

在下列其中一個案例中，您可能會不小心刪除或修改您的資料：  

* 刪除整個 Azure Cosmos 帳戶。

* 刪除一或多個 Azure Cosmos 資料庫。

* 刪除一或多個 Azure Cosmos 容器。

* 刪除或修改 Azure Cosmos 專案 (例如，) 容器內的檔。 此特定案例通常稱為資料損毀。

* 共用的供應專案資料庫或共用供應專案資料庫內的容器已刪除或損毀。

Azure Cosmos DB 可以在遇到上述所有情況時還原資料。 還原時，會建立新的 Azure Cosmos 帳戶來保存還原的資料。 如果未指定新帳戶的名稱，則會具有格式 `<Azure_Cosmos_account_original_name>-restored1` 。 嘗試多個還原時，最後一個數位會遞增。 您無法將資料還原至預先建立的 Azure Cosmos 帳戶。

當您不小心刪除 Azure Cosmos 帳戶時，我們可以將資料還原到具有相同名稱的新帳戶，但前提是帳戶名稱不在使用中。 因此，建議您不要在刪除帳戶之後重新建立帳戶。 因為它不僅會防止還原的資料使用相同的名稱，也會讓探索正確的帳戶從困難的情況下還原。

當您不小心刪除 Azure Cosmos 資料庫時，可以還原整個資料庫或該資料庫內的容器子集。 您也可以在資料庫中選取特定的容器，並將它們還原至新的 Azure Cosmos 帳戶。

當您不小心刪除或修改容器內的一或多個專案時 (資料損毀案例) ，您必須指定要還原至的時間。 如果資料損毀，時間就很重要。 因為容器是即時的，所以備份仍在執行中，因此，如果您等待超過保留期限 (預設值為8小時) 會覆寫備份。 為了防止覆寫備份，請至少將帳戶的備份保留期增加到7天。 最好是從資料損毀的8小時內增加您的保留期。

如果您不小心刪除或損毀資料，請在8小時內聯絡 [Azure 支援](https://azure.microsoft.com/support/options/) ，讓 Azure Cosmos DB 的團隊可以協助您從備份還原資料。 如此一來，Azure Cosmos DB 支援小組將擁有足夠的時間來還原您的帳戶。

> [!NOTE]
> 還原資料之後，不會將所有來源功能或設定都傳送至還原的帳戶。 下列設定不會傳送至新的帳戶：
> * VNET 存取控制清單
> * 預存程式、觸發程式和使用者定義函數
> * 多區域設定  

如果您在資料庫層級布建輸送量，此案例中的備份和還原程式會發生在整個資料庫層級，而不是在個別容器層級上。 在這種情況下，您無法選取要還原的一部分容器。

## <a name="options-to-manage-your-own-backups"></a>可用來管理自有備份的選項

使用 Azure Cosmos DB SQL API 帳戶時，您也可以藉由下列其中一個方法，來維護您自己的備份：

* 使用 [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) 將資料定期移動到所選儲存體。

* 使用 Azure Cosmos DB [變更](change-feed.md) 摘要來定期讀取資料，以進行完整備份或增量變更，並將其儲存在您自己的儲存體中。

## <a name="post-restore-actions"></a>還原後的動作

資料還原的主要目標是復原您不小心刪除或修改的資料。 因此，建議您先檢查所復原資料的內容，以確保所含資料符合您的預期。 如果一切看起來都沒問題，您可以將資料移轉回主要帳戶。 雖然您可以使用已還原的帳戶做為新的使用中帳戶，但如果您有生產工作負載，則不建議使用此選項。 

還原資料之後，您會收到有關新帳戶名稱 (格式通常會是 `<original-name>-restored1`) 及帳戶還原時間點的通知。 已還原帳戶中會有相同的已佈建輸送量和編製索引原則，且位於與原始帳戶相同的區域。 身為訂用帳戶管理員或共同管理員的使用者，可以看到還原的帳戶。

### <a name="migrate-data-to-the-original-account"></a>將資料移轉至原始帳戶

以下是將資料移轉回原始帳戶的不同方式：

* 使用 [Azure Cosmos DB 資料移轉工具](import-data.md)。
* 使用 [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)。
* 使用 Azure Cosmos DB 中的 [變更](change-feed.md) 摘要。
* 您可以撰寫自己的自訂程式碼。

建議您在完成資料遷移後立即刪除容器或資料庫。 如果沒有刪除已還原的資料庫或容器，則會產生要求單位、儲存體和輸出的成本。

## <a name="next-steps"></a>後續步驟

接下來，您可以了解如何從 Azure Cosmos 帳戶還原資料，或了解如何將資料遷移至 Azure Cosmos 帳戶

* 若要提出還原要求，請[從 Azure 入口網站提出票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以連絡 Azure 支援
* [使用 Cosmos DB 變更摘要](change-feed.md)將資料移至 Azure Cosmos DB。
* [使用 Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) 將資料移至 Azure Cosmos DB。

