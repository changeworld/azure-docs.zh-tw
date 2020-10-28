---
title: Azure 入口網站中的帳戶管理員工作
description: 說明如何在 Azure 入口網站中執行付款作業
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contentperfq2
ms.openlocfilehash: c9f344ec6eae40db2c76f3712df6f1f9d8cead37
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668865"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Azure 入口網站中的帳戶管理員工作

本文說明如何在 Azure 入口網站中執行下列工作：
- 管理訂用帳戶的付款方式
- 移除訂用帳戶的消費限制
- 增加 Azure In Open 訂用帳戶的點數

您必須是帳戶管理員，才能執行這些工作。

## <a name="navigate-to-your-subscriptions-payment-methods"></a>瀏覽至訂用帳戶的付款方式

1. 以帳戶管理員身分登入 Azure 入口網站。

1. 搜尋 [成本管理 + 帳單]  。

    ![顯示搜尋 [成本管理 + 帳單] 的螢幕擷取畫面 ](./media/account-admin-tasks/search-bar.png)

1. 在 [我的訂用帳戶]  清單中，選取要新增信用卡的訂用帳戶。

   ![顯示 [成本管理 + 帳單] 頁面的螢幕擷取畫面，您可在其中選取訂用帳戶。](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > 如果您在這裡未看到某些訂用帳戶，原因可能是您在某個時間點變更了訂用帳戶目錄。 若要看到這些訂用帳戶，您必須將目錄切換到原始目錄 (您最初註冊時所在的目錄)。 然後，重複步驟 2。

1. 選取 [ **付款方式** ]。

    ![顯示 [付款方式] 頁面的螢幕擷取畫面，您可在其中新增付款方式。](./media/account-admin-tasks/subscription-payment-methods-blade.png)

您可以在這裡新增信用卡、變更使用中的付款方式、編輯信用卡詳細資料，以及刪除信用卡。

### <a name="change-active-payment-method"></a>變更使用中的付款方式

您可藉由新增信用卡或選擇已儲存的信用卡來變更使用中的付款方式。 若要將使用中的付款方式變更為新的信用卡：

1. 在左上角選取 [+] 以新增信用卡。

    ![顯示加號的螢幕擷取畫面](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. 在右邊的表單中輸入信用卡詳細資料。

    ![顯示新增信用卡表單的螢幕擷取畫面。](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. 若要讓此卡片成為您的有效付款方式，請在表單上方勾選 [將此方式設定成我的有效付款方式]  旁的方塊。 對於所有與選取的訂用帳戶使用相同卡片的訂用帳戶，這張卡片都將成為使用中的付款方式。

    ![顯示讓卡片成為使用中付款方式的核取方塊螢幕擷取畫面。](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. 選取 [下一步]  。

若要將使用中的付款方式變更為已儲存的信用卡：

1. 選取要設為有效付款方式的卡片旁邊的方塊。

    ![顯示信用卡旁已核取方塊的螢幕擷取畫面](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. 在命令列中按一下 [設定使用中]  。

    ![顯示 [設定使用中] 按鈕的螢幕擷取畫面](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>編輯信用卡詳細資料

若要編輯信用卡詳細資料 (例如到期日或位址)，請按一下您要編輯的信用卡。 信用卡表單會出現在右側。

![顯示已選取信用卡的螢幕擷取畫面](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

更新信用卡詳細資料，然後按一下 [儲存]  。

### <a name="remove-a-credit-card-from-the-account"></a>從帳戶中移除信用卡

1. 選取您要刪除的卡片旁邊的方塊。

    ![顯示信用卡旁已核取方塊的螢幕擷取畫面](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. 在命令列中按一下 [刪除]  。

    ![顯示 [刪除] 按鈕的螢幕擷取畫面](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

如果您的信用卡是任何 Microsoft 訂用帳戶的有效付款方式，您就無法從 Azure 帳戶中將它移除。 請變更連結至此信用卡之所有訂用帳戶的有效付款方式，然後再試一次。

### <a name="switch-to-invoice-payment"></a>切換為發票付款

如果您有資格按發票付款 (支票/電匯)，可以在 Azure 入口網站中將您的訂用帳戶切換為發票付款 (支票/電匯)。

1. 在命令列中選取 [按發票付款]  。

    ![顯示已選取 [按發票付款] 之 [付款方式] 頁面的螢幕擷取畫面。](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. 輸入發票付款方式的位址。
1. 按 [下一步]  。

如果您想獲准按發票付款，請參閱[了解如何按發票付款](pay-by-invoice.md)。

### <a name="edit-invoice-payment-address"></a>編輯發票付款地址

若要編輯發票付款方式的地址，請在訂用帳戶的付款方式清單中，按一下 [發票]  。 地址表單隨即在右側開啟。

## <a name="remove-spending-limit"></a>移除消費限制

Azure 消費限制可避免您的花費超過您的點數額度。 只要您的 Azure 訂用帳戶所關聯的付款方式有效，您就可以隨時移除消費限制。 對於具有多個月點數的訂用帳戶類型 (例如 Visual Studio Enterprise 和 Visual Studio Professional)，您可以選擇在下一個計費週期開始時重新啟用消費限制。

消費限制不適用於採用承諾用量方案或採用隨用隨付定價的訂用帳戶。

1. 以帳戶管理員身分登入 Azure 入口網站。
1. 搜尋 [成本管理 + 帳單]  。

    ![顯示搜尋 [成本管理 + 帳單] 的螢幕擷取畫面 ](./media/account-admin-tasks/search-bar.png)

1. 在 [我的訂用帳戶]  清單中，選取您的 Visual Studio Enterprise 訂用帳戶。

   ![顯示 [我的訂用帳戶] 區域的螢幕擷取畫面，您可在其中選取您的 Visual Studio Enterprise 訂用帳戶。](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > 如果您在這裡未看到某些 Visual Studio 訂用帳戶，原因可能是您在某個時間點變更了訂用帳戶目錄。 若要看到這些訂用帳戶，您必須將目錄切換到原始目錄 (您最初註冊時所在的目錄)。 然後，重複步驟 2。

1. 在 [訂用帳戶] 概觀中，按一下橙色橫幅以移除消費限制。

    ![顯示 [移除消費限制] 橫幅的螢幕擷取畫面](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. 選擇您是否要無限期移除消費限制，或只將其用於目前計費週期。

   ![顯示 [移除消費限制] 刀鋒視窗的螢幕擷取畫面](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. 按一下 [選取付款方式]  以選擇您訂用帳戶的付款方式。 這將成為您訂用帳戶的使用中付款方式。

1. 按一下 [完成]  。

## <a name="add-credits-to-azure-in-open-subscription"></a>增加 Azure In Open 訂用帳戶的點數

如果您有 Azure in Open 授權訂用帳戶，您可藉由兌換產品金鑰或利用信用卡購買點數，在 Azure 入口網站中新增您訂用帳戶的點數。

1. 以帳戶管理員身分登入 Azure 入口網站。
1. 搜尋 [成本管理 + 帳單]  。

    ![顯示搜尋 [成本管理 + 帳單] 的螢幕擷取畫面 ](./media/account-admin-tasks/search-bar.png)

1. 在 [我的訂用帳戶]  清單中，選取您的 Azure in Open 訂用帳戶。

    ![顯示 [我的訂閱] 區域的螢幕擷取畫面，您可在其中選取您的 Azure in Open 訂用帳戶。](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > 如果您在這裡未看到訂用帳戶，原因可能是您已在某個時間點變更其目錄。 您必須將訂用帳戶的目錄切換到原始目錄 (您最初註冊時所在的目錄)。 然後，重複步驟 2。

1. 選取 [點數歷程記錄]  。

    ![顯示點數歷程記錄的螢幕擷取畫面](./media/account-admin-tasks/aio-credit-history-blade.png)

1. 在左上角，選取 [+] 以新增更多點數。

    ![顯示 [新增點數] 按鈕的螢幕擷取畫面](./media/account-admin-tasks/aio-credit-history-plus.png)

1. 在下拉式清單中選取付款方式類型。 您可以使用信用卡來新增產品金鑰或購買點數。

    ![[新增點數] 刀鋒視窗中 [付款方式] 下拉式清單的螢幕擷取畫面](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. 如果您選擇產品金鑰：
    - 輸入產品金鑰
    - 按一下 [驗證]  。

1. 如果您選擇信用卡：
    - 按一下 [選取付款方式]  以新增信用卡或選取現有的信用卡。
    - 指定您要新增的點數額度。

1. 按一下 [套用]  。

## <a name="troubleshooting"></a>疑難排解
我們不支援虛擬或預付卡。 如果您在新增或更新有效信用卡時發生錯誤，請嘗試以私人模式開啟瀏覽器。

## <a name="next-steps"></a>後續步驟
- 深入了解[分析非預期的費用](../understand/analyze-unexpected-charges.md)
