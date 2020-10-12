---
title: 瞭解 Azure 監視器傳統警示的自動遷移程式如何運作
description: 瞭解自動遷移程式的運作方式。
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 6a2d032c6aa33b72fe422638df45ca48bf8b1036
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87847277"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>瞭解傳統警示規則的自動遷移程式

如 [先前所宣佈](monitoring-classic-retirement.md)，Azure 監視器中的傳統警示已淘汰，但仍對尚未支援新警示的資源提供有限的使用。 作為淘汰程式的一部分，Azure 入口網站中的 [遷移工具](alerts-using-migration-tool.md) 可供客戶自行觸發遷移。
本文將逐步引導您完成自動遷移程式，並協助您解決任何可能遇到的問題。

  > [!NOTE]
  > 本文僅適用于 Azure 公用雲端。 在 Azure Government 雲端和 Azure 中國世紀 Azure 監視器傳統警示的淘汰程式將在未來的日期宣佈。

## <a name="what-will-happen-during-the-automatic-migration-process"></a>自動遷移程式會發生什麼事？

- 自 **2019 年9月 1**日起，客戶將無法建立任何新的傳統警示規則，除非有 [特定計量](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)。
- 針對例外狀況，客戶可以繼續建立新的傳統警示規則，並使用其傳統警示，直到進一步公告為止。
- 從 **2019 年9月 1**日開始，將會針對任何具有傳統警示的客戶，以批次方式觸發傳統警示的遷移。
- 就像自願的遷移工具一樣，某些傳統警示規則會保持不可移轉。 在進一步公告之前，將會繼續支援這些傳統警示規則。 但是，將會刪除任何不正確傳統警示規則，因為它們無法正常運作。
任何監視已刪除之目標資源的傳統警示規則，或已 [不再支援的計量](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) 都會被視為無效。
- 一旦啟動訂用帳戶的遷移，除非有任何問題，否則應在一小時內完成遷移。 客戶可以 [在 Azure 監視器中監視遷移](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)分頁的遷移狀態。
- 訂用帳戶擁有者會在成功完成遷移時收到電子郵件。
- 如果在遷移期間發生任何問題，訂用帳戶擁有者也會收到一封電子郵件，通知他們相同。 客戶可以使用「遷移」分頁來查看問題的完整詳細資料。
- 如果客戶需要採取動作，例如暫時停用資源鎖定或變更原則指派，客戶就必須解決任何這類問題。 如果沒有解決這些問題，則無法保證傳統警示的成功遷移。

    > [!NOTE]
    > 如果您不想要等候自動遷移程式啟動，您仍然可以使用「遷移工具」主動觸發遷移。

## <a name="important-things-to-note"></a>需要注意的重要事項

遷移程式會將傳統警示規則轉換為新的對等警示規則，並建立動作群組。 準備時，請注意下列幾點：

- 新警示規則的通知承載格式與傳統警示規則的通知承載格式不同，因為它們支援更多功能。 如果您有由傳統警示規則引發的邏輯應用程式、runbook 或 webhook，它們可能會在遷移完成後停止正常運作，因為通知承載的差異。 [瞭解如何準備進行遷移](alerts-prepare-migration.md)。

- 某些傳統警示規則無法使用工具來遷移。 [瞭解哪些規則無法遷移，以及要](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)如何處理它們。

    > [!NOTE]
    > 遷移程式不會影響傳統警示規則的評估。 它們會繼續執行並傳送警示，直到它們遷移且新的警示規則生效。

## <a name="what-if-the-automatic-migration-fails"></a>如果自動遷移失敗，該怎麼辦？

當自動遷移程式失敗時，訂用帳戶擁有者會收到一封電子郵件，通知他們問題。 您可以使用 Azure 監視器中的「遷移」分頁來查看問題的完整詳細資料。

請參閱 [疑難排解指南](alerts-understand-migration.md#common-problems-and-remedies) ，以取得您在遷移期間可能遇到的問題的協助。

  > [!NOTE]
  > 如果客戶需要採取動作，例如暫時停用資源鎖定或變更原則指派，客戶就必須解決任何這類問題。 如果沒有解決問題，則無法保證傳統警示的成功遷移。

## <a name="next-steps"></a>後續步驟

- [準備移轉](alerts-prepare-migration.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
