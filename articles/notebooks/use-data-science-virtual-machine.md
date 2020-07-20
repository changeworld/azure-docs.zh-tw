---
title: 使用 Azure 資料科學虛擬機器
description: 瞭解如何連線到 Azure 資料科學虛擬機器（DSVM），以擴充可供 Azure Notebooks 預覽的計算能力。
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: 291f1ac093568f50ad6146b70fa1ef69263b7b5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830296"
---
# <a name="use-azure-data-science-virtual-machines"></a>使用 Azure 資料科學虛擬機器

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

根據預設，專案會在**免費計算**層上執行，其限制為 4 gb 的記憶體和 1 GB 的資料，以防止濫用。 您可以藉由使用已佈建在 Azure 訂用帳戶中的其他虛擬機器來繞過這些限制。 基於此目的，最佳選擇是使用**適用于 Linux （Ubuntu）的資料科學虛擬機器**的 Azure 資料科學虛擬機器（DSVM）映射。 這類 DSVM 會預先設定 Azure Notebooks 所需的所有專案，並自動顯示在 Azure Notebooks 的 [**執行**] 下拉式清單中。

> [!Note]
> 只有在使用 on Linux Ubuntu 映射建立的 Dsvm 上，才支援 Azure Notebooks。 Windows 2012、Windows 2016 或 Linux CentOS 映射不支援筆記本。

## <a name="create-a-dsvm-instance"></a>建立 DSVM 實例

若要建立新的 DSVM 執行個體，請依照[建立 Ubuntu 資料科學 VM](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro) 中的指示執行。 如需詳細資訊（包括定價詳細資訊），請參閱[資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a name="connect-to-the-dsvm"></a>連線至 DSVM

建立 DSVM 之後，請選取 [Azure Notebooks 專案] 儀表板上的 [**執行**] 下拉式清單，然後選取適當的 DSVM 實例。 如果下列條件成立，則下拉式清單會顯示 DSVM 實例：

- 您以使用 Azure Active Directory (AAD) 的帳戶 (如公司帳戶) 登入 Azure Notebooks。
- 您的帳戶已連接到 Azure 訂用帳戶。
- 您在該訂用帳戶中有一或多部虛擬機器，且至少具有讀取者存取權，使用 Linux （Ubuntu）的資料科學虛擬機器。）

![專案儀表板上的下拉式清單中的資料科學虛擬機器執行個體](media/project-compute-tier-dsvm.png)

當您選取 DSVM 執行個體時，Azure Notebooks 可能會提示您輸入您在建立 VM 時使用的特定機器認證。

> [!Important]
> 使用者名稱必須是小寫，才能搭配 JupyterHub 使用。

如果不符合任何條件，您仍然可以連接到 DSVM。 在下拉式清單中，選取 [**直接計算**] 選項，它會提示您輸入名稱（要顯示在清單中）、VM 的 IP 位址和埠（通常是8000、JupyterHub 接聽的預設埠），以及 vm 認證：

![收集 [直接計算] 選項伺服器資訊的提示](media/project-compute-tier-direct.png)

您可以從 Azure 入口網站的 [DSVM] 頁面取得這些值。

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>從 DSVM 存取 Azure Notebooks 檔案

DSVM 版本的19.06.15 或更新版本支援檔案系統存取。 若要檢查版本，請先透過 SSH 連接到您的 DSVM，然後執行下列命令： `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` （您必須使用此處所示的確切 IP 位址）。 版本號碼會顯示在「版本」的輸出中。

若要保留檔案路徑與**免費計算**層的同位，您一次只能在 DSVM 上開啟一個專案。 若要開啟新的專案，您必須先關閉開啟的專案。

當專案在 VM 上執行時，這些檔案會掛接到 Jupyter 伺服器的根目錄（顯示在 JupyterHub 中的目錄），取代預設的 Azure Notebooks 檔案。 當您使用筆記本 UI 上的 [**關機**] 按鈕關閉 VM 時，Azure Notebooks 會還原預設檔案。

![Azure Notebooks 中的 [關機] 按鈕](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>建立新的 DSVM 使用者

如果有多個使用者共用 DSVM，您可以建立並使用每個筆記本使用者的 DSVM 使用者，以避免彼此封鎖：

1. 在 [ [Azure 入口網站](https://portal.azure.com)上，流覽至您的虛擬機器。
1. 在左邊界的 [**支援與疑難排解**] 底下，選取 [**重設密碼**]。
1. 輸入新的使用者**名稱**。 使用者名稱必須是小寫，才能搭配 JupyterHub 使用。 輸入密碼。 然後選取 [**更新**]。 （現有的使用者名稱不會受到影響）。
1. 針對任何其他使用者重複上一個步驟。

## <a name="next-steps"></a>後續步驟

深入瞭解[Azure 資料科學虛擬機器簡介](/azure/machine-learning/data-science-virtual-machine/overview)dsvm。
