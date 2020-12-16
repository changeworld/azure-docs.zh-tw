---
title: 針對 Azure 入口網站中沒有符合資格的訂用帳戶一事進行疑難排解
description: 當您嘗試購買保留，但 Azure 入口網站中卻出現「沒有符合資格的訂用帳戶」錯誤訊息時，本文可協助您進行疑難排解。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.openlocfilehash: ad85bd278b5dff1532f218acc0b8e88515d96070
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561200"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>針對沒有符合資格的訂用帳戶一事進行疑難排解

當您嘗試購買保留，但 Azure 入口網站中卻出現「沒有符合資格的訂用帳戶」錯誤訊息時，本文可協助您進行疑難排解。

## <a name="symptoms"></a>徵兆

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 [保留]。
1. 選取 [新增]，然後選取某個服務。
1. 您看到下列「錯誤訊息」：
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. 在 [選取您要購買的產品] 區域中，展開 [計費訂用帳戶] 清單，以查看特定訂用帳戶沒有資格購買保留執行個體的原因。 下圖顯示為何無法購買保留的範例。  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="顯示為何無法購買保留的範例" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>原因

若要購買 Azure 保留執行個體，您必須至少有一個符合下列需求的訂用帳戶：

- 訂用帳戶必須是支援的供應項目類型。 支援的供應項目類型有：隨用隨付、雲端解決方案提供者 (CSP)、Microsoft Azure 企業版或 Microsoft 客戶合約。
- 您必須是訂用帳戶的擁有者或保留購買者。

當您沒有符合需求的訂用帳戶時，您會收到 `No eligible subscriptions` 錯誤。

### <a name="cause-1"></a>原因 1

訂用帳戶必須是支援的供應項目類型。 支援的供應項目類型有：隨用隨付、CSP、Microsoft Azure 企業版或 Microsoft 客戶合約。 您的訂用帳戶類型不受支援。 當您選取的訂用帳戶具有不支援保留的供應項目類型時，您會看到下列錯誤。

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="顯示訂用帳戶沒有資格購買錯誤訊息的範例" :::

### <a name="cause-2"></a>原因 2

您必須是訂用帳戶的擁有者或保留購買者。 當您沒有足夠的權限時，您會看到下列錯誤。

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>解決方法

- 如果您目前的供應項目不支援保留，則必須建立新的 Azure 訂用帳戶。
- 如果您沒有現有保留的存取權，則可以從目前的擁有者取得其存取權。

### <a name="solution-1"></a>解決方案 1

若要購買保留，您必須建立支援保留的新 Azure 訂用帳戶。

- 如果您有 Azure 免費試用版，則可以[升級訂用帳戶](../manage/upgrade-azure-subscription.md)。
- 您可以使用[隨用隨付費率](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)建立新的 Azure 訂用帳戶。
- 註冊 [Microsoft 客戶合約](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)並建立新的訂用帳戶。
- 使用 [CSP](https://www.microsoft.com/solution-providers/home) 購買新的訂用帳戶，並建立新的訂用帳戶。

### <a name="solution-2"></a>解決方案 2

若要取得保留的擁有者存取權，您必須取得下列任一項的存取權：

- 用來購買保留的保留訂單
- 保留本身

目前的保留訂單擁有者或保留擁有者可以使用下列步驟，將存取權委派給您。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [所有服務] >  [保留] 來列出您具有存取權的保留。
1. 選取您想要將存取權委派給其他使用者的保留。
1. 選取 [存取控制 (IAM)]。
1. 選取 [新增角色指派] > [角色] > [擁有者]。 或是，如果您想要給予有限的存取權，可選取不同角色。
1. 輸入要新增為擁有者之使用者的電子郵件地址。
1. 選取使用者，然後選取 [儲存]。

如需詳細資訊，請參閱[新增或變更可以管理保留的使用者](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default)。

## <a name="next-steps"></a>後續步驟

- 如果您需要讓保留擁有者授與存取權給您，請參閱[新增或變更可以管理保留的使用者](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default)。
