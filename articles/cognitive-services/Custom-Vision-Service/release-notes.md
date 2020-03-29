---
title: 版本資訊 - 自訂視覺服務
titleSuffix: Azure Cognitive Services
description: 從自訂願景團隊獲取有關新版本的最新資訊。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: b9606c63ec7b53fb0b69918c21bdd9206f34c555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647566"
---
# <a name="custom-vision-service-release-notes"></a>自訂視覺服務版本資訊

## <a name="may-2-2019-and-may-10-2019"></a>2019 年 5 月 2 日和 2019 年 5 月 10 日

- 錯誤修復和後端改進

## <a name="may-23-2019"></a>2019年5月23日

- 改進了與 Azure 訂閱相關的門戶使用者體驗，從而更輕鬆地選擇 Azure 目錄。

## <a name="april-18-2019"></a>2019 年 4 月 18 日 

- 為視覺 AI 開發工具組添加了物件檢測匯出。
- UI 調整，包括專案搜索。

## <a name="april-3-2019"></a>2019年4月3日

- 將每張圖像的邊界框數限制增加到 200。 
- 錯誤修復，包括匯出到 TensorFlow 的模型的大量性能更新。 

## <a name="march-26-2019"></a>2019 年 3 月 26 日

- 自訂視覺服務已在 Azure 上輸入了"通用"！
- 添加了高級培訓功能，並添加了新的機器學習後端，以提高性能，尤其是在具有挑戰性的資料集和細細微性分類方面。 通過高級培訓，您可以指定用於培訓的計算時間預算，自訂視覺將實驗性地確定最佳培訓和增強設置。 對於快速反覆運算，您可以繼續使用現有的快速培訓。
- 引入了 3.0 API。 2019 年 10 月 1 日宣佈即將棄用 3.0 API。 有關如何入門的示例，請參閱[.Net、Python、](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial)[節點](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial)[、JAVA](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial)或[Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial)[Go](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial)的文檔快速入門。
- 在 3.0 API 中將"預設反覆運算"替換為發佈/取消發佈。
- 添加了新的模型匯出目標。 Dockerfile 匯出已升級，以支援樹莓派 3 的 ARM。 匯出支援已添加到[願景 AI 開發工具組中。](https://visionaidevkit.com/).
- S0 層每個專案的標記限制增加到 500。 S0 層每個專案的圖像限制增加到 100，000。
- 已刪除成人域。 建議改為常規域。
- 已宣佈的通用可用性[定價](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。  

## <a name="february-25-2019"></a>2019 年 2 月 25 日

- 宣佈有限試用專案（未與 Azure 資源關聯的專案）的結束，因為自訂視覺即將完成其遷移到 Azure 公共預覽。 從 2019 年 3 月 25 日開始，CustomVision.ai網站將僅支援查看與 Azure 資源關聯的專案，例如免費的自訂視覺資源。 到 2019 年 10 月 1 日，您仍可以通過自訂視覺 API 訪問現有的有限試用專案。 這將讓您有時間更新使用自訂 Vision 編寫的任何應用的 API 金鑰。 2019 年 10 月 1 日之後，您尚未移動到 Azure 的任何有限試用專案都將被刪除。

## <a name="january-22-2019"></a>2019 年 1 月 22 日

- 為新的 Azure 區域添加了支援：美國西部 2、美國東部、美國東部 2、西歐、北歐、東南亞、澳大利亞東部、印度中部、英國南部、日本東部和美國中北部。 對於美國中南部繼續提供支援。

## <a name="december-12-2018"></a>2018 年 12 月 12 日

- 支援匯出物件偵測模型 (導入了物件偵測 Compact 網域)。
- 已修正一些協助工具問題，改善螢幕閱讀程式和鍵盤瀏覽支援。
- 影像檢視器的 UX 更新，改善物件偵測標記體驗，以便更快速標記。  
- 更新物件偵測網域的基底模型，以獲得更佳品質的物體偵測。
- 錯誤修正。

## <a name="november-6-2018"></a>2018 年 11 月 6 日

- 新增物件偵測中標誌網域的支援。

## <a name="october-9-2018"></a>2018 年 10 月 9 日

- 物件偵測進入付費預覽。 您現在可以使用 Azure 資源建立物件偵測專案。
- 新增「移至 Azure」功能到網站，讓有限的試用版專案升級連結到 Azure 更輕鬆。 資源連結專案（F0 或 S0。您可以在產品的"設置"頁面上找到此內容。  
- 新增匯出至 ONNX 1.2 以支援 Windows ML 的 Windows 2018 十月更新版。
錯誤修復，包括具有特殊字元的 ONNX 匯出。

## <a name="august-14-2018"></a>2018 年 8 月 14 日

- customvision.ai 網站已新增「開始使用」小工具，可引導使用者進行專案訓練。
- 機器學習服務管線的進一步增強，有利於多標籤專案 (新遺失層)。

## <a name="june-28-2018"></a>2018 年 6 月 28 日

- 錯誤修復&後端改進。
- 啟用多類分類，用於圖像正好具有一個標籤的專案。 在多類模式的預測中，概率將總和為 1（所有圖像都分類到指定的標記中）。

## <a name="june-13-2018"></a>2018 年 6 月 13 日

- UX 重新整理，著重於使用和協助工具的便利性。
- 機器學習管線的步增強，有利於有許多標籤的多標籤專案。
- 修正在 TensorFlow 匯出的錯誤。 啟用匯出模型版本設定，以便可多次匯出反覆項目。

## <a name="may-7-2018"></a>2018 年 5 月 7 日

- 導入了有限試用專案的預覽物體偵測功能。
- 升級為 2.0 API
- S0 層已擴充為多達 250 個標記和 50,000 個影像。
- 影像分類專案的機器學習管線已進行重大的後端功能改進。 在 2018 年 4 月 27 日之後訓練的專案會受益於這些更新。
- 新增了目的地為 ONNX 的模型匯出功能，以用於 Windows ML。
- 新增了目的地為 Dockerfile 的模型匯出功能。 這可讓您下載成品來建置您自己的 Windows 或 Linux 容器，包括 DockerFile、TensorFlow 模型和服務程式碼。
- 對於新訓練出來的模型，如果其匯出至一般 (精簡) 和地標 (精簡) 網域中的 TensorFlow，則[平均值現在為 (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample)，以便讓所有專案保持一致。

## <a name="march-1-2018"></a>2018 年 3 月 1 日

- 輸入付費預覽並登上 Azure 門戶。 專案現在可以連結至 F0 (免費) 或 S0 (標準) 層的 Azure 資源。 導入了 S0 層專案，允許使用多達 100 個標記和 25,000 個影像。
- 後端變更為機器學習管線/正規化參數。 這會讓客戶在調整機率閾值時，對準確率與召回率有更好的取捨控制能力。 在進行這些變更的過程中，CustomVision.ai 入口網站中的預設機率閾值設為 50%。

## <a name="december-19-2017"></a>2017 年 12 月 19 日

- 匯出到 Android （TensorFlow） 除了以前發佈的匯出到 iOS （CoreML.） 之外，這允許匯出經過訓練的緊湊模型在應用程式中離線運行。
- 已新增零售和地標「精簡」網域，以啟用這些網域的模型匯出功能。
- 已發行 [1.2 版訓練 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) 和 [1.1 版預測 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164)。 已更新 API 支援模型匯出、不會將影像儲存至「預測」的新預測作業，並為訓練 API 引進了批次作業。
- UX 調整，包括可查看使用了哪些網域來訓練反覆項目。
- 已更新 [C# SDK 和範例](https://github.com/Microsoft/Cognitive-CustomVision-Windows)。