---
title: 機器學習與資料科學工具
titleSuffix: Azure Data Science Virtual Machine
description: 深入瞭解預先安裝在資料科學虛擬機器上的機器學習工具和架構。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 087679c49c3cc025268e6f895757ae5f5c47c917
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014205"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Azure 資料科學虛擬機器上的機器學習和資料科學工具
Azure 資料科學虛擬機器 (Dsvm) 有一組豐富的工具和程式庫，適用于適用于熱門語言的機器學習，例如 Python、R 和 Julia。

以下是 Dsvm 上的一些機器學習工具和程式庫。

## <a name="azure-machine-learning-sdk-for-python"></a>適用於 Python 的 Azure Machine Learning SDK

請參閱適用于 [Python AZURE MACHINE LEARNING SDK](../overview-what-is-azure-ml.md)的完整參考。

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   |   Azure Machine Learning 是一項雲端服務，可用來開發和部署機器學習模型。 您可以使用 Python SDK，在進行模型建置、定型、調整規模及管理時加以追蹤。 將模型部署成容器，並在雲端、內部部署環境或 Azure IoT Edge 上執行。   |
| 支援的版本     | Windows (Conda 環境：AzureML)、Linux (Conda 環境：py36)    |
| 典型的使用案例      | 一般機器學習平臺      |
| 如何設定或安裝它？      |  搭配 GPU 支援安裝   |
| 如何使用或執行它      | 作為 Python SDK 和 Azure CLI 中。 啟動至 Conda 環境 `AzureML` (Windows 版)「或」啟動至 `py36` (Linux 版)。      |
| 範例的連結      | 範例 Jupyter Notebook 包含在 `AzureML` 目錄中的 Notebook 底下。  |
| 相關工具      | Visual Studio Code、Jupyter   |

## <a name="h2o"></a>H2O

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 開放原始碼 AI 平臺，支援記憶體內部、分散式、快速且可調整的機器學習服務。  |
| 支援的版本      | Linux   |
| 典型的使用案例      | 一般用途的分散式、可調整的機器學習   |
| 如何設定或安裝它？      | H2O 是安裝在 `/dsvm/tools/h2o` 中。      |
| 如何使用或執行它      | 使用 X2Go 來連線至 VM。 啟動新的終端機，然後執行 `java -jar /dsvm/tools/h2o/current/h2o.jar`。 然後啟動網頁瀏覽器，並連線至 `http://localhost:54321`。      |
| 範例的連結      | VM 上 Jupyter 中的 `h2o` 目錄底下有提供範例。      |
| 相關工具      | Apache Spark、MXNet、XGBoost、Sparkling Water、Deep Water    |

Dsvm 上還有其他幾個機器學習程式庫，例如 `scikit-learn` dsvm 的 Anaconda Python 散發套件中的熱門套件。 若要查看以 Python、R 及 Julia 提供的套件清單，請執行個別的套件管理員。

## <a name="lightgbm"></a>LightGBM

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 以決策樹演算法為基礎的快速、分散式高效能漸層提升 (GBDT、GBRT、GBM 或超市) 架構。 它用於排名、分類以及許多其他機器學習工作。    |
| 支援的版本      | Windows、Linux    |
| 典型的使用案例      | 一般用途的漸層提升架構      |
| 如何設定或安裝它？      | 在 Windows 上，LightGBM 是以 Python 套件的形式安裝。 在 Linux 上，命令列可執行檔位於 `/opt/LightGBM/lightgbm`，R 套件和 Python 套件皆已安裝。     |
| 範例的連結      | [LightGBM 指南](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| 相關工具      | MXNet、XgBoost  |

## <a name="rattle"></a>Rattle
| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   |   使用 R 進行資料採礦的圖形化使用者介面。   |
| 支援的版本     | Windows、Linux     |
| 典型的使用案例      | 適用于 R 的一般 UI 資料採礦工具    |
| 如何使用或執行它      | 作為 UI 工具。 在 Windows 上，啟動命令提示字元，執行 R，然後在 R 內執行 `rattle()` 。 在 Linux 上，使用 X2Go 連線、啟動終端機、執行 R，然後在 R 內執行 `rattle()` 。 |
| 範例的連結      | [Rattle](https://togaware.com/onepager/) |
| 相關工具      |LightGBM、Weka、XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   |   快速、開放原始碼的跨核心學習系統程式庫    |
| 支援的版本     | Windows、Linux     |
| 典型的使用案例      | 一般機器學習程式庫      |
| 如何設定或安裝它？      |  Windows： msi 安裝程式<br/>Linux： apt-get |
| 如何使用或執行它      | 作為路徑上命令列工具 (`C:\Program Files\VowpalWabbit\vw.exe` (在 Windows 上)、`/usr/bin/vw` (在 Linux 上))    |
| 範例的連結      | [VowPal Wabbit 範例](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) \(英文\) |
| 相關工具      |LightGBM、MXNet、XGBoost   |


## <a name="weka"></a>Weka
| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   |  適用于資料採礦工作的機器學習演算法集合。 您可以將這些演算法直接套用至資料集，也可以從您自己的 Java 程式碼呼叫它們。 Weka 包含資料前置處理、分類、迴歸、群集、關聯規則和視覺效果的工具。 |
| 支援的版本     | Windows、Linux     |
| 典型的使用案例      | 一般機器學習工具     |
| 如何使用或執行它      | 在 Windows 上，于 [ **開始** ] 功能表中搜尋 Weka。 在 Linux 上，使用 X2Go 登入，然後移至 [**應用程式**  >  **開發**]  >  **Weka**。 |
| 範例的連結      | [Weka 範例](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) \(英文\) |
| 相關工具      |LightGBM、Rattle、XGBoost   |

## <a name="xgboost"></a>XGBoost 
| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   |   適用于 Python、R、JAVA、Scala、c + + 等的快速、可攜且分散式梯度提升 (GBDT、GBRT 或 GBM) 程式庫。 它會在單一電腦上執行，並在 Apache Hadoop 和 Spark 上執行。    |
| 支援的版本     | Windows、Linux     |
| 典型的使用案例      | 一般機器學習程式庫      |
| 如何設定或安裝它？      |  搭配 GPU 支援安裝   |
| 如何使用或執行它      | 作為 Python 程式庫 (2.7 和 3.5) 、R 套件，以及 `C:\dsvm\tools\xgboost\bin\xgboost.exe` 適用于 Windows 和 Linux 的內部路徑命令列工具 (`/dsvm/tools/xgboost/xgboost`)     |
| 範例的連結      | 範例包含在 VM 的 `/dsvm/tools/xgboost/demo`(適用於 Linux) 中，以及 `C:\dsvm\tools\xgboost\demo` (適用於 Windows) 中。   |
| 相關工具      | LightGBM、MXNet   |

## <a name="apache-drill"></a>Apache 深入探詢
| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 大型資料的開放原始碼 SQL 查詢引擎    |
| 支援的 DSVM 版本      | Windows 2019、Linux  |
| 如何在 DSVM 上設定和安裝？      |  僅在 `/dsvm/tools/drill*` 內嵌模式中安裝   |
| 典型的使用案例      |  適用于就地資料探索，而不需要解壓縮、轉換、載入 (ETL) 。 查詢不同的資料來源和格式，包括 CSV、JSON、關聯式資料表和 Hadoop。     |
| 如何使用和執行      | 桌面快捷方式  <br/> [在 10 分鐘內開始使用深入探詢](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| DSVM 上的相關工具      |   Rattle、Weka、SQL Server Management Studio      |