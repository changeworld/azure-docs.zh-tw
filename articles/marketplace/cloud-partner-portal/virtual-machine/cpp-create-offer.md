---
title: 在 Azure 應用商店中創建虛擬機器產品/服務
description: 列出在 Microsoft Azure Marketplace 建立新虛擬機器 (VM) 供應項目所需步驟。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: bc1cf7a839307e65bd91eb29531663141e521472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278036"
---
# <a name="create-virtual-machine-offer"></a>建立虛擬機器供應項目

本節列出在 Microsoft Azure Marketplace 建立新虛擬機器 (VM) 供應項目要求所需步驟。  每個供應項目在 Microsoft Azure Marketplace 中皆為獨立實體，會與一或多個 SKU 相關聯。  虛擬機器供應項目由下列資產群組和支援服務所組成： 

![虛擬機器供應項目資產](./media/publishvm_002.png)

其中：

|  **資產群組**   |  **描述**  |
|  ---------------   |  ---------------  |
|    SKU            |  供應項目的最小購買單位。 單一供應項目 (產品類別) 可與多個 SKU 相關聯，以便區分支援的功能、 虛擬機器映像型別與計費模式。 |
|  Marketplace       | 包含行銷、法律及潛在客戶管理的資產和規格。  <ul><li> 行銷資產包括供應項目名稱、說明和標誌</li> <li> 法律資產包括隱私權原則、使用條款及其他法律文件</li>  <li> 潛在客戶管理原則可指定於 Microsoft Azure Marketplace 使用者入口網站管理潛在客戶的方式。</li> </ul> |
| 支援            | 包含支援聯絡人與原則的相關資訊 |
| 試用產品         | 定義可讓終端使用者在購買前測試供應項目的資產 |
|  |  |


## <a name="new-offer-form"></a>新增供應項目表單

登錄[雲合作夥伴門戶](https://cloudpartner.azure.com/)後，按一下左側功能表列上的 **"新優惠**"項。 在顯示的選單中，按一下 [虛擬機器]**** 以顯示 [新增供應項目]****，並開始定義新虛擬機器供應項目資產。 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![選擇新虛擬機器供應項目使用者介面](./media/publishvm_003.png)

> [!WARNING]
> 如果 [虛擬機器]**** 選項並未顯示，或未啟用，則您的帳戶沒有建立此類供應項目的權限。  請檢查您是否已符合此類供應項目所有[先決條件](./cpp-prerequisites.md)，包括：註冊開發人員帳戶。


## <a name="next-steps"></a>後續步驟

在本節中後續的主題可對照至**新增供應項目**頁面中的索引標籤 （適用於虛擬機器供應項目型別）。  個別文章將說明如何使用相關索引標籤來定義資產群組，以及新虛擬機器供應項目的支援服務。

- ["提供設置"選項卡](./cpp-offer-settings-tab.md)
- [SKU 索引標籤](./cpp-skus-tab.md)
- [試用產品索引標籤](./cpp-test-drive-tab.md)
- [Marketplace 索引標籤](./cpp-marketplace-tab.md)
- [支援索引標籤](./cpp-support-tab.md)
