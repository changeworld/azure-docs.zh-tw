---
title: 移轉 Azure 監視器警示規則
description: 瞭解如何使用自願遷移工具遷移經典警報規則。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: ab5c16995a2d2bad6e44f0f9d1187ca3d66be1b6
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114257"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>使用自願移工具移至經典警示規則

正如[先前宣佈的](monitoring-classic-retirement.md),Azure 監視器中的經典警報將於 2019 年 9 月停用(最初為 2019 年 7 月)。 Azure 門戶中可以使用使用經典警報規則並希望自己觸發遷移的客戶使用遷移工具。 本文介紹如何在 2019 年 9 月自動遷移開始之前使用遷移工具自願遷移經典警報規則。

> [!NOTE]
> 由於延遲推出遷移工具,經典警報遷移的停用日期從最初宣佈的 2019 年 6 月[30 日延長至 2019 年 8 月 31](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)日。

## <a name="benefits-of-new-alerts"></a>新警報的好處

經典警報正在被 Azure 監視器中新的統一警報所取代。 新的警報平臺具有以下優點:

- 您可以針對[更多 Azure 服務](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)對各種多維指標發出警報。
- 新的指標警報支援[多資源警報規則](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor),這大大降低了管理許多規則的開銷。
- 統一通知機制,支援:
  - [操作組](action-groups.md),一種模組化通知機制,適用於所有新的警報類型(指標、日誌和活動日誌)。
  - 新的通知機制,如簡訊、語音和 ITSM 連接器。
- [統一的警報體驗](alerts-overview.md)將不同信號(指標、日誌和活動日誌)上的所有警報帶到一個位置。

## <a name="before-you-migrate"></a>遷移前

遷移過程將經典警報規則轉換為新的等效警報規則,並創建操作組。 在準備時,請注意以下幾點:

- 通知有效負載格式和用於創建和管理新警報規則的 API 都與經典警報規則不同,因為它們支援更多功能。 [瞭解如何為遷移做好準備](alerts-prepare-migration.md)。

- 無法使用該工具遷移某些經典警報規則。 [瞭解哪些規則不能遷移,以及如何處理它們](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)。

    > [!NOTE]
    > 遷移過程不會影響經典警報規則的評估。 它們將繼續運行並發送警報,直到遷移且新的警報規則生效。

## <a name="how-to-use-the-migration-tool"></a>如何使用移轉工具

要在 Azure 門戶中觸發經典警報規則的遷移,請按照以下步驟操作:

1. 在[Azure 門戶](https://portal.azure.com)中,選擇 **「監視器**」。

1. 選擇**警報**,然後選擇 **「管理警報規則**」或 **「查看經典警報**」。

1. 選擇 **「遷移到新規則**」以轉到遷移著陸頁」。 此頁顯示所有訂閱及其移轉狀態的清單:

    ![遷移登陸](media/alerts-migration/migration-landing.png "移轉規則")

    可以使用 該工具遷移的所有訂閱都標記為 **「準備遷移**」。

    > [!NOTE]
    > 遷移工具分階段推出到使用經典警報規則的所有訂閱。 在推出的早期階段,您可能會看到某些訂閱標記為尚未準備好遷移。

1. 選擇一個或多個訂閱,然後選擇 **「預覽遷移**」。。

    生成的頁面顯示將一次為一個訂閱遷移的經典警報規則的詳細資訊。 您還可以選擇 **「下載此訂閱的遷移詳細資訊」** 以 CSV 格式取得詳細資訊。

    ![移轉預覽](media/alerts-migration/migration-preview.png "預覽移轉")

1. 指定一個或多個電子郵件地址以通知遷移狀態。 移轉完成後,或者如果需要您執行任何操作,您將收到電子郵件。

1. 選擇 **「開始遷移**」。 閱讀確認對話框中顯示的資訊,確認您已準備好開始遷移過程。

    > [!IMPORTANT]
    > 啟動訂閱遷移后,將無法編輯或創建該訂閱的經典警報規則。 此限制可確保在遷移到新規則期間不會丟失對經典警報規則的更改。 儘管您無法更改經典警報規則,但它們仍將繼續運行並提供警報,直到它們已遷移。 完成訂閱的遷移後,您不能再使用經典警報規則。

    ![遷移確認](media/alerts-migration/migration-confirm.png "確認啟動移轉")

1. 移轉完成後,或者如果需要您執行操作,您將收到一封電子郵件,位址位於您之前提供的位址。 您還可以定期檢查門戶中遷移登錄頁上的狀態。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>為什麼我的訂閱未準備好進行遷移?

遷移工具分階段向客戶推出。 在早期階段,大多數或所有訂閱可能標記為**未準備好遷移**。 

當訂閱準備好進行遷移時,訂閱擁有者將收到一封電子郵件,指出該工具可用。 留意此消息。

### <a name="who-can-trigger-the-migration"></a>誰可以觸發遷移?

在訂閱級別分配了監視參與者角色的用戶能夠觸發遷移。 [詳細瞭解移到作用控制](alerts-understand-migration.md#who-can-trigger-the-migration)。

### <a name="how-long-will-the-migration-take"></a>遷移需要多長時間?

大多數訂閱在一小時內完成遷移。 您可以在遷移著陸頁上跟蹤遷移進度。 在遷移期間,請確保警報仍在經典警報系統中或新警報系統中運行。

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>如果在遷移過程中遇到問題,我該怎麼辦?

有關遷移期間可能遇到問題[,請參閱故障排除指南](alerts-understand-migration.md#common-problems-and-remedies)。 如果需要執行任何操作才能完成移轉,則會在設定工具時透過您提供的電子郵件地址通知您。

## <a name="next-steps"></a>後續步驟

- [移轉準備工作](alerts-prepare-migration.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
