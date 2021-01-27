---
title: 將共同銷售解決方案從 OCP 的合作夥伴中心遷移至 Microsoft AppSource 的
description: 瞭解如何將共同銷售解決方案從 OCP 操作人員遷移至 Microsoft AppSource) 的合作夥伴中心。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 12/07/2020
ms.openlocfilehash: 84d98eedb3ea6f3faaeaecf832811f9f4c78c7c5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880350"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>將協力公司的共同銷售解決方案遷移至商業市場

Microsoft 正在移動發佈體驗。 [商業 marketplace](overview.md)藉由在合作夥伴中心集中集中供應專案的建立和管理，讓您能夠透過 microsoft 的三個管道，簡化供應專案發佈至共同銷售的功能，而您已在其中管理與 Microsoft 的關聯性。

如果您是在商業 marketplace 中註冊的 Microsoft 合作夥伴，您可以：

- 集中發佈您的供應專案，並在 Microsoft direct 客戶、合作夥伴與賣方通道之間共同銷售。
- 確定您的供應專案位於正確的線上商店（[Microsoft AppSource](https://appsource.microsoft.com) 或 [Azure Marketplace](https://azure.microsoft.com)），以觸及符合您供應專案功能的數百萬個雲端客戶。
- 利用符合您商務目標的供應專案，推動您自己的發佈經驗與共同銷售。
- 在合作夥伴中心中讓您的供應專案發佈保持一致，您已在其中管理您的 Microsoft 關係和共同銷售商機。
- 解除鎖定 [Marketplace Rewards](partner-center-portal/marketplace-rewards.md)。

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>繼續與 Microsoft 共同銷售的必要條件

確定您有作用中的 Microsoft 合作夥伴網路成員資格，並已在合作夥伴中心的商業 marketplace 中註冊。

- [免費加入 Microsoft 合作夥伴網路。](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) 作為合作夥伴，您將能夠存取專屬資源、程式、工具和連線，以拓展您的業務。
- 如果您在商用 marketplace 中沒有帳戶，請 [立即註冊](partner-center-portal/create-account.md) 以繼續與 Microsoft 共同銷售並存取完整的發佈體驗。

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>發佈更新以取得共同銷售就緒狀態

為了讓 Microsoft 銷售人員和合作夥伴可以找到您的解決方案，其必須符合 [共同銷售的就緒需求](marketplace-co-sell.md)。 針對要受到激勵的 Microsoft 賣方，您的解決方案必須符合 [獎勵符合資格的需求](marketplace-co-sell.md)。 請在合作夥伴中心的 [共同銷售] 索引標籤上完成這些需求 (請參閱本文稍後的 [此圖](#cosell-tab)) 。

> [!NOTE]
> 在商業 marketplace 中，您的解決方案在發佈體驗中稱為「優惠」。

在您于商用 marketplace 註冊之後，就可以準備好從 OCP 的公司遷移您的解決方案。

從 OCP 的進行匯入解決方案之前，請遵循下列步驟：

1. 造訪您公司的 [發行者清單](https://partner.microsoft.com/dashboard/account/v3/publishers/list)。 它包含擁有發行存取權的帳戶擁有者、管理員和開發人員。 深入瞭解 [合作夥伴中心使用者角色](./partner-center-portal/manage-account.md#define-user-roles-and-permissions)。
2. 要求其中一個列出的連絡人， [將使用者新增](https://partner.microsoft.com/dashboard/account/usermanagement) 至商用 marketplace 作為 *經理* 或 *開發人員*，因為只有這些角色可以編輯和發佈方案。
3. 與您的開發人員合作，將您的解決方案從 OCP 動作帳戶移至商用 marketplace。
4. 決定您要執行下列作業：
    1. 將此解決方案與商業 marketplace 中的類似供應專案合併。
    1. 將此解決方案從 OCP 轉移到商用 marketplace。
    1. 捨棄此解決方案。

## <a name="migrate-your-solutions-from-ocp-gtm"></a>從 OCP 的進行遷移您的解決方案

1. 開始 [這裡](https://partner.microsoft.com/solutions/migration#)的遷移。
2. 選取 [ **總覽** ] 頁面，然後 **按一下這裡開始** 使用。

    :::image type="content" source="media/co-sell-migrate/migration-overview.png" alt-text="合作夥伴中心總覽] 頁面的 [總覽] 索引標籤。":::

3. 若要開始遷移，請選取 [ **方案** ] 索引標籤，以顯示與您的 MPN 識別碼相關聯的所有解決方案。

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="[方案] 索引標籤合作夥伴中心 [總覽] 頁面。":::

    > [!NOTE]
    > 此索引標籤會注意是否沒有任何解決方案可遷移至商用 marketplace。 若要繼續與 Microsoft 共同銷售，請確定已遷移的解決方案已發佈至商用 marketplace。

    若要深入瞭解解決方案狀態，請查看工具提示。 所有解決方案暫止動作都會列在 [ **動作**] 底下。<a name="beginmigration"></a>

4. 選取 [ **開始遷移** ] (查看您想要遷移的解決方案的上方) 影像，然後選取下列其中一個選項：

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="遷移的三個選項。":::

### <a name="merge-solution-with-a-similar-offer"></a>具有類似供應專案的合併解決方案

如果解決方案已在商業 marketplace 中發行，而且這兩個專案應合併為單一供應專案，請選取此選項。 這會避免建立重複的供應專案。

1. 識別現有的供應專案。
    1. 選取 **[我想要將此解決方案與商業 marketplace 中的類似供應專案合併**] (查看 [上述](#beginmigration)) **所需的動作** 映射。
    1. [ **動作 1** ] 索引標籤會顯示您的 OCP 可與您的 OCP 可建立關聯的即時商業 marketplace 供應專案。 從清單中選取 live 供應專案（如果有的話）。 如果沒有可供選擇的供應專案清單，請從 Microsoft AppSource 或 Azure Marketplace 輸入客戶導向的位址 (URL) 。
        [![合併處理的 [動作 1] 索引標籤。](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
    1. 選取 [繼續]。
1. 要求合併。
    1. [ **動作 2** ] 索引標籤會顯示要求與您所識別的 OCP 的進行合併的解決方案合併的指示。 若要要求合併，請選取 [ **儲存] & 連絡人支援**，這會在瀏覽器中開啟合作夥伴支援頁面。
    1. 選取 [**提供問題詳細資料**]，然後輸入下列內容： [ ![ 合併處理的 [動作 2]](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)索引標籤。
    1. 選取 [提交]  。 合作夥伴支援小組將在兩個工作天內與您聯繫。
    1. 合作夥伴支援將與您合作，以確保成功合併此供應專案，使其發佈為即時供應專案。

### <a name="migrate-this-solution-from-ocp-gtm"></a>從 OCP 的進行遷移的解決方案

當您的 OCP 進行中的解決方案尚未在商業 marketplace 中發佈對應的供應專案時，請選取此選項。 您將需要將此解決方案發佈至商用 marketplace，以繼續與 Microsoft 共同銷售並遷移此解決方案，藉由保留來自 OCP 轉移的資訊和物料清單來節省您的時間。 此選項會要求您選取供應專案類型。

1. 選取 **[我想要將此解決方案從 OCP 轉移到商用 marketplace** ] (查看 [上述](#beginmigration)的 **必要** 影像) ，然後 **繼續** 進行。
1. 在 [ **動作 1** ] 索引標籤上，選取 [供應專案類型](publisher-guide-by-offer-type.md)，然後 **繼續**。

    [![遷移進程的 [動作 1] 索引標籤。](media/co-sell-migrate/action-1-migrate.png)](media/co-sell-migrate/action-1-migrate.png#lightbox)

1. 在 [ **動作 2** ] 索引標籤上，從提供的清單中選取 [ [發行者設定檔](partner-center-portal/create-account.md) ]。 如果您沒有發行者帳戶，請在 [合作夥伴中心](https://partner.microsoft.com/solutions/migration)建立一個帳戶，在這裡選取它。

    [![遷移進程的 [動作 2] 索引標籤。](media/co-sell-migrate/action-2-migrate.png)](media/co-sell-migrate/action-2-migrate.png#lightbox)

1. 選取 [ **建立草稿供應** 專案]，將您的解決方案遷移至商用 marketplace 作為草稿。 它還不會存留。
1. 繼續合作夥伴中心中的發佈程式。 如需在合作夥伴中心中發佈的協助，請參閱下方 [的商業 marketplace 中的供應專案上線](#make-your-offer-live-in-the-marketplace) 。

### <a name="discard-this-solution"></a>捨棄此解決方案

當 OCP 的解決方案中的解決方案不再相關時，請選取此選項。 系統會要求您確認捨棄，您也可以稍後再復原。

1. 選取 [**我要捨棄此解決方案** (查看 [上述](#beginmigration)的 **必要** 影像) ，然後 **繼續** 進行。
2. 選取 [ **捨棄**]。

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="確認捨棄。":::

3. 若要復原捨棄，請選取 [ **復原捨棄**]。

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="復原捨棄連結。":::

4. 如果您需要其他協助，請選取 [ **取得協助** ] 索引標籤，以聯繫合作夥伴支援小組。

    :::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="[取得協助] 索引標籤上的 [支援] 連結。":::

## <a name="make-your-offer-live-in-the-marketplace"></a>讓您的供應專案在 marketplace 中上線

如果您選擇將供應專案遷移至商用 marketplace，則會以草稿的形式抵達。 您仍然需要發佈您的供應專案，使其上線于商業市場;這將會保留其共同銷售狀態、獎勵和參考管線。

如需有關您必須在供應專案發佈之前提供之資訊的詳細指示，請參閱適當的 [發佈指南](publisher-guide-by-offer-type.md)。 如需摘要，請參閱下面的。

1. 在 [ **總覽** ] 頁面的 [合作夥伴中心中，選取您的草稿供應專案名稱。

    :::image type="content" source="media/co-sell-migrate/offer-overview.png" alt-text="合作夥伴中心中的 [供應專案總覽] 頁面。":::

<a name="cosell-tab"></a>

2. 在每個索引標籤中完成所需的資訊。（選擇性）在下面的左側導覽功能表中完成 [ **轉售給 csp** ] 頁面 (，) 透過雲端解決方案提供者 (CSP) 方案轉售。 **深入瞭解** 連結和工具提示將引導您完成需求和詳細資料。

    :::image type="content" source="media/co-sell-migrate/offer-setup.png" alt-text="合作夥伴中心中的 [供應專案總覽設定] 索引標籤。":::

3. 部分 Microsoft 賣方相關的詳細資料是從 OCP 的轉移解決方案複製而來。 在 [ **與 Microsoft 共同銷售** ] 索引標籤中填寫剩餘的必要資訊，讓您的供應專案共同銷售就緒。 完成時，選取 [ **審核併發布**]。 如需詳細資訊，請參閱 [如何在合作夥伴中心提交共同銷售教材](marketplace-co-sell.md#how-to-submit-co-sell-materials-in-partner-center)。

    :::image type="content" source="media/co-sell-migrate/co-sell-page.png" alt-text="合作夥伴中心中的 [供應專案共同銷售] 索引標籤。":::

4. 檢查所有已提交的資訊之後，請選取 [ **發佈** ] 提交您的草稿供應專案以進行認證審核。

    :::image type="content" source="media/co-sell-migrate/co-sell-review-publish.png" alt-text="合作夥伴中心中的 [供應專案評論] 索引標籤和 [發佈] 按鈕。":::

5. 在 [ **總覽** ] 索引標籤上追蹤您提交的狀態。

    :::image type="content" source="media/publish-status-publisher-signoff.png" alt-text="合作夥伴中心中 [總覽] 索引標籤上的 [供應專案發行] 狀態列。":::

6. 我們會在認證評論完成時通知您。 如果我們提供可採取動作的意見反應，請加以解決，然後選取 [ **發行** ] 以起始重新認證。
7. 供應專案通過認證後，請使用提供的連結來預覽供應專案，並進行任何您可能想要的最終調整。 當您準備好時，請選取 [ **上線** ] (參閱上方的按鈕) ，將您的供應專案發佈至相關的商用 marketplace 店面 (s) 。 一旦上線，您的供應專案將會從原始的 OCP 進行中解決方案保留其共同銷售狀態。

## <a name="next-steps"></a>後續步驟

- [透過 CSP 合作夥伴轉售](cloud-solution-providers.md)
- [如何在合作夥伴中心提交共同銷售教材](marketplace-co-sell.md#how-to-submit-co-sell-materials-in-partner-center)
-  (PDF) 觀看這些[常見問題](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf)