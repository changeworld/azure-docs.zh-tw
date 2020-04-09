---
title: 更新現有的 Azure IoT 邊緣模組產品 /服務 |Azure 應用商店
description: 說明如何更新 Microsoft Azure Marketplace 中之現有 IoT Edge 模組供應項目。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 019711f35d249e6684013feddb6b453d509f66d4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985349"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>更新現有 IoT Edge 模組供應項目

>[!Important]
>從 2020 年 4 月 13 日起,我們將開始將 IoT Edge 模組產品的管理轉移到合作夥伴中心。 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照[創建IoT Edge模組產品/服務](https://aka.ms/AzureCreateIoT)中的說明進行操作,以管理遷移的優惠。

本文將逐步說明在 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)更新 IoT Edge 模組供應項目並重新發佈的各個層面。

您可能會因為某些原因而想要更新供應項目，例如：

-  將新的 IoT Edge 模組映像版本新增至現有的 SKU。
-  新增 SKU。
-  更新供應項目或個別 SKU 的市集中繼資料。

為了協助您修改這些項目，入口網站提供了 **「比較」** 與 **「記錄」** 的功能。  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>不允許對 IoT Edge 模組供應項目或 SKU 進行的變更

IoT Edge 模組產品/服務或 SKU 的一些屬性在 Azure 應用商店中即時運行後無法更改。 您無法變更下列設定：

-  供應項目的**供應項目識別碼**和**發行者識別碼**
-  現有 SKU 的 **SKU** 識別碼
-  版本標記，例如：`1.0.1`
-  現有 SKU 的計費/授權模式變更

## <a name="common-update-operations"></a>一般更新作業

下列更新作業很常見。

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>更新 SKU 的 IoT Edge 模組映像版本

物聯網邊緣模組映像通常使用安全修補程式、其他功能等定期更新。 針對此類狀況，建議透過以下步驟更新 SKU 參考的 IoT Edge 模組映像：

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2.  在 [所有供應項目]**** 之下，尋找您要更新的供應項目。

3.  在 [SKU]**** 索引標籤中，按一下與 IoT Edge 模組映像關聯的 SKU，即可執行更新。

4.  在 [Edge 模組映像]**** 下，選取 [+ 新增新的映像版本]****，即可新增 IoT Edge 模組映像。

5.  提供新的 IoT Edge 模組**映像版本**。 映像版本須遵循與舊版相同的標記指導方針。 版本格式應該是 X.Y.Z，其中 X、Y 和 Z 是整數。 請確認您提供的新版本大於所有先前的版本。

6.  按一下 [發佈]**** 啟動工作流程，將新的 IoT Edge 模組版本發佈至 Microsoft Azure Marketplace。

### <a name="add-a-new-sku"></a>新增 SKU

依照下列步驟，讓您的供應項目可使用新的 SKU： 

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2.  在 [所有供應項目]**** 之下，尋找您要更新的供應項目。

3.  在 [SKU]**** 索引標籤下方，按一下 [新增 SKU]****，並在快顯視窗中提供 [SKU 識別碼]****。

4.  使用[將IoT邊緣模組](./cpp-publish-offer.md)發佈到Azure應用商店中描述的步驟重新發佈IoT邊緣模組。

5.  選取 [發佈]**** 啟動工作流程，以發佈新 SKU。


### <a name="update-offer-marketplace-metadata"></a>更新供應項目市集中繼資料

使用下列步驟來更新與供應項目關聯的市集中繼資料。 (例如：公司名稱、標誌等等)

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2.  在 **"所有優惠**"下,找到您要更新的報價。

3.  轉到**應用商店**選項卡。使用[「將 IoT 邊緣」模組](./cpp-publish-offer.md)中的說明發佈到 Azure 應用商店一文進行元數據更改。

4.  選取 [發佈]**** 啟動工作流程，以發佈變更。

## <a name="compare-feature"></a>比較功能

當您變更已發佈的供應項目時，可使用 **「比較」** 功能來稽核已完成的變更。 

**使用「比較」功能：**

1.  在編輯流程中的任何時間點，選取您供應項目的 [比較]****。

    ![比較功能按鈕](./media/iot-edge-module-compare.png)


2.  並排檢視行銷資產與中繼資料。


## <a name="history-of-publishing-actions"></a>發佈動作的記錄

若要查看發佈活動記錄，請在 Cloud Partner 入口網站左邊導覽功能表列中，選擇 [記錄]**** 索引標籤。 您可以檢視在您的 Microsoft Azure Marketplace 供應項目生命週期期間發生的動作 (含時間戳記)。  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
