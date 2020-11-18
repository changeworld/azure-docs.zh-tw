---
title: 使用 Azure 資料科學虛擬機器
description: 瞭解如何連線到 Azure 資料科學虛擬機器 (DSVM) ，以將可用的計算能力延伸至 Azure Notebooks 預覽。
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: 7d65a39c1a1d302e565b59db1644af2c08befb6b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843157"
---
# <a name="use-azure-data-science-virtual-machines"></a>使用 Azure 資料科學虛擬機器

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

依預設，專案會在 **免費計算** 層上執行，其限制為 4 GB 的記憶體和 1 gb 的資料，以避免濫用。 您可以藉由使用已佈建在 Azure 訂用帳戶中的其他虛擬機器來繞過這些限制。 基於此目的，最佳選擇是使用 **Linux (Ubuntu)** 映射的資料科學虛擬機器 DSVM) 的 Azure 資料科學虛擬機器 (。 這種 DSVM 已預先設定 Azure Notebooks 所需的一切，並且會自動出現在 Azure Notebooks 的 [ **執行** ] 下拉式清單中。

> [!Note]
> 只有使用 on Linux Ubuntu 映射建立的 Dsvm 才支援 Azure Notebooks。 Windows 2012、Windows 2016 或 Linux CentOS 映射不支援筆記本。

## <a name="create-a-dsvm-instance"></a>建立 DSVM 實例

若要建立新的 DSVM 執行個體，請依照[建立 Ubuntu 資料科學 VM](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md) 中的指示執行。 如需詳細資訊（包括定價詳細資料），請參閱 [資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a name="connect-to-the-dsvm"></a>連線至 DSVM

建立 DSVM 之後，請選取 [Azure Notebooks 專案] 儀表板上的 [ **執行** ] 下拉式清單，然後選取適當的 DSVM 實例。 當下列條件成立時，下拉式清單會顯示 DSVM 實例：

- 您以使用 Azure Active Directory (AAD) 的帳戶 (如公司帳戶) 登入 Azure Notebooks。
- 您的帳戶已連接到 Azure 訂用帳戶。
- 您在該訂用帳戶中有一或多部虛擬機器（至少須具備讀取器存取權）使用 Linux (Ubuntu) 映射的資料科學虛擬機器。 ) 

![專案儀表板上的下拉式清單中的資料科學虛擬機器執行個體](media/project-compute-tier-dsvm.png)

當您選取 DSVM 執行個體時，Azure Notebooks 可能會提示您輸入您在建立 VM 時使用的特定機器認證。

> [!Important]
> 使用者名稱必須是小寫，才能與 JupyterHub 搭配使用。

如果有任何條件不符合，您仍然可以連接到 DSVM。 在下拉式清單中，選取 [ **直接計算** ] 選項，它會提示您輸入名稱， (顯示在清單) 、VM 的 IP 位址和埠 (通常是8000、JupyterHub 接聽) 的預設埠，以及 VM 認證：

![收集 [直接計算] 選項伺服器資訊的提示](media/project-compute-tier-direct.png)

您可以從 Azure 入口網站中的 [DSVM] 頁面取得這些值。

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>從 DSVM 存取 Azure Notebooks 檔案

DSVM 版本19.06.15 或更新版本支援檔案系統存取。 若要檢查版本，請先透過 SSH 連接到您的 DSVM，然後執行下列命令： `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (您必須使用此處所示的確切 IP 位址) 。 版本號碼會顯示在 "version" 的輸出中。

若要保留具有 **免費計算** 層的檔案路徑，您一次只能在 DSVM 上開啟一個專案。 若要開啟新的專案，您必須先關閉開啟的專案。

當專案在 VM 上執行時，這些檔案會掛接在 Jupyter 伺服器的根目錄 (JupyterHub) 中顯示的目錄，取代預設的 Azure Notebooks 檔。 當您使用筆記本 UI 上的 [ **關機** ] 按鈕關閉 VM 時，Azure Notebooks 會還原預設檔案。

![Azure Notebooks 中的 [關閉] 按鈕](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>建立新的 DSVM 使用者

如果有多個使用者共用 DSVM，您可以建立並使用每個筆記本使用者的 DSVM 使用者，以避免彼此封鎖：

1. 在 [Azure 入口網站](https://portal.azure.com)上，流覽至您的虛擬機器。
1. 在左邊界的 [ **支援 + 疑難排解** ] 底下，選取 [ **重設密碼**]。
1. 輸入新的使用者 **名稱**。 使用者名稱必須是小寫，才能與 JupyterHub 搭配使用。 輸入密碼。 然後選取 [ **更新**]。  (現有的使用者名稱不會受到影響。 ) 
1. 針對任何其他使用者重複上述步驟。

## <a name="next-steps"></a>後續步驟

深入瞭解 [Azure 資料科學虛擬機器的簡介](../machine-learning/data-science-virtual-machine/overview.md)dsvm。