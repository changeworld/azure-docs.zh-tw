---
title: 自動更新 Azure 保留
description: 了解您可以如何自動更新 Azure 保留，以繼續獲得保留折扣。
services: billing
author: bandersmsft
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: dfff02c554c94daf1dd7fd844cea461fd76589d8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "75995711"
---
# <a name="automatically-renew-reservations"></a>自動更新保留

您可以更新保留，使其在現有保留到期時自動購買替代保留。 自動更新提供了簡單的方式讓您繼續獲得保留折扣。 其也能讓您不必密切監控保留的到期時間。 透過自動更新，您即可因為不需要手動更新，而避免損失節省效益。 系統預設會關閉更新設定。 在現有保留到期前，您隨時都可以啟用或停用更新設定。

更新保留會在現有保留到期時建立新的保留。 其不會延伸現有保留的期限。

您隨時都可以選擇加入自動更新功能。 在現有保留到期日前的 30 天內，可享有更新價格。 若您在保留到期日還剩超過 30 天時啟用更新，系統會傳送一封電子郵件，詳述到期日前 30 天內的更新成本。 在您鎖定更新價格時和實際進行更新時的這一段期間，保留價格可能會變更。 若是如此，您的更新成本會是這兩個成本較低者。 您可以對保留數量進行變更。 如果您這樣做，更新便會改為使用在變更數量時，所設定的市場價格。

您沒有更新義務，而且在現有保留到期前，隨時都可以選擇退出更新。

## <a name="set-up-renewal"></a>設定更新

移至 Azure 入口網站 > [保留]  。

1. 選取保留。
2. 按一下 [更新]  。
3. 選取 [在到期時自動購買新的保留]  。  
  ![顯示保留更新的範例](./media/reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>如果您未更新

您的服務會繼續正常執行。 在保留到期後，您就必須以隨用隨付費率來支付使用費。

## <a name="required-renewal-permissions"></a>所需的更新權限

若要更新保留，就必須符合下列條件：

- 您必須是現有保留的擁有者。
- 如果保留的範圍設定為單一訂用帳戶或資源群組，則您必須是訂用帳戶的擁有者。
- 如果訂用帳戶具有共用範圍，則您必須是該訂用帳戶的擁有者。

## <a name="default-renewal-settings"></a>預設的更新設定

根據預設，更新會從即將到期的保留繼承所有屬性。 為了更新所購買的保留會有相同的 SKU、區域、範圍、計費訂用帳戶、期限和數量。

不過，您可以更新所購買的更新保留數量，以獲得最佳節省效果。

## <a name="when-the-new-reservation-is-purchased"></a>購買新的保留時

當現有保留到期時，系統就會購買新的保留。 我們會設法讓兩個保留之間沒有任何延遲。 保持連貫可確保您可以預測成本，並繼續獲得折扣。

## <a name="changing-parent-reservation-after-setting-renewal"></a>在設定更新後變更父代保留

如果您對即將到期的保留進行下列任何變更，保留更新將會取消：

- Split
- 合併
- 將保留從某個帳戶轉移至另一個帳戶
- 將保留從 WebDirect 訂用帳戶轉移至 Enterprise 合約 (EA) 訂用帳戶或任何其他購買方法
- 更新註冊

新的保留會在更新期間從即將到期的保留繼承範圍和執行個體大小彈性設定。

## <a name="new-reservation-permissions"></a>新的保留權限

Azure 會將權限從即將到期的保留複製到新的保留。 此外，用來購買保留的訂用帳戶管理員會有新保留的存取權。

## <a name="potential-renewal-problems"></a>可能的更新問題

有下列情況時，Azure 可能不會處理更新：

- 無法收取款項
- 更新期間發生系統錯誤
- 即將到期的 SKU 未在更新期間處於使用中狀態
- EA 更新為不同的 EA

如果發生上述任何一種情況，且更新停用，您就會收到電子郵件通知。

## <a name="renewal-notification"></a>更新通知

根據您的購買方法，系統會將電子郵件傳送給不同人員：

- EA 客戶 - 電子郵件會傳送到 EA 入口網站上所設定的通知連絡人。
- 使用隨用隨付費率的個別訂用帳戶客戶 - 電子郵件會傳送到設定為帳戶管理員的使用者。
- 雲端解決方案提供者客戶 - 電子郵件會傳送到合作夥伴通知連絡人。

## <a name="next-steps"></a>後續步驟
- 若要深入了解 Azure 保留，請參閱[什麼是 Azure 保留？](save-compute-costs-reservations.md)
