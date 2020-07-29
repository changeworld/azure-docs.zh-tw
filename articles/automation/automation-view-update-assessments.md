---
title: 檢視 Azure 自動化更新評量
description: 此文章說明如何檢視更新管理部署的更新評量。
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 321146364897d46a403bdfd6789fcb219179d88c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830594"
---
# <a name="view-update-assessments"></a>檢視更新評估

在您的 Azure 自動化帳戶中，選取 [更新管理] 來檢視機器的狀態。

此檢視會提供您的機器、缺少的更新、更新部署以及已排定之更新部署的相關資訊。 在 [合規性] 欄中，您可以看到機器上次的評估時間。 在 [更新代理程式整備程度] 欄中，您可以查看更新代理程式的健康情況。 如果發生問題，請選取連結來移至可協助您更正問題的疑難排解文件。

若要執行記錄搜尋來傳回與機器、更新或部署相關的資訊，請選取清單中的對應項目。 [記錄搜尋] 窗格隨即開啟，並顯示所選項目的查詢。

![更新管理的預設檢視](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>檢視缺少的更新

選取 [缺少的更新] 以檢視機器缺少的更新清單。 會列出每個更新，而且您可以選取更新。 畫面上會顯示需要更新的機器數目、作業系統詳細資料，以及一個能提供詳細資訊的連結。 [記錄搜尋] 窗格也會顯示與更新有關的詳細資訊。

![遺失更新](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>使用更新分類

下表列出「更新管理」中的更新分類清單，其中包含每個分類的定義。

### <a name="windows"></a>Windows

|分類  |描述  |
|---------|---------|
|重大更新     | 特定問題的更新，負責解決與安全性無關的重大錯誤 (Bug)。        |
|安全性更新     | 產品特定的安全性相關問題更新。        |
|更新彙總套件     | 一組封裝在一起以便於部署的 Hotfix。        |
|Feature Pack     | 在產品版本之外散發的新產品功能。        |
|Service Pack     | 一組套用到應用程式的 Hotfix。        |
|定義更新     | 病毒或其他定義檔案的更新。        |
|工具     | 有助於完成一或多個工作的公用程式或功能。        |
|更新     | 目前已安裝應用程式或檔案的更新。        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|分類  |描述  |
|---------|---------|
|重大更新和安全性更新     | 特定問題或特定產品的安全性相關問題的更新，         |
|其他更新     | 本質上不重要或非安全性更新的所有其他更新。        |

針對 Linux，更新管理可以區分雲端中的重大更新和安全性更新，同時顯示評量資料。 (能實現此保證是因為雲端資料豐富性。)針對修補，「更新管理」仰賴機器上可用的分類資料。 與其他發行版本不同，CentOS 在產品的 RTM 版本中沒有此資訊可供使用。 如果您將 CentOS 機器設定為傳回下列命令的安全性資料，則更新管理就能根據分類進行修補：

```bash
sudo yum -q --security check-update
```

目前沒有支援的方法可以在 CentOS 上啟用原生分類資料可用性。 目前，為那些已自行啟用此功能的客戶，只提供盡力而為的支援。

若要將 Red Hat Enterprise 版本 6 上的更新分類，您必須安裝 yum-security 外掛程式。 在 Red Hat Enterprise Linux 7 上，外掛程式已經是 yum 本身的一部分，因此不需要安裝任何項目。 如需進一步資訊，請參閱下列 Red Hat [知識文章](https://access.redhat.com/solutions/10021) \(英文\)。

## <a name="next-steps"></a>後續步驟

如需一般資訊，請參閱[管理 Azure VM 的更新和修補程式](automation-tutorial-update-management.md)。
