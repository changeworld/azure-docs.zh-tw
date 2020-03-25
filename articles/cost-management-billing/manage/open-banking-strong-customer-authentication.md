---
title: 適用於 Azure 客戶的開放銀行 (PSD2) 和強大客戶驗證 (SCA)
description: 本文說明為何某些 Azure 購買需要多重要素驗證，以及如何完成驗證。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: banders
ms.openlocfilehash: 1c4522bed191ef4142cc603bf0e1d22f086111ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200583"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>適用於 Azure 客戶的開放銀行 (PSD2) 和強大客戶驗證 (SCA)

從 2019 年 9 月 14 日起，[歐洲經濟區域](https://en.wikipedia.org/wiki/European_Economic_Area) 31 個國家/地區的銀行必須在處理付款之前先驗證進行線上購買的人員身分識別。 此驗證需要多重要素驗證，協助您確保您的線上購買安全無虞且受到保護。 在某些國家/地區，此驗證需求的日期會延遲。 如需詳細資訊，請參閱 [Microsoft PSD2 常見問題集](https://support.microsoft.com/en-us/help/4517854?preview)。

## <a name="what-psd2-means-for-azure-customers"></a>PSD2 對於 Azure 客戶的意義

如果您使用[歐洲經濟區域](https://en.wikipedia.org/wiki/European_Economic_Area)的銀行所核發的信用卡支付 Azure 費用，您可能需要針對您帳戶的付款方式完成多重要素驗證。 當您註冊 Azure 帳戶或升級 Azure 帳戶時，系統可能會提示您完成多重要素驗證挑戰，即使您當時未進行購買也一樣。 當您變更 Azure 帳戶的付款方式、移除您的消費上限，或從 Azure 入口網站進行立即付款 (例如，結清未付餘額或購買 Azure 點數) 時，系統也可能會要求您提供多重要素驗證。

如果您的銀行拒絕您的每月 Azure 費用，您將會收到來自 Azure 的逾期電子郵件，內含修正此問題的指示。 您可以完成多重要素驗證挑戰，並在 Azure 入口網站中結清未付費用。

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>在 Azure 入口網站中完成多重要素驗證

下列各節說明如何在 Azure 入口網站中完成多重要素驗證。 請使用您適用的資訊。

### <a name="change-the-active-payment-method-of-your-azure-account"></a>變更 Azure 帳戶的有效付款方式

您可以遵循下列步驟，變更 Azure 帳戶的有效付款方式：

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 [成本管理 + 帳單]  。
2. 在 [概觀]  頁面中，從 [我的訂用帳戶]  方格中選取對應的訂用帳戶。
3. 在 [帳單] 下方選取 [付款方式]  。 您可以新增信用卡，或將現有卡片設為訂用帳戶的有效付款方式。 如果您的銀行需要多重要素驗證，系統會提示您在處理過程中完成驗證挑戰。

如需詳細資訊，請參閱[新增、更新或移除用於 Azure 的信用卡](change-credit-card.md)。

### <a name="settle-outstanding-charges-for-azure-services"></a>結清 Azure 服務的未付費用

如果您的銀行拒絕費用，則您的 Azure 帳戶狀態將會在 Azure 入口網站中變成 [逾期]  。 您可以遵循下列步驟來檢查您的帳戶狀態：

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 針對 [成本管理 + 帳單]  進行搜尋。
3. 在 [成本管理 + 帳單]  的 [概觀]  頁面上，檢閱 [我的訂用帳戶]  方格中的 [狀態] 資料行。
4. 如果您的訂用帳戶標示為 [逾期]  ，請按一下 [結清餘額]  。 系統會提示您在處理過程中完成多重要素驗證。

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>結清 Marketplace 和保留購買的未付費用

Marketplace 和保留購買會與 Azure 服務分開計費。 如果您的銀行拒絕 Marketplace 或保留費用，您的發票將會變成逾期狀態，而您會在 Azure 入口網站中看到 [立即付費]  的選項。 您可以遵循下列步驟，為逾期的 Marketplace 和保留發票付費：

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 針對 [成本管理 + 帳單]  進行搜尋。
3. 在 [帳單] 底下，選取 [發票]  。
5. 在 [訂用帳戶] 下拉式篩選條件中，選取與 Marketplace 或保留購買相關聯的訂用帳戶。
6. 在 [發票] 方格中，檢閱 [類型] 資料行。 如果類型是 [Azure Marketplace 和保留]  ，則當發票到期或逾期時，您將會看到 [立即付費]  連結。 如果您沒有看到 [立即付費]  ，則表示您的發票已完成付費。 在立即付費期間，系統會提示您完成多重要素驗證。

## <a name="next-steps"></a>後續步驟
- 如果您需要支付 Azure 帳單，請參閱[處理 Azure 訂用帳戶的逾期未付帳款](resolve-past-due-balance.md)。
