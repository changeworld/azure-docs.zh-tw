---
title: 變更您用於 Azure 的信用卡
description: 描述如何變更用來支付 Azure 訂用帳戶的信用卡。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: banders
ms.openlocfilehash: f773c964d900c45a51eac433c3616a6e4b8978ae
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "92131134"
---
# <a name="add-or-update-a-credit-card-for-azure"></a>新增或更新用於 Azure 的信用卡

本文件適用於使用信用卡註冊 Azure Online 的客戶。

在 Azure 入口網站中，您可以將預設付款方式變更為新的信用卡以及更新信用卡詳細資料。 您必須是[帳戶管理員](../understand/subscription-transfer.md#whoisaa)才能進行這些變更。

如果您想要刪除信用卡，請參閱[刪除 Azure 帳單付款方法](delete-azure-payment-method.md)。

Microsoft Azure 支援的付款方法為信用卡和支票/電匯。 若要獲得以支票/電匯付款的核准，請參閱[按發票支付 Azure 訂用帳戶費用](pay-by-invoice.md)。

使用 Microsoft 客戶合約，付款方式會與帳單設定檔相關聯。 了解如何[檢查 Microsoft 客戶合約的存取](#check-the-type-of-your-account)。 如果您有 MCA，請跳到[管理 Microsoft 客戶合約的信用卡](#manage-credit-cards-for-a-microsoft-customer-agreement)。

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>管理 Azure 訂用帳戶的信用卡

下列各節適用於擁有 Microsoft Online Services 方案計費帳戶的客戶。 了解如何[查看您的計費帳戶類型](#check-the-type-of-your-account)。 如果您的計費帳戶類型是 Microsoft Online Services 方案，付款方式會與個別 Azure 訂用帳戶相關聯。 如果在新增信用卡之後收到錯誤，請參閱[在 Azure 註冊時信用卡遭拒](./troubleshoot-declined-card.md)。

### <a name="change-credit-card-for-a-subscription-by-adding-a-new-credit-card"></a>新增新的信用卡以變更訂用帳戶的信用卡

您可以將 Azure 訂用帳戶的預設信用額度變更到新的信用卡，或先前在 Azure 入口網站中儲存的信用卡。 您必須是帳戶管理員，才能變更信用卡。 如果多個訂用帳戶具有相同的有效付款方式，則變更任一訂用帳戶的有效付款方式也會更新其他訂用帳戶的有效付款方式。

您可以遵循下列步驟，將訂用帳戶的預設信用卡變更為新的信用卡：

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]。  
    ![顯示搜尋的螢幕擷取畫面](./media/change-credit-card/search.png)
1. 選取要新增信用卡的訂用帳戶。
1. 選取 [ **付款方式** ]。  
    ![顯示已選取 [管理付款方式] 選項的螢幕擷取畫面](./media/change-credit-card/payment-methods-blade-x.png)
1. 在左上角選取 [+] 以新增卡片。 信用卡表單會出現在右側。
1. 輸入信用卡詳細資料。  
    ![顯示新增卡片的螢幕擷取畫面](./media/change-credit-card/sub-add-new-x.png)
1. 若要讓此卡片成為您的有效付款方式，請在表單上方勾選 [將此方式設定成我的有效付款方式] 旁的方塊。 對於所有與選取的訂用帳戶使用相同卡片的訂用帳戶，這張卡片都將成為使用中的付款方式。
1. 選取 [下一步] 。

### <a name="change-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>將訂用帳戶的信用卡變更為之前儲存的信用卡

您也可以依照下列步驟，將訂用帳戶的預設信用卡變更為已儲存至帳戶的信用卡：

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]。  
    ![顯示搜尋的螢幕擷取畫面](./media/change-credit-card/search.png)
1. 選取要新增信用卡的訂用帳戶。
1. 選取 [ **付款方式** ]。
    ![顯示已選取 [管理付款方式] 選項的螢幕擷取畫面](./media/change-credit-card/payment-methods-blade-x.png)
1. 選取要設為有效付款方式的卡片旁邊的方塊。
1. 選取 [設為有效]。
    ![顯示已選取信用卡和設為有效的螢幕擷取畫面](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>編輯信用卡詳細資料

如果您的信用卡換卡，但號碼維持不變，請更新現有信用卡的詳細資料即可，例如有效期限。 如果您的信用卡號碼因為卡片遺失、遭竊或過期而變更，請遵循[將信用卡新增為付款方式](#addcard)一節中的步驟。 您不需要更新 CVV。

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]。
    ![顯示搜尋的螢幕擷取畫面](./media/change-credit-card/search.png)
1. 選取 [ **付款方式** ]。
    ![顯示已選取 [管理付款方式] 選項的螢幕擷取畫面](./media/change-credit-card/payment-methods-blade-x.png)
1. 選取您要編輯的信用卡。 信用卡表單會出現在右側。
    ![顯示已選取信用卡的螢幕擷取畫面](./media/change-credit-card/edit-card-x.png)
1. 更新信用卡詳細資料。
1. 選取 [儲存]。

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>管理 Microsoft 客戶合約的信用卡

下列各節適用於擁有 Microsoft 客戶合約並已使用信用卡註冊線上 Azure 的客戶。 [了解如何確認您是否有 Microsoft 客戶合約](#check-the-type-of-your-account)。

### <a name="change-default-credit-card"></a>變更預設信用卡

如果您有 Microsoft 客戶合約，您的信用卡會與帳單設定檔相關聯。 若要變更帳單設定檔的付款方式，您必須是已註冊 Azure 並建立計費帳戶的人員。

如果您想要將帳單設定檔的預設付款方式變更為支票/電匯，請參閱[按發票支付 Azure 訂用帳戶的費用](pay-by-invoice.md)。

若要變更您的信用卡，請按照下列步驟執行：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單] 進行搜尋。
1. 在左側功能表中，選取 [帳單設定檔]。
1. 選取帳單設定檔。
1. 在左側功能表中，選取 [付款方式]。  
   ![在功能表中顯示付款方式的螢幕擷取畫面](./media/change-credit-card/payment-methods-tab-mca.png)
1. 在 [預設付款方式] 區段中，選取 [取代]。  
    :::image type="content" source="./media/change-credit-card/change-payment-method-mca.png" alt-text="顯示取代選項的螢幕擷取畫面" :::
1. 在右側的新區域中，從下拉式選單選取現有的卡片，或選取藍色的 [新增付款方式] 連結來新增信用卡。

### <a name="edit-a-credit-card"></a>編輯信用卡

您可以在 Azure 入口網站中編輯信用卡詳細資料 (例如更新到期日)。 

若要編輯信用卡，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單] 進行搜尋。
1. 在左側功能表中，選取 [帳單設定檔]。
1. 選取帳單設定檔。
1. 在左側功能表中，選取 [付款方式]。  
   ![在功能表中顯示付款方式的螢幕擷取畫面](./media/change-credit-card/payment-methods-tab-mca.png)
1. 在 [您的信用卡] 區段中，尋找想要編輯的信用卡。
1. 選取資料列結尾處的省略符號 (`...`)。  
    :::image type="content" source="./media/change-credit-card/edit-delete-credit-card-mca.png" alt-text="顯示省略符號的螢幕擷取畫面" :::
1. 若要編輯您的信用卡詳細資料，請從捷徑功能表中選取 [編輯]。

## <a name="troubleshooting"></a>疑難排解

Azure 不支援虛擬或預付卡。 如果您在新增或更新有效信用卡時發生錯誤，請嘗試以私人模式開啟瀏覽器。

## <a name="frequently-asked-questions"></a>常見問題集

下列各節回答有關變更信用卡資訊的常見問題。

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>為什麼我一直收到「您的登入工作階段已過期。 請按一下這裡重新登入」錯誤訊息？

如果您已登出並重新登入，但還是一直收到此錯誤訊息，請用私密瀏覽工作階段再試一次。

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>如何針對我的每個訂閱使用不同的信用卡/轉帳卡？

很抱歉，如果您已經為所有的訂閱設定使用相同的信用卡/轉帳卡，就不能改用其他信用卡。 不過，註冊新訂閱時，可以為該訂閱選擇新的付款方式。

### <a name="how-do-i-make-payments"></a>如何付款？

如果您將信用卡設定為付款方式，我們便會在每次計費期間結束後自動向您的信用卡/轉帳卡扣款。 您不需要進行任何動作。

如果您使用[按發票付款](pay-by-invoice.md)，請將您的款項寄到發票底部所列的地址。

### <a name="how-do-i-change-the-tax-id"></a>如何變更統一編號？

若要新增或更新統一編號，請在 [Azure 入口網站](https://portal.azure.com)更新您的設定檔，然後選取 [稅務記錄]。 此統一編號會用於計算免稅金額，並出現在您的發票上。

## <a name="check-the-type-of-your-account"></a>檢查帳戶的類型

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 保留](../reservations/save-compute-costs-reservations.md)，以查看是否能為您節省費用。
- 如果您想要刪除信用卡，請參閱[刪除 Azure 帳單付款方法](delete-azure-payment-method.md)。