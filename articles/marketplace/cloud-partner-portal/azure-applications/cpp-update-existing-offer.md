---
title: 更新現有的 Azure 應用程式產品/服務 |Azure 應用商店
description: 如何在 Azure Marketplace 上更新現有的 Azure 應用程式供應項目。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: dsindona
ms.openlocfilehash: 152fd24fbc5d2762d381ffce2a937bc448858b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275946"
---
# <a name="update-an-existing-azure-application-offer"></a>更新現有的 Azure 應用程式供應項目

在您的供應項目發行並上架之後，您可以對它進行多種更新。 您對供應項目新版本所做的任何變更都應該儲存並重新發行，以在 Marketplace 中反映您所做的變更。 本文將逐步說明在 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)中更新受控應用程式供應項目的各個層面。

您可能會因為某些原因而想要更新供應項目，例如：

- 將新的映像版本新增至現有的 SKU。
- 新增 SKU。
- 更新供應項目或個別 SKU 的市集中繼資料。

為了協助您修改這些項目，入口網站提供了 [比較]**** 與 [歷程記錄]**** 功能。

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>不允許對 Azure 應用程式供應項目或 SKU 進行的變更

供應項目於 Azure Marketplace 上線後，便無法變更容器供應項目或 SKU 的某些屬性。 您無法變更下列設定：

- 供應項目的供應項目識別碼和發行者識別碼
- 現有 SKU 的 SKU 識別碼
- 版本標記，例如：`1.0.1`
- 現有 SKU 的計費/授權模式變更

## <a name="common-update-operations"></a>一般更新作業

下列更新作業很常見。

### <a name="update-image-version-for-a-sku"></a>更新 SKU 的映像版本

常見於要使用安全性修補程式、額外功能等項目定期更新的映像。 在此案例中，您想要使用以下步驟來更新 SKU 所參考的映像：

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2. 在 [所有供應項目]**** 之下，尋找您要更新的供應項目。
3. 在 [SKU]**** 索引標籤中，選取與映像相關聯的 SKU 以進行更新。
4. 選取 [+ 新增映像版本]**** 來新增映像。
5. 提供新的映像版本。 映像版本須遵循與舊版相同的標記指導方針。 版本格式應該是 X.Y.Z，其中 X、Y 和 Z 是整數。 請確認您提供的新版本大於所有先前的版本。
6. 按一下 [發佈]**** 啟動工作流程，將新的容器映像版本發佈至 Azure Marketplace。

### <a name="add-a-new-sku"></a>新增 SKU

依照下列步驟，讓您的供應項目可使用新的 SKU：

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2. 在 [所有供應項目]**** 之下，尋找您要更新的供應項目。
3. 在 [SKU]**** 索引標籤下方，按一下 [新增 SKU]****，並在快顯視窗中提供 [SKU 識別碼]****。
4. 使用[發佈 Azure 應用程式供應項目](./cpp-publish-offer.md)中所述的步驟，重新發佈供應項目。
5. 選取 [發佈]**** 啟動工作流程，以發佈新 SKU。

### <a name="update-offer-marketplace-metadata"></a>更新供應項目市集中繼資料

使用下列步驟來更新與供應項目關聯的市集中繼資料。 (例如：公司名稱、標誌等等)

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2. 在 [所有供應項目]**** 下，尋找您要更新的供應項目。
3. 轉到**應用商店**選項卡。使用["發佈 Azure 應用程式"服務中的](./cpp-publish-offer.md)說明進行中繼資料更改。
4. 選取 [發佈]**** 啟動工作流程，以發佈變更。
 
>[!Note]
>雲解決方案供應商 （CSP） 合作夥伴管道加入宣告現已可用。  有關通過 Microsoft CSP 合作夥伴管道行銷產品/服務的更多資訊[，請參閱雲解決方案供應商](../../cloud-solution-providers.md)。

## <a name="deleting-an-existing-offer"></a>刪除現有的供應項目

您可能決定從 Marketplace 中移除您的供應項目。 刪除供應項目不會影響供應項目的目前購買。 那些客戶購買將如同一往一樣繼續運作。 不過，刪除完成之後，該供應項目將不再提供購買。

「終止供應項目」是終止服務及/或您與現有客戶之間授權合約的程序。
＜Microsoft Marketplace 發行者合約＞(請參閱第 7 節) 與＜參與原則＞(請參閱第 6.2 節) 會管理移除和終止供應項目的相關指導方針與原則。

如需詳細資訊，請參閱[從 Azure Marketplace 中刪除供應項目/SKU](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete)。

## <a name="compare-feature"></a>比較功能

當您變更已發佈的供應項目之後，即可使用 [比較] 功能來稽核您所做的變更。

若要使用 [比較] 功能：

1. 在編輯流程中的任何時間點，選取您供應項目的 [比較]。
2. 並排檢視行銷資產與中繼資料。

## <a name="history-of-publishing-actions"></a>發佈動作的歷程記錄

若要查看發佈活動記錄，請在 Cloud Partner 入口網站左邊導覽功能表列中，選擇 [記錄]**** 索引標籤。 您可以檢視在您的 Microsoft Azure Marketplace 供應項目生命週期期間發生的動作 (含時間戳記)。

## <a name="next-steps"></a>後續步驟

[Azure 應用程式產品/服務](./cpp-azure-app-offer.md)
