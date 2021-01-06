---
title: 如何為您的受控服務供應專案新增預覽物件
description: 瞭解如何在 Microsoft 合作夥伴中心中為您的受控服務供應專案新增預覽物件。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 072f1d63e0b3df898ef170205b5c560432d870b0
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918164"
---
# <a name="how-to-add-a-preview-audience-for-your-managed-service-offer"></a>如何為您的受控服務供應專案新增預覽物件

本文說明如何使用合作夥伴中心在商業 marketplace 中設定受管理服務供應專案的預覽物件。 預覽物件可以在您的供應專案上線前進行檢查。

## <a name="define-a-preview-audience"></a>定義預覽物件

在 [ **預覽物件** ] 頁面上，您可以定義有限的物件，讓他們能夠在您將受管理的服務供應專案即時發佈給更廣大的 marketplace 物件之前，先進行審核。 您可以使用 Azure 訂用帳戶 Id 來定義預覽物件，以及各自的選擇性描述。 客戶無法看到這些欄位。 您可以在 Azure 入口網站的 [訂用帳戶 **] 頁面上找到您的 Azure** 訂用帳戶識別碼。

至少新增一個 Azure 訂用帳戶識別碼，其可個別 (最多10個) 或上傳 CSV 檔案 (最多100個) ，以定義可以在發佈之前預覽您供應專案的人員。 如果您的供應專案已上線，您仍然可以定義預覽物件來測試您的供應專案更新。

> [!NOTE]
> *預覽* 物件與 *私* 用物件不同。 預覽物件可以存取您的供應專案，然後才會在線上商店上線。 他們可以查看並驗證所有方案，包括您的供應專案在 marketplace 中完整發佈之後，才會提供給私用物件。 您只能將方案提供給私用物件。 私用物件 (定義于方案的 [可用性] 索引標籤中，) 具有特定方案的獨佔存取權。

## <a name="add-email-addresses-manually"></a>手動新增電子郵件地址

1. 在 [ **預覽物件** ] 頁面上，于提供的方塊中新增單一 Azure 訂用帳戶識別碼和選擇性描述。
2. 若要新增其他電子郵件地址，請選取 [ **新增識別碼 (最大 10)** ] 連結。
3. 選取 [ **儲存草稿** ]，然後繼續進行下一個索引標籤。

## <a name="add-email-addresses-using-a-csv-file"></a>使用 CSV 檔案新增電子郵件地址

1. 在 [預覽物件 **預覽物件** ] 頁面上，選取 [ **匯出物件 (csv])** 連結。
2. 開啟 CSV 檔案。 在 [ **識別碼** ] 欄中，輸入您想要新增至預覽物件的 Azure 訂用帳戶識別碼。
3. 在 [ **描述** ] 欄中，您可以選擇新增每個專案的描述。
4. 在 [ **類型** ] 資料行中，將 [ **SubscriptionId** ] 加入至每個具有識別碼的資料列。
5. 將檔案儲存為 CSV 檔案。
6. 在 [ **預覽物件** ] 頁面上，選取 [匯 **入物件 (csv])** 連結。
7. 在 [ **確認** ] 對話方塊中，選取 [ **是]**，然後上傳 CSV 檔案。
8. 選取 [ **儲存草稿** ]，然後繼續進行下一個索引標籤。

## <a name="next-steps"></a>後續步驟

* [建立方案](create-managed-service-offer-plans.md)
