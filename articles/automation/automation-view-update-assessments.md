---
title: 查看 Azure 更新管理更新評量
description: 本文說明如何查看更新部署的更新評量。
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 58d3cf6261456c09195ad6dafaeb781b55d9e5ee
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373016"
---
# <a name="view-azure-update-management-update-assessments"></a>查看 Azure 更新管理更新評量

在您的 Azure 自動化帳戶中，選取 [**更新管理**] 以查看電腦的狀態。

此檢視會提供您的機器、缺少的更新、更新部署以及已排定之更新部署的相關資訊。 在 [**相容性**] 欄中，您可以看到上次評估電腦的時間。 在 [**更新代理程式準備就緒**] 欄位中，您可以看到更新代理程式的健全狀況。 如果發生問題，請選取 [移至疑難排解檔] 連結，以協助您更正問題。

若要執行會傳回機器、更新或部署相關資訊的記錄檔搜尋，請選取清單中的對應專案。 [記錄搜尋] 窗格隨即開啟，並顯示所選項目的查詢：

![更新管理的預設檢視](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>檢視缺少的更新

選取 [缺少的更新] 以檢視機器缺少的更新清單。 會列出每個更新，而且您可以選取更新。 系統會顯示需要更新的機器數目、作業系統詳細資料和連結以取得詳細資訊。 [**記錄搜尋**] 窗格也會顯示更多有關更新的詳細資料。

![缺少更新](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>更新分類

下表列出更新管理中支援的更新分類，以及每個分類的定義。

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

### <a name="linux-2"></a>Linux

|分類  |描述  |
|---------|---------|
|重大更新和安全性更新     | 特定問題或特定產品的安全性相關問題的更新，         |
|其他更新     | 本質上不重要或不是安全性更新的所有其他更新。        |

針對 Linux，更新管理可以在顯示評量資料時，區別雲端中的重大更新和安全性更新。 （這很可能是因為雲端中的資料擴充）。針對修補，更新管理會依賴機器上可用的分類資料。 與其他散發套件不同的是，CentOS 不會在產品的 RTM 版本中提供這項資訊。 如果您已將 CentOS 機器設定為傳回下列命令的安全性資料，更新管理可以根據分類進行修補：

```bash
sudo yum -q --security check-update
```

目前沒有支援的方法可在 CentOS 上啟用原生分類資料可用性。 目前，只會為已自行啟用此功能的客戶提供最佳支援。

若要將 Red Hat Enterprise 版本6上的更新分類，您必須安裝 yum-security 外掛程式。 在 Red Hat Enterprise Linux 7 上，外掛程式已是 yum 本身的一部分，不需要安裝任何專案。 如需進一步資訊，請參閱下列 Red Hat[知識文章](https://access.redhat.com/solutions/10021)。

## <a name="next-steps"></a>後續步驟

在您查看任何更新評估之後，您可以遵循[管理 Azure vm 的更新和修補程式](automation-tutorial-update-management.md)中的步驟來排程更新部署。
