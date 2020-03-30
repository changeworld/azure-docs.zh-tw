---
title: 機器學習與資料科學工具
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解預先安裝在資料科學虛擬機器上的機器學習工具和框架。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c414087270558e21340e50114c0563ff7e50064c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282302"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Azure 資料科學虛擬機器上的機器學習和資料科學工具
Azure 資料科學虛擬機器 （DSVM） 具有一組豐富的用於機器學習的工具和庫，可通過常用語言（如 Python、R 和 Julia）提供。

以下是 DSVM 上的一些機器學習工具和庫。

## <a name="azure-machine-learning-sdk-for-python"></a>適用於 Python 的 Azure Machine Learning SDK

請參閱 Python 的[Azure 機器學習 SDK](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)的完整參考。

|    |           |
| ------------- | ------------- |
| 這是什麼？   |   Azure 機器學習是一種雲服務，可用於開發和部署機器學習模型。 您可以使用 Python SDK，在進行模型建置、定型、調整規模及管理時加以追蹤。 將模型部署成容器，並在雲端、內部部署環境或 Azure IoT Edge 上執行。   |
| 支援的版本     | Windows (Conda 環境：AzureML)、Linux (Conda 環境：py36)    |
| 典型的使用案例      | 通用機器學習平臺      |
| 如何設定或安裝它？      |  搭配 GPU 支援安裝   |
| 如何使用或執行它      | 作為 Python SDK 和 Azure CLI 中。 啟動至 Conda 環境 `AzureML` (Windows 版)「或」** 啟動至 `py36` (Linux 版)。      |
| 範例的連結      | 範例 Jupyter Notebook 包含在 `AzureML` 目錄中的 Notebook 底下。  |
| 相關工具      | Visual Studio Code、Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 支援記憶體中、分散式、快速和可擴展機器學習的開源 AI 平臺。  |
| 支援的版本      | Linux   |
| 典型的使用案例      | 通用分散式、可擴展的機器學習   |
| 如何設定或安裝它？      | H2O 是安裝在 `/dsvm/tools/h2o` 中。      |
| 如何使用或執行它      | 使用 X2Go 來連線至 VM。 啟動新的終端機，然後執行 `java -jar /dsvm/tools/h2o/current/h2o.jar`。 然後啟動網頁瀏覽器，並連線至 `http://localhost:54321`。      |
| 範例的連結      | VM 上 Jupyter 中的 `h2o` 目錄底下有提供範例。      |
| 相關工具      | Apache Spark、MXNet、XGBoost、Sparkling Water、Deep Water    |

DSVM 上還有其他幾個機器學習庫，例如作為 DSVM Anaconda Python 分發的一部分的流行`scikit-learn`包。 若要查看以 Python、R 及 Julia 提供的套件清單，請執行個別的套件管理員。

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 基於決策樹演算法的快速、分散式、高性能梯度提升（GBDT、GBRT、GBM 或 MART）框架。 它用於排名、分類和許多其他機器學習任務。    |
| 支援的版本      | Windows、Linux    |
| 典型的使用案例      | 通用梯度促進框架      |
| 如何設定或安裝它？      | 在 Windows 上，LightGBM 是以 Python 套件的形式安裝。 在 Linux 上，命令列可執行檔位於 `/opt/LightGBM/lightgbm`，R 套件和 Python 套件皆已安裝。     |
| 範例的連結      | [光GBM指南](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| 相關工具      | MXNet、XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 這是什麼？   |   使用 R 進行資料採礦的圖形化使用者介面。   |
| 支援的版本     | Windows、Linux     |
| 典型的使用案例      | R 的通用 UI 資料採礦工具    |
| 如何使用或執行它      | 作為 UI 工具。 在 Windows 上，啟動命令提示符，運行 R，然後在`rattle()`R 內部運行 。 在 Linux 上，與 X2Go 連接，啟動終端，運行 R，然後在`rattle()`R 內部運行 。 |
| 範例的連結      | [Rattle](https://togaware.com/onepager/) \(英文\) |
| 相關工具      |LightGBM、Weka、XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| 這是什麼？   |   快速、開源、核心外學習系統庫    |
| 支援的版本     | Windows、Linux     |
| 典型的使用案例      | 通用機器學習庫      |
| 如何設定或安裝它？      |  視窗：msi 安裝程式<br/>Linux： 貼切獲取 |
| 如何使用或執行它      | 作為路徑上命令列工具 (`C:\Program Files\VowpalWabbit\vw.exe` (在 Windows 上)、`/usr/bin/vw` (在 Linux 上))    |
| 範例的連結      | [VowPal Wabbit 範例](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) \(英文\) |
| 相關工具      |LightGBM、MXNet、XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 這是什麼？   |  用於資料採礦任務的機器學習演算法的集合。 您可以將這些演算法直接套用至資料集，也可以從您自己的 Java 程式碼呼叫它們。 Weka 包含資料前置處理、分類、迴歸、群集、關聯規則和視覺效果的工具。 |
| 支援的版本     | Windows、Linux     |
| 典型的使用案例      | 一般機器學習工具     |
| 如何使用或執行它      | 在 Windows 上，在 **"開始"** 功能表上搜索 Weka。 在 Linux 上，使用 X2Go 登錄，然後轉到**應用程式** > **開發** > **Weka**。 |
| 範例的連結      | [Weka 範例](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) \(英文\) |
| 相關工具      |LightGBM、Rattle、XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| 這是什麼？   |   適用于 Python、R、JAVA、Scala、C++ 等的快速、可移植和分散式梯度增強庫（GBDT、GBRT 或 GBM）。 它在一台機器上運行，在阿帕奇哈多普和火花上運行。    |
| 支援的版本     | Windows、Linux     |
| 典型的使用案例      | 通用機器學習庫      |
| 如何設定或安裝它？      |  搭配 GPU 支援安裝   |
| 如何使用或執行它      | 作為 Python 庫（2.7 和 3.5），R 包和路徑命令列工具（`C:\dsvm\tools\xgboost\bin\xgboost.exe`適用于 Windows`/dsvm/tools/xgboost/xgboost`和 Linux）    |
| 範例的連結      | 範例包含在 VM 的 `/dsvm/tools/xgboost/demo`(適用於 Linux) 中，以及 `C:\dsvm\tools\xgboost\demo` (適用於 Windows) 中。   |
| 相關工具      | LightGBM、MXNet   |

## <a name="apache-drill"></a>Apache 深入探詢
|    |           |
| ------------- | ------------- |
| 這是什麼？   | 大資料上的開源 SQL 查詢引擎    |
| 支援的 DSVM 版本      | 視窗 2019， Linux  |
| 它是如何在 DSVM 上配置和安裝的？      |  僅在 `/dsvm/tools/drill*` 內嵌模式中安裝   |
| 典型的使用案例      |  用於就地資料探索，無需提取、轉換、負載 （ETL）。 查詢不同的資料來源和格式，包括 CSV、JSON、關係表和 Hadoop。     |
| 如何使用和運行它      | 桌面快捷方式  <br/> [在 10 分鐘內開始使用深入探詢](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| DSVM 的相關工具      |   Rattle、Weka、SQL Server Management Studio      |


