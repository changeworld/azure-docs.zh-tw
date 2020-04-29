---
title: 刪除 marketplace 供應專案 |Azure Marketplace
description: 使用 Cloud Partner 入口網站刪除 Azure 和 AppSource Marketplace 上的供應項目
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: dsindona
ms.openlocfilehash: fa8ee834bd6d9261c47c540e9517c200c894b6c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80286450"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>刪除 Azure Marketplace 和 AppSource 的供應項目或 SKU

基於各種原因，您可能會決定從 Microsoft 市集中撤消供應項目，而這可能有兩種形式：

- 「移除供應項目」** 可確保新的客戶無法再購買或部署您的供應項目，但是對現有客戶沒有任何影響，現有客戶指的是您必須根據授權合約和相關法規繼續支援的客戶。 
- *供應專案終止*是終止您與現有客戶之間之服務及/或授權合約的程式。 

供應專案移除及終止的相關指導方針和原則，受[Microsoft Marketplace 發行者合約](https://go.microsoft.com/fwlink/?LinkID=699560)和[參與原則](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)（[提供擱置和移除](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)的區段）所規範。 

本文件討論所支援的不同刪除案例，以及執行每個案例所需的步驟。  

> [!NOTE]
> 若要刪除尚未發佈的供應項目，只需在 [編輯器]**** 索引標籤的工具列中選取 [刪除]**** 按鈕即可。


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>從 Azure Marketplace 刪除已發佈的 SKU

您可以使用下列步驟從 Azure Marketplace 刪除已發佈的 SKU︰

1.  登入[Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2.  在 [所有供應項目]**** 頁面中選取您的供應項目。  您的供應項目應該會顯示在 [編輯器]**** 索引標籤中。
3.  在左邊工具列中，選取 [SKU]**** 索引標籤。 
4.  選取您要刪除的 SKU，然後按一下 [刪除]**** 按鈕。
5.  將供應項目[重新發佈至](./cpp-publish-offer.md) Azure Marketplace。

將修改過的供應項目發佈到 Azure Marketplace 後，Azure Marketplace 和 Azure 入口網站中將不再列出所選取的 SKU。


## <a name="roll-back-to-a-previous-sku-version"></a>復原為先前的 SKU 版本

您可以使用下列步驟從 Azure Marketplace 中刪除目前已發佈的 SKU 版本。 此程序完成時，SKU 會回復為先前的版本。

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2. 在 [所有供應項目]**** 頁面中選取您的供應項目。  您的供應項目應該會顯示在 [編輯器]**** 索引標籤中。
3. 在左邊工具列中，選取 [SKU]**** 索引標籤。 
4. 從磁碟版本清單中刪除相關聯解決方案資產的最新版本。  視供應項目類型而定，此欄位可能是**磁碟版本**、**套件版本**或類似資產。 
5. 將供應項目[重新發佈至](./cpp-publish-offer.md) Azure Marketplace。

在 Azure Marketplace 上發佈已修改的供應項目之後，列出項目將不再有所列出 SKU 的目前版本。 在 Azure Marketplace 和 Azure 入口網站中。  SKU 會回復為先前的版本。


## <a name="delete-a-live-offer"></a>刪除上線的供應項目

移除上線的供應項目牽涉到各種不同的程序、商務和法律層面。 請遵循下列步驟來取得支援小組的指引，以從 Azure Marketplace 中移除上線的供應項目︰

1.  使用[建立事件](https://go.microsoft.com/fwlink/?linkid=844975)頁面，或按一下 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)右上角的 [支援]**** 以建立支援票證。

2.  在 [問題類型]**** 清單中選取您的特定供應項目類型，並選取 [類別]**** 清單中的 [移除已發佈的供應項目]****。

3.  提交要求。

支援小組會引導您完成供應項目刪除程序。

> [!NOTE]
> 刪除供應項目 (或 SKU) 不會影響目前購買的供應項目 (或 SKU)。 這些已購買項目將如同一往一樣繼續運作。 不過，未來將無法購買已刪除的供應項目或 SKU。


## <a name="next-steps"></a>後續步驟

在您已熟悉管理供應項目所需的基本作業後，您就可以準備建立 Microsoft [市集供應項目](../cpp-marketplace-offers.md)的執行個體。
