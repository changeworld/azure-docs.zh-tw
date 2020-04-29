---
title: 遷移 Azure 監視器警示規則
description: 瞭解如何使用自發的遷移工具來遷移傳統警示規則。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: ab5c16995a2d2bad6e44f0f9d1187ca3d66be1b6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114257"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>使用自發的遷移工具來遷移傳統警示規則

如[先前所宣佈](monitoring-classic-retirement.md)，Azure 監視器中的傳統警示將于2019年9月淘汰（原本是在2019年7月）。 Azure 入口網站提供遷移工具給使用傳統警示規則的客戶，以及想要自行觸發遷移的使用者。 本文說明如何使用遷移工具，在2019年9月開始自動遷移之前，先主動遷移傳統警示規則。

> [!NOTE]
> 由於遷移工具的推出延遲，傳統警示遷移的淘汰日期已從2019年6月30日最初宣佈的日期[延長到2019年8月31日](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)。

## <a name="benefits-of-new-alerts"></a>新警示的優點

傳統警示會由 Azure 監視器中的新整合警示所取代。 新的警示平臺具有下列優點：

- 您可以針對[許多 Azure 服務](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)，針對各種多維度計量發出警示。
- 新的計量警示支援[多資源警示規則](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)，可大幅降低管理許多規則的額外負荷。
- 整合通知機制，支援：
  - [動作群組](action-groups.md)，這是適用于所有新警示類型（度量、記錄和活動記錄）的模組化通知機制。
  - 新的通知機制，像是 SMS、voice 和 ITSM 連接器。
- [整合警示體驗](alerts-overview.md)會將不同信號（計量、記錄和活動記錄）上的所有警示帶入一個位置。

## <a name="before-you-migrate"></a>在您遷移之前

遷移程式會將傳統警示規則轉換成新的對等警示規則，並建立動作群組。 在準備時，請注意下列幾點：

- 通知承載格式和用來建立和管理新警示規則的 Api，與傳統警示規則的不同，因為它們支援更多的功能。 [瞭解如何準備進行遷移](alerts-prepare-migration.md)。

- 某些傳統警示規則無法使用工具來遷移。 [瞭解哪些規則無法遷移，以及要如何處理它們](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)。

    > [!NOTE]
    > 遷移程式不會影響傳統警示規則的評估。 他們會繼續執行並傳送警示，直到遷移完畢，新的警示規則才會生效。

## <a name="how-to-use-the-migration-tool"></a>如何使用移轉工具

若要在 Azure 入口網站中觸發傳統警示規則的遷移，請遵循下列步驟：

1. 在[Azure 入口網站](https://portal.azure.com)中，選取 [**監視**]。

1. 選取 [**警示**]，然後選取 [**管理警示規則**] 或 [**查看傳統警示**]。

1. 選取 [**遷移至新規則**] 以移至 [遷移] 登陸頁面。 此頁面會顯示您所有訂用帳戶及其遷移狀態的清單：

    ![遷移-登陸](media/alerts-migration/migration-landing.png "遷移規則")

    所有可以使用工具遷移的訂閱都會標示為「**準備好」進行遷移**。

    > [!NOTE]
    > 遷移工具會分階段向所有使用傳統警示規則的訂用帳戶推出。 在首度發行的早期階段中，您可能會看到某些訂用帳戶標示為尚未準備好進行遷移。

1. 選取一個或多個訂用帳戶，然後選取 [預覽] [**遷移**]。

    產生的頁面會顯示一次將針對一個訂用帳戶遷移的傳統警示規則詳細資料。 您也可以選取 **[下載此訂用帳戶的遷移詳細資料**]，以 CSV 格式取得詳細資料。

    ![遷移-預覽](media/alerts-migration/migration-preview.png "預覽遷移")

1. 指定一或多個要收到遷移狀態通知的電子郵件地址。 當您完成遷移或需要任何動作時，您會收到電子郵件。

1. 選取 [**開始遷移**]。 閱讀確認對話方塊中顯示的資訊，並確認您已經準備好開始進行遷移程式。

    > [!IMPORTANT]
    > 起始訂用帳戶的遷移之後，您將無法編輯或建立該訂用帳戶的傳統警示規則。 這種限制可確保在遷移至新規則時，不會遺失傳統警示規則的任何變更。 雖然您無法變更傳統警示規則，但它們仍會繼續執行並提供警示，直到它們遷移為止。 在您的訂用帳戶完成遷移之後，您將無法再使用傳統警示規則。

    ![遷移-確認](media/alerts-migration/migration-confirm.png "確認開始遷移")

1. 當遷移完成時，或如果您需要採取動作，您會在先前提供的位址收到電子郵件。 您也可以在入口網站中的 [遷移] 登陸頁面定期檢查狀態。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>我的訂用帳戶為何列為尚未準備好進行遷移？

遷移工具會分階段向客戶推出。 在早期階段中，大部分或所有的訂用帳戶可能會標示為**未準備好進行遷移**。 

當訂用帳戶準備好進行遷移時，訂用帳戶擁有者會收到一封電子郵件訊息，指出該工具可供使用。 請留意此訊息。

### <a name="who-can-trigger-the-migration"></a>誰可以觸發遷移？

在訂用帳戶層級指派給他們的「監視參與者」角色的使用者，可以觸發該遷移。 [深入瞭解以角色為基礎的存取控制，以進行遷移程式](alerts-understand-migration.md#who-can-trigger-the-migration)。

### <a name="how-long-will-the-migration-take"></a>遷移需要多久的時間？

在一小時內，大部分的訂用帳戶都已完成遷移。 您可以在 [遷移] 登陸頁面上追蹤遷移進度。 在遷移期間，請確定您的警示仍在傳統警示系統中執行，或在新功能中執行。

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>如果我在遷移期間遇到問題，我可以怎麼做？

請參閱[疑難排解指南](alerts-understand-migration.md#common-problems-and-remedies)，以協助您瞭解在遷移期間可能會面臨的問題。 如果您需要採取任何動作來完成遷移，則會在您設定工具時所提供的電子郵件地址收到通知。

## <a name="next-steps"></a>後續步驟

- [移轉準備工作](alerts-prepare-migration.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
