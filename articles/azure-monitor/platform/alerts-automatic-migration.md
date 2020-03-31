---
title: 瞭解 Azure 監視器經典警報的自動遷移過程的工作原理
description: 瞭解自動遷移過程的工作原理。
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668242"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>瞭解經典警報規則的自動遷移過程

正如[先前宣佈的](monitoring-classic-retirement.md)，Azure 監視器中的經典警報將于 2019 年 9 月停用（最初為 2019 年 7 月）。 作為停用過程的一部分，Azure 門戶中提供了[遷移工具](alerts-using-migration-tool.md)，供客戶自己觸發遷移。 如果您在 2019 年 8 月 31 日之前尚未使用遷移工具，Azure 監視器將從 2019 年 9 月 1 日開始自動遷移經典警報的過程。
本文將引導您完成自動遷移過程，並説明您解決可能會遇到的任何問題。

  > [!NOTE]
  > 本文僅適用于 Azure 公共雲。 Azure 監視器在 Azure 政府雲和 Azure 中國 21Vianet 中的經典警報的停用過程將在未來日期公佈。

## <a name="what-will-happen-during-the-automatic-migration-process"></a>在自動遷移過程中會發生什麼？

- 從**2019 年 9 月 1 日起**，客戶將不能創建任何新的經典警報規則，除非在某些[指標](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)上。
  - 對於例外情況，客戶可以繼續創建新的經典警報規則，並在 2020 年 6 月之前使用其經典警報。
- 從**2019 年 9 月 1**日起，經典警報的遷移將分批觸發任何具有經典警報的客戶。
- 與自願遷移工具一樣，某些不可遷移的經典警報規則將保留為它們。 這些經典警報規則將繼續支援到 2020 年 6 月。 但是，任何不正確經典警報規則都將被刪除，因為它們是不起作用的。
監視已刪除的目標資源或[不再支援的指標](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics)上的任何經典警報規則都被視為無效。
- 訂閱的遷移開始後，除非存在任何問題，否則遷移應在一小時內完成。 客戶可以在[Azure 監視器 中的遷移邊欄選項卡](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)上監視遷移狀態。
- 訂閱擁有者將收到一封有關成功完成遷移的電子郵件。
- 如果在遷移過程中存在任何問題，訂閱擁有者也會收到一封電子郵件，通知他們同樣的問題。 客戶可以使用遷移邊欄選項卡查看問題的完整詳細資訊。
- 如果需要客戶執行臨時禁用資源鎖定或更改策略分配等操作，客戶需要在 2019 年 10 月 31 日前解決任何問題。 如果屆時問題未得到解決，則無法保證成功遷移經典警報。

    > [!NOTE]
    > 如果不想等待自動遷移過程啟動，您仍可以使用遷移工具自願觸發遷移。

## <a name="important-things-to-note"></a>需要注意的重要事項

遷移過程將經典警報規則轉換為新的等效警報規則，並創建操作組。 在準備時，請注意以下幾點：

- 新警報規則的通知有效負載格式不同于經典警報規則的通知有效負載格式，因為它們支援更多功能。 如果您有由經典警報規則引發的邏輯應用、Runbook 或 Webhook，則由於通知負載的差異，遷移完成後，它們可能會按預期方式停止運行。 [瞭解如何為遷移做好準備](alerts-prepare-migration.md)。

- 無法使用該工具遷移某些經典警報規則。 [瞭解哪些規則不能遷移，以及如何處理它們](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)。

    > [!NOTE]
    > 遷移過程不會影響經典警報規則的評估。 它們將繼續運行併發送警報，直到遷移且新的警報規則生效。

## <a name="what-if-the-automatic-migration-fails"></a>如果自動遷移失敗怎麼辦？

當自動遷移過程失敗時，訂閱擁有者將收到一封電子郵件，通知他們問題。 您可以使用 Azure 監視器中的遷移邊欄選項卡查看問題的完整詳細資訊。

有關遷移期間可能遇到問題的説明[，請參閱故障排除指南](alerts-understand-migration.md#common-problems-and-remedies)。

  > [!NOTE]
  > 如果需要客戶執行臨時禁用資源鎖定或更改策略分配等操作，客戶需要在 2019 年 10 月 31 日前解決任何問題。 如果屆時問題未得到解決，則無法保證成功遷移經典警報。

## <a name="next-steps"></a>後續步驟

- [移轉準備工作](alerts-prepare-migration.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
