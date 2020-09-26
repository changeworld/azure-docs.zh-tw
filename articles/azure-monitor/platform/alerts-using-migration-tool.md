---
title: 遷移 Azure 監視器警示規則
description: 瞭解如何使用自發的遷移工具來遷移傳統警示規則。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: e49525018a3e23ecbbf92d7a8b3f7c50804432b8
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358656"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>使用自發的遷移工具來遷移傳統警示規則

如 [先前所宣佈](monitoring-classic-retirement.md)，Azure 監視器中的傳統警示已淘汰，但仍對尚未支援新警示的資源提供有限的使用。 使用傳統警示規則的客戶以及想要自行觸發遷移的客戶，都可以使用 Azure 入口網站的遷移工具。 本文說明如何使用該遷移工具，這項工具也會用於剩餘的警示，以供進一步公告。

## <a name="benefits-of-new-alerts"></a>新警示的優點

傳統警示會取代為 Azure 監視器中的新整合警示。 新的警示平臺有下列優點：

- 您可以針對 [更多 Azure 服務](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)，針對各種多維度計量發出警示。
- 新的計量警示支援 [多重資源的警示規則](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) ，可大幅降低管理許多規則的額外負荷。
- 整合的通知機制，它支援：
  - [動作群組](action-groups.md)是適用于所有新警示類型的模組化通知機制， (度量、記錄和活動記錄) 。
  - 新的通知機制，例如 SMS、voice 和 ITSM 連接器。
- [整合警示體驗](alerts-overview.md)會將不同信號的所有警示， (計量、記錄和活動記錄) 放入同一個位置。

## <a name="before-you-migrate"></a>遷移之前

遷移程式會將傳統警示規則轉換為新的對等警示規則，並建立動作群組。 準備時，請注意下列幾點：

- 用來建立和管理新警示規則的通知承載格式和 Api，與傳統警示規則的不同之處在于它們支援更多功能。 [瞭解如何準備進行遷移](alerts-prepare-migration.md)。

- 某些傳統警示規則無法使用工具來遷移。 [瞭解哪些規則無法遷移，以及要](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)如何處理它們。

    > [!NOTE]
    > 遷移程式不會影響傳統警示規則的評估。 它們會繼續執行並傳送警示，直到它們遷移且新的警示規則生效。

## <a name="how-to-use-the-migration-tool"></a>如何使用移轉工具

若要在 Azure 入口網站中觸發傳統警示規則的遷移，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [ **監視**]。

1. 選取 [ **警示**]，然後選取 [ **管理警示規則** ] 或 [ **查看傳統警示**]。

1. 選取 [ **遷移至新規則** ]，移至 [遷移] 登陸頁面。 此頁面會顯示您所有訂用帳戶及其遷移狀態的清單：

    ![顯示 [遷移警示規則] 頁面的螢幕擷取畫面。](media/alerts-migration/migration-landing.png "遷移規則")

    您可以使用此工具來遷移的所有訂用帳戶都會標示為 **準備好遷移**。

    > [!NOTE]
    > 使用傳統警示規則的所有訂用帳戶階段都會推出遷移工具。 在首度發行的早期階段中，您可能會看到某些標示為尚未準備好進行遷移的訂用帳戶。

1. 選取一或多個訂用帳戶，然後選取 [ **預覽遷移**]。

    產生的頁面會顯示每次針對一個訂用帳戶遷移的傳統警示規則詳細資料。 您也可以選取 **[下載此訂用帳戶的遷移詳細資料** ]，以取得 CSV 格式的詳細資料。

    ![螢幕擷取畫面顯示 [遷移警示規則] 頁面，其中包含可下載此訂用帳戶之遷移詳細資料的連結，而且您可以指定用於遷移通知的電子郵件。](media/alerts-migration/migration-preview.png "預覽遷移")

1. 指定一或多個要收到遷移狀態通知的電子郵件地址。 當遷移完成或您需要任何動作時，您將會收到電子郵件。

1. 選取 [ **開始遷移**]。 閱讀確認對話方塊中顯示的資訊，並確認您已經準備好開始進行遷移程式。

    > [!IMPORTANT]
    > 在您起始訂用帳戶的遷移之後，您將無法編輯或建立該訂用帳戶的傳統警示規則。 這種限制可確保在遷移至新規則期間，不會遺失傳統警示規則的任何變更。 雖然您無法變更傳統警示規則，但是仍會繼續執行並提供警示，直到遷移為止。 完成訂用帳戶的遷移之後，您就無法再使用傳統警示規則。

    ![螢幕擷取畫面會顯示您的遷移確認提示，其中包含重要資訊，其中包含可深入瞭解的連結，然後再繼續進行。](media/alerts-migration/migration-confirm.png "確認開始遷移")

1. 當遷移完成，或是您需要採取動作時，您會在稍早提供的位址收到電子郵件。 您也可以在入口網站中，定期檢查遷移登陸頁面上的狀態。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>為什麼我的訂用帳戶列為尚未準備好進行遷移？

遷移工具會以階段推出給客戶。 在早期階段中，大部分或所有的訂用帳戶可能會標示為 **尚未準備好進行遷移**。 

當訂用帳戶準備好進行遷移時，訂用帳戶擁有者會收到一封電子郵件訊息，指出該工具可供使用。 請留意這則訊息。

### <a name="who-can-trigger-the-migration"></a>誰可以觸發遷移？

在訂用帳戶層級指派「監視參與者」角色的使用者，可以觸發遷移。 [深入瞭解角色型存取控制的遷移程式](alerts-understand-migration.md#who-can-trigger-the-migration)。

### <a name="how-long-will-the-migration-take"></a>遷移需要多久的時間？

在一小時內，大部分訂用帳戶的遷移作業都已完成。 您可以追蹤遷移登陸頁面上的遷移進度。 在遷移期間，請確定您的警示仍在傳統警示系統或新警示系統中執行。

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>如果我在遷移期間遇到問題，該怎麼辦？

請參閱 [疑難排解指南](alerts-understand-migration.md#common-problems-and-remedies) ，以取得您在遷移期間可能遇到的問題的協助。 如果您需要採取任何動作來完成遷移，您將會收到在設定工具時所提供的電子郵件地址通知。

## <a name="next-steps"></a>後續步驟

- [準備移轉](alerts-prepare-migration.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
