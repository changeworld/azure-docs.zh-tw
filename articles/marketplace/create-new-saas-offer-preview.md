---
title: 如何在 Microsoft 商業市場中為您的 SaaS 供應專案新增預覽物件
description: 如何在 Microsoft 合作夥伴中心中為您的軟體即服務 (SaaS) 供應專案新增預覽物件。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: ed0c7ef98e70774350c9a3ff12b0cc3f4186e1bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380780"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>如何為您的 SaaS 供應專案新增預覽物件

當您在合作夥伴中心中建立軟體即服務 (SaaS) 供應專案時，您需要定義預覽物件，以便在上線之前先審核您的供應專案清單。 本文說明如何設定預覽物件。

> [!NOTE]
> 如果您選擇單獨處理交易，將不會看到此選項。 相反地，請跳至 [如何行銷 SaaS 供應](create-new-saas-offer-marketing.md)專案。

## <a name="define-a-preview-audience"></a>定義預覽物件

在 [ **預覽物件** ] 索引標籤中，您可以定義有限的物件，讓他們可以在發佈至更廣大的 marketplace 物件之前，先審核您的 SaaS 供應專案。 您可以將邀請傳送給 Microsoft 帳戶 (MSA) 或 Azure Active Directory (Azure AD) 電子郵件地址。 手動新增最少一個和最多10個電子郵件地址，或匯入最多20個副檔名為 .csv 的電子郵件地址。 您可以隨時更新預覽物件清單。

> [!NOTE]
> 預覽對象與私人對象不同。 預覽物件可以存取您的供應專案，然後才會在線上商店上線。 您也可以選擇建立方案，僅供應給私人對象。 私人方案會在 [如何為您的 SaaS 供應專案建立方案](create-new-saas-offer-plans.md)中加以說明。

### <a name="add-email-addresses-manually"></a>手動新增電子郵件地址

1. 在 [ **預覽物件** ] 頁面上，于提供的方塊中新增單一 AZURE AD 或 MSA 電子郵件地址和選擇性描述。
1. 若要新增其他電子郵件地址，請選取 [ **新增其他電子郵件** ] 連結。
1. 選取 [ **儲存草稿** ]，然後繼續進行下一個索引標籤： [ **技術**設定]。
1. 繼續 [瞭解如何為您的 SaaS 供應專案新增技術詳細資料](create-new-saas-offer-technical.md)。

### <a name="add-email-addresses-using-the-csv-file"></a>使用 CSV 檔案新增電子郵件地址

1. 在 [ **預覽物件** ] 頁面上，選取 [ **匯出物件 (csv]) ** 連結。
1. 開啟。應用程式中的 CSV 檔案，例如 Microsoft Excel。
1. 在。CSV 檔案，在 [ **識別碼** ] 欄中，輸入您想要新增至預覽物件的電子郵件地址。
1. 在 [ **描述** ] 欄中，您可以選擇性地新增每個電子郵件地址的描述。
1. 在 [ **類型** ] 資料行中，將 **MixedAadMsaId** 加入至具有電子郵件地址的每個資料列。
1. 將檔案另存為。CSV 檔案。
1. 在 [ **預覽物件** ] 頁面上，選取 [匯 **入物件 (csv]) ** 連結。
1. 在 [ **確認** ] 對話方塊中，選取 [ **是]**。
1. 選取。CSV 檔案，然後選取 [ **開啟**]。
1. 選取 [ **儲存草稿** ]，然後繼續進行下一個索引標籤： [ **技術**設定]。

## <a name="next-steps"></a>後續步驟

- [如何為您的 SaaS 供應專案新增技術詳細資料](create-new-saas-offer-technical.md)
