---
title: 瞭解 Azure 監視器傳統警示的自動遷移程式如何運作
description: 瞭解自動遷移程式的運作方式。
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77668242"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>瞭解傳統警示規則的自動遷移程式

如[先前所宣佈](monitoring-classic-retirement.md)，Azure 監視器中的傳統警示將于2019年9月淘汰（原本是在2019年7月）。 作為淘汰程式的一部分，Azure 入口網站提供[遷移工具](alerts-using-migration-tool.md)，讓客戶自行觸發遷移。 如果您未在2019年8月31日前使用遷移工具，Azure 監視器將會開始自動遷移傳統警示的程式，自2019年9月1日起生效。
本文會逐步引導您完成自動遷移程式，並協助您解決可能遇到的任何問題。

  > [!NOTE]
  > 本文僅適用于 Azure 公用雲端。 Azure Government cloud 和 Azure 中國世紀 Azure 監視器傳統警示的淘汰程式將在未來的日期公告。

## <a name="what-will-happen-during-the-automatic-migration-process"></a>自動遷移過程中會發生什麼事？

- 從**2019 年9月 1**日開始，客戶將無法建立任何新的傳統警示規則，但不包括[特定計量](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)。
  - 針對例外狀況，客戶可以繼續建立新的傳統警示規則，並使用其傳統警示直到2020年6月為止。
- 從**2019 年9月 1**日開始，任何具有傳統警示的客戶都會以批次方式觸發傳統警示的遷移。
- 如同自發的遷移工具，某些未可移轉的傳統警示規則會保持不被使用。 在2020年6月之前，將會繼續支援這些傳統警示規則。 不過，任何不正確傳統警示規則將會遭到刪除，因為它們無法正常運作。
監視已刪除目標資源的任何傳統警示規則，或已不再[支援的計量](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics)，都視為無效。
- 一旦您的訂用帳戶開始遷移之後，除非有任何問題，否則應在一小時內完成遷移。 客戶可以在[Azure 監視器的 [遷移](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)] 分頁上監視遷移的狀態。
- 訂用帳戶擁有者將會在成功完成遷移時收到一封電子郵件。
- 如果在遷移期間發生任何問題，訂用帳戶擁有者也會收到一封電子郵件，通知他們相同。 客戶可以使用 [遷移] 分頁來查看問題的完整詳細資料。
- 如果客戶需要採取動作，例如暫時停用資源鎖定或變更原則指派，客戶就必須在2019年10月31日解決任何問題。 如果未解決問題，則無法保證傳統警示的成功遷移。

    > [!NOTE]
    > 如果您不想等候自動遷移程式啟動，您仍然可以使用遷移工具來主動觸發遷移。

## <a name="important-things-to-note"></a>要注意的重要事項

遷移程式會將傳統警示規則轉換成新的對等警示規則，並建立動作群組。 在準備時，請注意下列幾點：

- 新警示規則的通知裝載格式與傳統警示規則不同，因為它們支援更多的功能。 如果您有由傳統警示規則引發的邏輯應用程式、runbook 或 webhook，當遷移完成時，可能會因為通知承載的差異而停止正常運作。 [瞭解如何準備進行遷移](alerts-prepare-migration.md)。

- 某些傳統警示規則無法使用工具來遷移。 [瞭解哪些規則無法遷移，以及要如何處理它們](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)。

    > [!NOTE]
    > 遷移程式不會影響傳統警示規則的評估。 他們會繼續執行並傳送警示，直到遷移完畢，新的警示規則才會生效。

## <a name="what-if-the-automatic-migration-fails"></a>如果自動遷移失敗，會發生什麼事？

當自動遷移程式失敗時，訂用帳戶擁有者會收到一封電子郵件，通知他們該問題。 您可以使用 Azure 監視器中的 [遷移] 分頁來查看問題的完整詳細資料。

請參閱[疑難排解指南](alerts-understand-migration.md#common-problems-and-remedies)，以協助您瞭解在遷移期間可能會面臨的問題。

  > [!NOTE]
  > 如果客戶需要採取動作，例如暫時停用資源鎖定或變更原則指派，客戶就必須在2019年10月31日解決任何問題。 如果未解決問題，則無法保證傳統警示的成功遷移。

## <a name="next-steps"></a>後續步驟

- [移轉準備工作](alerts-prepare-migration.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
