---
title: 使用 Azure HDInsight 工具 PySpark 互動式環境
description: 了解如何使用適用於 Visual Studio Code 的 Azure HDInsight 工具來建立及提交查詢和指令碼。
keywords: VScode,Azure HDInsight 工具,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,互動式 Hive,互動式查詢
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: 7cf86824bef5b6f521df0f0446d5337e2b7c418a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130131"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>設定 Visual Studio Code 的 PySpark 互動式環境

下列步驟說明如何在 VSCode 中設定 PySpark 互動式環境。 此步驟僅適用于非 Windows 使用者。

我們會使用 **python/pip** 命令在您的首頁路徑中建置虛擬環境。 如果想要使用其他版本，您需要手動變更 **python/pip** 命令的預設版本。 請參閱[更新替代項目](https://linux.die.net/man/8/update-alternatives)了解更多詳細資料。

1. 安裝 [Python](https://www.python.org/downloads/) 和 [pip](https://pip.pypa.io/en/stable/installing/)。

   * 從安裝 Python [https://www.python.org/downloads/](https://www.python.org/downloads/) 。 
   * [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/)如果未從 Python 安裝) 安裝 pip，請從 (安裝 pip。
   * 選擇性地使用命令、和分別驗證 Python 和 pip 是否已順利安裝 `python --version` `pip --version` 。 

     > [!NOTE]
     > 建議您手動安裝 Python，而不是使用 macOS 預設版本。

2. 執行下列命令來安裝 **virtualenv** 。

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>其他套件

在 Linux 上，如果您遇到下列錯誤訊息，請執行下列兩個命令來安裝必要的套件。

   ![安裝適用于 python 的 libkrb5 套件](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

重新開機 VSCode，然後返回 VSCode 編輯器並執行 **Spark： PySPark Interactive** 命令。

## <a name="next-steps"></a>後續步驟

### <a name="demo"></a>示範

* HDInsight for VS Code：[影片](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>工具和擴充功能

* [使用適用於 Visual Studio Code 的 Azure HDInsight 工具](hdinsight-for-vscode.md)
* [使用 Azure Toolkit for IntelliJ 來建立和提交 Apache Spark Scala 應用程式](spark/apache-spark-intellij-tool-plugin.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](spark/apache-spark-jupyter-notebook-install-locally.md)
