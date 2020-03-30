---
title: 查看 Azure 更新管理更新評估
description: 本文介紹如何查看更新部署的更新評估。
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 58d3cf6261456c09195ad6dafaeb781b55d9e5ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278411"
---
# <a name="view-azure-update-management-update-assessments"></a>查看 Azure 更新管理更新評估

在 Azure 自動化帳戶中，選擇 **"更新管理"** 以查看電腦的狀態。

此檢視會提供您的機器、缺少的更新、更新部署以及已排定之更新部署的相關資訊。 在 **"遵守"** 列中，您可以看到上次評估電腦的時間。 在 **"更新代理準備"** 列中，您可以看到更新代理的運行狀況。 如果有問題，請選擇連結以轉到可用於疑難排解的文檔，這些文檔可以説明您更正問題。

要運行返回有關電腦、更新或部署的資訊的日誌搜索，請在清單中選擇相應的項。 [記錄搜尋]**** 窗格隨即開啟，並顯示所選項目的查詢：

![更新管理的預設檢視](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>檢視缺少的更新

選取 [缺少的更新]**** 以檢視機器缺少的更新清單。 會列出每個更新，而且您可以選取更新。 將顯示有關需要更新的電腦數量、作業系統詳細資訊以及詳細資訊連結的資訊。 **"日誌搜索"** 窗格還顯示有關更新的更多詳細資訊。

![遺失更新](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>更新分類

下表列出了更新管理中支援的更新分類，並為每個分類提供了定義。

### <a name="windows"></a>Windows

|分類  |描述  |
|---------|---------|
|重大更新     | 特定問題的更新，負責處理與安全性無關的重大錯誤。        |
|安全性更新     | 特定產品的安全性相關更新。        |
|更新彙總套件     | 一組累計的 Hotfix，封裝在一起以便於部署。        |
|Feature Pack     | 在產品版本之外散發的新產品功能。        |
|Service Pack     | 一組套用到應用程式的累計 Hotfix。        |
|定義更新     | 病毒或其他定義檔案的更新。        |
|工具     | 有助於完成一或多個工作的公用程式或功能。        |
|更新     | 目前安裝之應用程式或檔案的更新。        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|分類  |描述  |
|---------|---------|
|重大更新和安全性更新     | 特定問題或特定產品的安全性相關問題的更新，         |
|其他更新     | 所有其他不在性質上不重要的更新或不是安全更新的更新。        |

對於 Linux，更新管理可以在顯示評估資料的同時區分雲中的重大更新和安全更新。 （由於雲中的資料豐富，因此可以進行這種細微性。對於修補，更新管理依賴于電腦上可用的分類資料。 與其他發行版本不同，CentOS 在產品的 RTM 版本中沒有此資訊。 如果配置了 CentOS 電腦以返回以下命令的安全資料，則更新管理可以基於分類進行修補：

```bash
sudo yum -q --security check-update
```

目前沒有支援在 CentOS 上啟用本機分類資料可用性的方法。 此時，僅向自行啟用此功能的客戶提供盡力而為的支援。

要對紅帽企業版 6 上的更新進行分類，您需要安裝 yum-安全外掛程式。 在紅帽企業Linux 7上，外掛程式已經是yum本身的一部分，沒有必要安裝任何東西。 有關詳細資訊，請參閱以下紅帽[知識文章](https://access.redhat.com/solutions/10021)。

## <a name="next-steps"></a>後續步驟

查看任何更新評估後，可以按照[Azure VM 的管理更新和修補程式](automation-tutorial-update-management.md)的步驟來安排更新部署。
