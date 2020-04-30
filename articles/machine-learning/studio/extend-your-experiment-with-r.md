---
title: 透過 R 擴展您的經驗
titleSuffix: ML Studio (classic) - Azure
description: 如何使用執行 R 腳本模組，透過 R 語言擴充 Azure Machine Learning Studio （傳統）的功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7b4b869695eb2073121a889cd81d99c4fc06d4b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79218042"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio （傳統）：使用 R 擴充您的實驗 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
您可以使用 [[執行 r 腳本][execute-r-script]] 模組，透過 R 語言擴充 Azure Machine Learning Studio （傳統）的功能。

此模組接受多個輸入資料集，並產生單一資料集作為輸出。 您可以將 R 指令碼輸入[執行 R 指令碼][execute-r-script]模組的 **R 指令碼**參數中。

您可使用類似下面的程式碼，存取模組的每個輸入連接埠：

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>列出所有目前安裝的封裝
可以變更已安裝的封裝清單。 您可以在[Azure Machine Learning Studio （傳統）支援的 R 套件](https://msdn.microsoft.com/library/azure/mt741980.aspx)中找到目前已安裝的套件清單。

請將下列程式碼輸入[執行 R 指令碼][execute-r-script]模組，取得目前已安裝套件的最新完整清單︰

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

這會將套件的清單傳送至[執行 R 指令碼][execute-r-script]模組的輸出連接埠。
若要檢視套件清單，請將轉換模組 (例如[轉換為 CSV][convert-to-csv]) 連接至[執行 R 指令碼][execute-r-script]模組的左輸出，執行實驗，然後按一下轉換模組的輸出並選取 [下載]****。 

![下載「轉換為 CSV」模組的輸出](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>匯入封裝
您可以在[執行 R 指令碼][execute-r-script] 模組中使用下列命令，匯入尚未安裝的套件：

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

其中 `my_favorite_package.zip` 檔案包含您的套件。




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
