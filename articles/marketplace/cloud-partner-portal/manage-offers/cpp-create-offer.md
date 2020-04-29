---
title: 建立 Marketplace 供應專案 |Azure Marketplace
description: 使用 Cloud Partner 入口網站建立 Azure 和 AppSource Marketplace 上的供應項目
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: dsindona
ms.openlocfilehash: d14940eff569a80759e9db0bfa2d6d1541a01377
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80286467"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>建立 Azure Marketplace 和 AppSource 的供應項目

Cloud Partner 入口網站的一個基本目的是使發行者能夠建立 (並發行) Microsoft Azure 和 AppSource Marketplace 的供應項目。  這項作業始終從[新增供應項目功能表](../portal-tour/cpp-new-offer-menu.md)中選取所需的供應項目類型開始。  在回應中，將顯示該供應項目類型的適當 [新增供應項目]**** 頁面。  例如，下圖顯示 Azure 應用程式類型的預設 [新增供應項目]**** 頁面。

![[新增供應項目] 預設頁面](./media/new-offer-page.png)

在此頁面頂端顯示的水平功能表列中有兩個索引標籤選項： 
- [編輯器]**** 索引標籤 - 允許為新的供應項目執行個體輸入資訊和上載資產。  根據預設，會顯示此索引標籤。
- [狀態]**** 索引標籤 - 提供發佈狀態，並列出任何驗證和檢閱問題。 

當您建立供應項目時，您可以使用 [編輯器]**** 索引標籤來輸入該供應項目的相關資訊。 

## <a name="editing-operations"></a>編輯作業

位於資料輸入區域上方的水平工具列中會顯示下列按鈕：

|   按鈕    |   目的                                                          |
|   ------    |  --------                                                          |
| **儲存**    | 儲存任何最近的資料輸入變更。  您必須在離開該頁面之前以手動方式儲存變更，否則您的變更將會遺失。 | 
| **捨棄** | 捨棄最近的資料輸入變更 (自上次儲存以來)             |
| **比較** | 將目前的供應項目與已發佈的供應項目的狀態進行比較。  僅在成功發佈供應項目之後啟用。  |
| **Publish** | 開始此供應項目的發佈程序                       |
| **刪除**  | 請在建立之後但在發佈之前刪除此供應項目。 |
|   |   |


## <a name="editing-tabs"></a>[編輯] 索引標籤

建立供應項目時，您可以在 [新增供應項目]**** 頁面左側垂直資料行中的每個索引標籤中提供必要和選擇性資料。  標準使用者介面控制項會顯示 (例如文字方塊、下拉式清單和核取方塊) 以進行資料收集。  雖然 [編輯] 索引標籤的特定集合是根據供應項目類型而定，但下表列出了一些常見的索引標籤。

|      索引標籤名稱       |   目的                                                            |
|      --------       |   -------                                                            |
| **供應專案設定**  | 收集供應項目和發行者身分識別資訊。                    |
| **SKU**            | 定義供應項目的每個庫存單位 (SKU) 版本的技術和商務特性 |
| **試用產品**      | 針對支援此選擇性功能的類型，請為您的供應項目定義示範。  如需詳細資訊，請參閱[什麼是試用產品？](../test-drive/what-is-test-drive.md)  |
| **市集**或**店面** | 收集用來列出市集中的供應項目的文字字串、文件和影像 |
| **支援**         | 收集客戶、工程和線上支援的連絡資訊  |
|  |  |

類似命名之索引標籤的內容，可能因不同的供應項目類型而不同。  每個供應項目類型的「建立供應項目」部分都提供了這些索引標籤的供應項目專屬的詳細資料。


## <a name="next-steps"></a>後續步驟

建立及儲存供應項目之後，以及在發佈供應項目之前或之後，您可以[檢視其狀態](./cpp-view-status-offer.md)。
