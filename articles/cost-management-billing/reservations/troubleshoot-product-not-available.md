---
title: 針對無法使用 Azure 保留類型進行疑難排解
description: 本文可協助您了解在 Azure 入口網站中顯示為未提供的保留執行個體並進行疑難排解。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 8575d9d86d8e720122a295cf92fa571ef33d5b4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92798179"
---
# <a name="troubleshoot-reservation-type-not-available"></a>針對無法使用保留類型進行疑難排解

本文可協助您了解在 Azure 入口網站中顯示為未提供的保留執行個體並進行疑難排解。

## <a name="symptoms"></a>徵兆

1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至 [保留]。
2. 選取 [+新增]，然後選取某個產品。
3. 選取 **所有產品** 索引標籤。
4. 在產品清單中選取一個。 您可能看見下列其中一個訊息：
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="顯示所選訂用帳戶或區域訊息無法使用之產品的範例" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="顯示沒有足夠核心配額訊息的範例" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>原因

無法購買某些保留，因為：

- 並非每個區域都能購買每個保留執行個體產品
- 您的訂用帳戶有配額限制

### <a name="cause-1"></a>原因 1

並非所有保留執行個體產品都可供購買。 Azure 決定依據與提供金額折扣給客戶相關聯的成本，允許或不允許某些產品。 在其他情況下，Azure 不會針對特定資料中心的容量條件提供部分產品。 此外，某些 Azure 產品開發小組現在可能會允許某些產品，因為他們想淘汰舊版產品。

在其他情況下，Azure 會根據這些資源在某些區域中的需求，為各種產品提供容量限制。 例如，若有在資料中心內執行特定 VM 大小的需求時，可能會發生這種限制。 在此範例中，Azure 無法保證在該區域中購買該保留大小的客戶所需的容量。 最後，有各種原因讓某些產品較為特殊。 這類產品僅適用於一組小型客戶。

為什麼 Azure 入口網站中會顯示無法購買的某些保留？ 因為使用者會建立支援要求，指出他們找不到想要的產品。 Azure 保留開發小組決定顯示具有 `Product unavailable` 訊息的所有產品，以減少提出的支援要求。

### <a name="cause-2"></a>原因 2

您的訂用帳戶有配額限制。 訂用帳戶對於可以取用的 CPU 核心數目有限制。 此限制適用於某些保留執行個體產品，尤其是虛擬機器。 Azure 想確保購買保留執行個體的人員可以充份運用。 Azure 會在 Azure 入口網站中進行簡單、約略的檢查，以確定您的訂用帳戶中有足夠的免費核心可部署 VM，並享有保留購買權益。

可讓您將特定產品新增至購物車並購買保留的檢查很簡單。 Azure 會評估您訂用帳戶可用的 CPU 核心總數，並檢查該數目是否大於所選項目的核心數目。

Azure 不會檢查 **共用** 範圍保留執行個體的配額。 共用範圍的保留執行個體權益適用於註冊中的所有訂用帳戶。 Azure 無法判斷您的所有訂用帳戶中是否有足夠的免費核心可部署資源。 無論配額為何，Azure 一律會在選取的範圍為共用時讓您選取 VM 大小。

此外，Azure 不會對 **建議的** 購買進行配額檢查。 建議是以作用中的使用量為基礎。 Azure 假設您有足夠的核心可執行特定的 VM 大小，因為您已經產生了建立建議所需的使用量。

## <a name="solution"></a>解決方法

根據您收到的錯誤訊息，使用下列其中一個解決方案來解決您的問題。

### <a name="solution-1"></a>解決方案 1

如果您看到「無法使用產品」訊息，請在錯誤訊息中選取 **連絡人支援** 連結，以要求為您的訂用帳戶新增例外狀況。 不一定會發生例外狀況。

### <a name="solution-2"></a>解決方案 2

如果您看到「沒有足夠的核心配額」訊息，可以將範圍變更為 **共用** 。 購買保留之後，您可以將保留範圍從 **共用** 變更為 **單一** 。

或者，選取錯誤訊息中的 **要求增加配額** 連結，以要求額外的訂用帳戶 CPU 核心配額。

## <a name="next-steps"></a>下一步

- 如需保留範圍選項的詳細資訊，請參閱[保留範圍](prepare-buy-reservation.md#scope-reservations)。