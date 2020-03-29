---
title: 使用 Azure 資料科學虛擬機器
description: 瞭解如何連接到 Azure 資料科學虛擬機器 （DSVM） 以擴展 Azure 筆記本預覽版可用的計算能力。
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: b4da63b7b2a6da4316215b85a09ca7420745251c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898408"
---
# <a name="use-azure-data-science-virtual-machines"></a>使用 Azure 資料科學虛擬機器

預設情況下，專案在**免費計算**層上運行，該層限制為 4 GB 記憶體和 1 GB 的資料，以防止濫用。 您可以藉由使用已佈建在 Azure 訂用帳戶中的其他虛擬機器來繞過這些限制。 為此，最佳選擇是使用**資料科學虛擬機器（Ubuntu）映射的**Azure 資料科學虛擬機器 （DSVM）。 此類 DSVM 預先配置了 Azure 筆記本所需的一切，並自動顯示在 Azure 筆記本中的 **"運行**"下拉清單中。

> [!Note]
> Azure 筆記本僅在使用 Linux Ubuntu 映射創建的 DSVM 上受支援。 Windows 2012、Windows 2016 或 Linux CentOS 映射不支援筆記本。

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-dsvm-instance"></a>創建 DSVM 實例

若要建立新的 DSVM 執行個體，請依照[建立 Ubuntu 資料科學 VM](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro) 中的指示執行。 有關詳細資訊（包括定價詳細資訊），請參閱[資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a name="connect-to-the-dsvm"></a>連線至 DSVM

創建 DSVM 後，在 Azure 筆記本專案儀表板上選擇 **"運行**下拉清單"，然後選擇相應的 DSVM 實例。 如果以下條件為 true，下拉清單將顯示 DSVM 實例：

- 您以使用 Azure Active Directory (AAD) 的帳戶 (如公司帳戶) 登入 Azure Notebooks。
- 您的帳戶已連接到 Azure 訂用帳戶。
- 該訂閱中至少有一個或多個虛擬機器，至少具有讀取器存取權限，這些虛擬機器使用 Linux 資料科學虛擬機器 （Ubuntu） 映射。

![專案儀表板上的下拉式清單中的資料科學虛擬機器執行個體](media/project-compute-tier-dsvm.png)

當您選取 DSVM 執行個體時，Azure Notebooks 可能會提示您輸入您在建立 VM 時使用的特定機器認證。

> [!Important]
> 使用者名必須小寫，才能將其與 JupyterHub 一起使用。

如果未滿足任何條件，您仍然可以連接到 DSVM。 在下拉清單中，選擇 **"直接計算"** 選項，該選項會提示您輸入名稱（顯示在清單中）、VM 的 IP 位址和埠（通常為 8000，JupyterHub 偵聽的預設埠）和 VM 憑據：

![收集 [直接計算] 選項伺服器資訊的提示](media/project-compute-tier-direct.png)

從 Azure 門戶中的 DSVM 頁獲取這些值。

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>從 DSVM 訪問 Azure 筆記本檔

DSVM 版本 19.06.15 或更高版本支援檔案系統訪問。 要檢查版本，請首先通過 SSH 連接到 DSVM，然後運行以下命令：（`curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"`您必須使用此處顯示的確切 IP 位址）。 版本號顯示在"版本"的輸出中。

要使用**自由計算**層保留檔路徑的同位，您可以在 DSVM 上一次只打開一個專案。 要打開新專案，必須首先關閉打開的專案。

在 VM 上運行專案時，檔將安裝在 Jupyter 伺服器（JupyterHub 中顯示的目錄）的根目錄中，替換預設的 Azure 筆記本檔。 使用筆記本 UI 上的 **"關閉"** 按鈕關閉 VM 時，Azure 筆記本將恢復預設檔。

![Azure 筆記本中的關機按鈕](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>創建新的 DSVM 使用者

如果多個使用者共用 DSVM，則可以通過為每個筆記本使用者創建和使用 DSVM 使用者來避免相互阻塞：

1. 在[Azure 門戶](https://portal.azure.com)上，導航到虛擬機器。
1. 在左邊距中的 **"支援 + 故障排除"** 下，選擇 **"重置密碼**"。
1. 輸入新的**使用者名**。 使用者名必須小寫，才能將其與 JupyterHub 一起使用。 輸入密碼。 然後選擇 **"更新**"。 （現有使用者名不受影響。
1. 對任何其他使用者重複上一步。

## <a name="next-steps"></a>後續步驟

瞭解有關[Azure 資料科學虛擬機器簡介的](/azure/machine-learning/data-science-virtual-machine/overview)DSVM 的更多詳細資訊。
