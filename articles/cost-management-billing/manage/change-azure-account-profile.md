---
title: 變更 Azure 帳單帳戶的連絡人資訊
description: 說明如何變更 Azure 帳單帳戶的連絡人資訊
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperfq2
ms.openlocfilehash: 05b93fd02ec229306d4e3e150b1e8baab072b597
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92671991"
---
# <a name="change-contact-information-for-an-azure-billing-account"></a>變更 Azure 帳單帳戶的連絡人資訊

本文可協助您在 Azure 入口網站中更新「帳單帳戶」的連絡人資訊。 更新連絡人資訊的指示會因計費帳戶類型而異。 若要深入了解計費帳戶及識別您的計費帳戶類型，請參閱[在 Azure 入口網站中檢視計費帳戶](view-all-accounts.md)。 Azure 計費帳戶與您的 Azure 使用者帳戶和 [Microsoft 帳戶](https://account.microsoft.com/)不同。

如果您想要更新 Azure Active Directory 的使用者設定檔資訊，只有使用者系統管理員可以進行變更。 如果您未獲指派使用者系統管理員角色，請洽詢您的使用者系統管理員。 如需有關變更使用者設定檔的詳細資訊，請參閱[使用 Azure Active Directory 新增或更新使用者的設定檔資訊](../../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)。

*銷售對象地址* - 銷售對象地址是組織或個人 (負責管理計費帳戶) 的地址和連絡人資訊。 其會顯示在計費帳戶所產生的所有發票中。

*帳單寄送地址* - 帳單寄送地址是組織或個人 (負責管理計費帳戶所產生的發票) 的地址和連絡人資訊。 Microsoft 線上服務方案 (MOSP) 的計費帳戶會有一個帳單寄送地址，此地址會顯示在為帳戶產生的所有發票上。 針對 Microsoft 客戶合約 (MCA) 的計費帳戶，每個帳單設定檔都會有一個帳單寄送地址，而且會顯示在為帳單設定檔產生的發票中。

*服務和行銷電子郵件的連絡人電子郵件地址* - 您可以指定與您用來登入的電子郵件地址不同的電子郵件地址，以接收關於 Azure 帳戶的重要帳單、服務和建議相關通知。 服務通知電子郵件 (例如緊急安全性問題、價格變更，或您的帳戶所使用之服務的重大變更) 一律會傳送至您的登入位址。

## <a name="update-an-mosp-billing-account-address"></a>更新 MOSP 計費帳戶地址

1. 使用具有帳戶上帳戶管理員權限的電子郵件地址來登入 Azure 入口網站。
1. 搜尋 [成本管理 + 帳單]。  
    ![顯示在 Azure 入口網站中何處搜尋 [成本管理 + 計費] 的螢幕擷取畫面](./media/change-azure-account-profile/search-cmb.png)
1. 從左側選取 [屬性]。  
    ![顯示 MOSP 帳單帳戶屬性的螢幕擷取畫面](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. 選取 [更新帳單地址] 以更新銷售對象地址和帳單寄送地址。 輸入新地址，然後選取 [儲存]。  
    ![顯示 MOSP 帳單帳戶更新地址的螢幕擷取畫面](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>更新 MCA 計費帳戶的銷售對象地址

1. 使用在 Microsoft 客戶合約的計費帳戶上具有擁有者或參與者角色的電子郵件地址來登入 Azure 入口網站。
1. 搜尋 [成本管理 + 帳單]。  
    ![顯示在 Azure 入口網站中何處搜尋的螢幕擷取畫面](./media/change-azure-account-profile/search-cmb.png)
1. 從左側選取 [屬性]，然後選取 [更新銷售對象]。  
    ![顯示 MCA 計費帳戶屬性中可以修改銷售地址的螢幕擷取畫面](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. 輸入新地址，然後選取 [儲存]。  
    ![顯示更新 MCA 帳戶銷售地址的螢幕擷取畫面](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > 某些帳戶需要先進行額外驗證才能更新其銷售對象。 如果您的帳戶需要手動核准，系統會要求您連絡 Azure 支援。

## <a name="update-an-mca-billing-account-address"></a>更新 MCA 計費帳戶地址

1. 使用在 MCA 的計費帳戶或帳單設定檔上具有擁有者或參與者角色的電子郵件地址來登入 Azure 入口網站。
1. 搜尋 [成本管理 + 帳單]。  
1. 從左側選取 [帳單設定檔]。
1. 選取要更新帳單地址的帳單設定檔。  
    ![顯示您可以在其中選取帳單設定檔的 [帳單] 設定檔頁面螢幕擷取畫面](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. 從左側選取 [屬性]。
1. 選取 [更新地址]。  
    ![顯示在何處更新地址的螢幕擷取畫面](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. 輸入新地址，然後選取 [儲存]。  
    ![顯示更新地址的螢幕擷取畫面](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="service-and-marketing-emails"></a>服務與行銷電子郵件

系統會提示您在 Azure 入口網站中，每隔 90 天驗證或更新您的電子郵件地址。 Microsoft 會將電子郵件傳送至此電子郵件地址，內含 Azure 帳戶相關資訊：

- 服務通知
- 安全性警示
- 計費目的
- 支援
- 行銷公關
- 最佳做法建議 (根據您的 Azure 使用量)

輸入您想要接收帳戶相關通訊的電子郵件地址。 藉由輸入電子郵件地址，您可選擇接收來自 Microsoft 的通訊。

![更新連絡人資訊的提示範例](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>變更連絡人電子郵件地址

您可以使用下列其中一種方法來變更您的連絡人電子郵件地址。 更新連絡人電子郵件地址並不會更新您用來登入的電子郵件地址。

1. 如果您是 MOSP 帳戶的帳戶管理員，請遵循[更新 MOSP 計費帳戶地址](#update-an-mosp-billing-account-address)中的指示，然後在最後一個步驟中選取 [更新連絡人資訊]。 接下來，輸入新的電子郵件地址。
1. 移至 Azure 入口網站中的[連絡人資訊](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade)區域，然後輸入新的電子郵件地址。 
1. 在 Azure 入口網站中，選取具有您的姓名縮寫或圖片的圖示。 然後，選取快顯功能表 ( **...** )。接下來，從功能表中選取 [我的連絡人資訊]，然後輸入新的電子郵件地址。

![在 Azure 中更新電子郵件地址的範例](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>退出行銷電子郵件

若不想要接收行銷電子郵件：

1. 前往[要求表單](https://account.microsoft.com/profile/permissions-link-request)，使用您的設定檔電子郵件地址提交要求。 您會經由電子郵件收到連結，以更新您的喜好設定。
1. 選取該連結以開啟 [管理通訊權限] 頁面。 此頁面會顯示電子郵件地址所加入的行銷通訊類型。 清除您想要退出的任何選取項目，然後選取 [儲存]。  
    ![用於管理通訊權限的頁面範例](./media/change-azure-account-profile/manage-communication-permissions.png)

當您選擇退出行銷通訊時，您仍會收到服務通知 (根據您的帳戶)。

## <a name="update-the-email-address-that-you-sign-in-with"></a>更新您用來登入的電子郵件地址

您無法更新用來存取帳戶的電子郵件地址。 不過，如果您有 MOSP 的計費帳戶，則可以使用新的電子郵件地址註冊另一個帳戶，並將訂用帳戶的擁有權轉移到下一個帳戶。 針對 MCA 計費帳戶，您可以為新的電子郵件地址提供帳戶上的權限。

## <a name="update-your-credit-card"></a>更新您的信用卡

若要了解如何更新信用卡，請參閱[變更用於支付 Azure 訂用帳戶的信用卡](change-credit-card.md)。

## <a name="update-your-country-or-region"></a>更新您的國家或地區

不支援變更現有帳戶的國家或地區。 不過，您可以在不同的國家或地區建立新的帳戶，然後連絡 Azure 支援將您的訂用帳戶轉移至新的帳戶。

## <a name="change-the-subscription-name"></a>變更訂用帳戶名稱

1. 登入 Azure 入口網站，從左窗格中選取 [訂用帳戶]，然後選取您要重新命名的訂用帳戶。
1. 選取 [概觀]，然後從命令列選取 [重新命名]。  
    ![重新命名 Azure 訂用帳戶的範例](./media/change-azure-account-profile/rename-sub.png)
1. 在變更名稱後，請選取 [儲存]。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [檢視您的計費帳戶](view-all-accounts.md)
