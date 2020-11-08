---
title: 如何為您的 Azure 應用程式供應專案新增預覽物件
description: 瞭解如何在合作夥伴中心中為您的 Azure 應用程式供應專案新增預覽物件。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370000"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>如何為您的 Azure 應用程式供應專案新增預覽物件

本文說明如何在商業市場中設定 Azure 應用程式供應專案的預覽物件。 您需要定義預覽物件，以便在上線之前先審核您的供應專案清單。

## <a name="define-a-preview-audience"></a>定義預覽物件

在 [ **預覽物件** ] 頁面上，您可以定義有限的物件，讓他們可以複習您的 Azure 應用程式供應專案，然後再將它即時發佈給更廣大的 marketplace 物件。 您可以使用 Azure 訂用帳戶 Id 來定義預覽物件，以及各自的選擇性描述。 客戶無法看到這些欄位。 您可以在 Azure 入口網站的 [訂用帳戶] 頁面上找到 Azure 訂用帳戶識別碼。

新增最少一個和最多10個 Azure 訂用帳戶識別碼，其可個別 (最多10個) 或上傳 CSV 檔案 (最多100個) ，以定義可以在發佈之前預覽您供應專案的人員。 如果您的供應項目已上線，您仍然可以定義預覽對象，以測試您供應項目的變更或更新。

> [!NOTE]
> 預覽對象與私人對象不同。 預覽物件可以存取您的供應專案，然後才會在線上商店上線。 預覽對象可以查看並驗證所有方案，包括只有在您的供應項目完全發佈到 Marketplace 之後，才供私人對象使用的方案。 您只能將方案提供給私用物件。 私用物件 (定義于方案的 [ **可用性** ] 索引標籤中，) 具有特定方案的獨佔存取權。

### <a name="add-email-addresses-manually"></a>手動新增電子郵件地址

1. 在 [ **預覽物件** ] 頁面上，于提供的方塊中新增單一 Azure 訂用帳戶識別碼和選擇性描述。
1. 若要新增其他電子郵件地址，請選取 [ **新增識別碼 (最大 10)** ] 連結。
1. 選取 [ **儲存草稿** ]，然後繼續進行下一個索引標籤： [技術設定]。
1. 移至 [下一個步驟](#next-steps)。

### <a name="add-email-addresses-using-the-csv-file"></a>使用 CSV 檔案新增電子郵件地址

1. 在 [ **預覽物件** ] 頁面上，選取 [ **匯出物件 (csv])** 連結。
1. 開啟。應用程式中的 CSV 檔案，例如 Microsoft Excel。
1. 在。CSV 檔案，在 [ **識別碼** ] 欄中，輸入您想要新增至預覽物件的 Azure 訂用帳戶識別碼。
1. 在 [ **描述** ] 欄中，您可以選擇性地新增每個電子郵件地址的描述。
1. 在 [ **類型** ] 資料行中，將 [ **SubscriptionID** ] 加入至每個具有電子郵件地址的資料列。
1. 將檔案另存為。CSV 檔案。
1. 在 [ **預覽物件** ] 頁面上，選取 [匯 **入物件 (csv])** 連結。
1. 在 [ **確認** ] 對話方塊中，選取 [ **是]** 。
1. 選取。CSV 檔案，然後選取 [ **開啟** ]。
1. 選取 [ **儲存草稿** ]，然後繼續進行下一個索引標籤： [技術設定]。

## <a name="next-steps"></a>後續步驟

- [如何為您的 Azure 應用程式供應專案新增技術詳細資料](create-new-azure-apps-offer-technical.md)
