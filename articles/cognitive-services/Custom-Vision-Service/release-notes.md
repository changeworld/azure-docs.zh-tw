---
title: 版本資訊 - 自訂視覺服務
titleSuffix: Azure Cognitive Services
description: 從自訂視覺團隊取得新版本的最新資訊。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: pafarley
ms.openlocfilehash: ccf742e5ff612816cae1056ef7bf0e64fc9dbb5b
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616462"
---
# <a name="custom-vision-service-release-notes"></a>自訂視覺服務版本資訊

## <a name="may-2-2019-and-may-10-2019"></a>5月2日，2019和5月10日2019

- 錯誤修正和後端改善

## <a name="may-23-2019"></a>2019 年 5 月 23 日

- 改進與 Azure 訂用帳戶相關的入口網站 UX 體驗，讓您更輕鬆地選取 Azure 目錄。

## <a name="april-18-2019"></a>2019 年 4 月 18 日 

- 已新增視覺 AI 開發工具組的物件偵測匯出。
- UI 調整，包括專案搜尋。

## <a name="april-3-2019"></a>2019年4月3日

- 將每個影像的周框方塊數目限制增加到200。 
- 錯誤修正，包含匯出至 TensorFlow 之模型的大量效能更新。 

## <a name="march-26-2019"></a>2019 年 3 月 26 日

- 自訂視覺服務已在 Azure 上進入正式運作！
- 使用新的機器學習後端新增了先進的訓練功能，以改善效能，特別是在具挑戰性的資料集和更細緻的分類上。 您可以使用先進的訓練來指定定型的計算時間預算，自訂視覺將此處實驗性找出最佳定型和增強設定。 若要快速反復執行，您可以繼續使用現有的快速定型。
- 引進了 3.0 Api。 宣佈即將于2019年10月1日淘汰3.0 之前的 Api。 如需如何開始使用的範例，請參閱 [.net](./quickstarts/image-classification.md)、 [Python](./quickstarts/image-classification.md)、 [Node](./quickstarts/image-classification.md)、 [JAVA](./quickstarts/image-classification.md)或 [Go](./quickstarts/image-classification.md) 的檔快速入門。
- 已將「預設反復專案」取代為 3.0 Api 中的發佈/取消發行。
- 已新增新的模型匯出目標。 Dockerfile 匯出已升級為支援 ARM for Raspberry Pi 3。 已將匯出支援新增至[視覺 AI 開發工具組。](https://visionaidevkit.com/)
- 針對 S0 層，將每個專案的標記限制增加為500。 針對 S0 層，將每個專案的影像限制增加為100000。
- 已移除成人網域。 建議改用一般網域。
- 已宣佈正式運作的 [定價](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) 。  

## <a name="february-25-2019"></a>2019 年 2 月 25 日

- 宣佈 (沒有與 Azure 資源) 相關聯的專案，因為自訂視覺接近完成移至 Azure 公開預覽的專案。 自2019年3月25日起，CustomVision.ai 網站將僅支援查看與 Azure 資源相關聯的專案，例如免費的自訂視覺資源。 從2019年10月1日起，您仍然可以透過自訂視覺 Api 來存取現有的受限試用專案。 這會讓您有時間更新您使用自訂視覺撰寫之任何應用程式的 API 金鑰。 2019年10月1日之後，任何未移至 Azure 的受限試用專案都會遭到刪除。

## <a name="january-22-2019"></a>2019 年 1 月 22 日

- 針對新的 Azure 區域新增的支援：美國西部2、美國東部、美國東部2、西歐、北歐、東南亞、澳大利亞東部、印度中部、英國南部、日本東部和美國中北部。 對於美國中南部繼續提供支援。

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
- 新增「移至 Azure」功能到網站，讓有限的試用版專案升級連結到 Azure 更輕鬆。 資源連結專案 (F0 或 S0. ) 您可以在產品的 [設定] 頁面上找到此專案。  
- 新增匯出至 ONNX 1.2 以支援 Windows ML 的 Windows 2018 十月更新版。
Bug 修正，包括使用特殊字元進行 ONNX 匯出。

## <a name="august-14-2018"></a>2018 年 8 月 14 日

- customvision.ai 網站已新增「開始使用」小工具，可引導使用者進行專案訓練。
- 機器學習服務管線的進一步增強，有利於多標籤專案 (新遺失層)。

## <a name="june-28-2018"></a>2018 年 6 月 28 日

- Bug 修正 & 後端改善。
- 啟用多元分類，適用于影像只有一個標籤的專案。 在多元模式的預測中，機率會加總為 1 (所有影像會在您指定的標記) 之間分類。

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

- 輸入付費預覽並上線至 Azure 入口網站。 專案現在可以連結至 F0 (免費) 或 S0 (標準) 層的 Azure 資源。 導入了 S0 層專案，允許使用多達 100 個標記和 25,000 個影像。
- 後端變更為機器學習管線/正規化參數。 這會讓客戶在調整機率閾值時，對準確率與召回率有更好的取捨控制能力。 在進行這些變更的過程中，CustomVision.ai 入口網站中的預設機率閾值設為 50%。

## <a name="december-19-2017"></a>2017 年 12 月 19 日

- 除了先前發行的匯出至 iOS (CoreML 以外，匯出至 Android (TensorFlow) 。 ) 如此可讓已定型的 compact 模型匯出在應用程式中離線執行。
- 已新增零售和地標「精簡」網域，以啟用這些網域的模型匯出功能。
- 已發行 [1.2 版訓練 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) 和 [1.1 版預測 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164)。 已更新 API 支援模型匯出、不會將影像儲存至「預測」的新預測作業，並為訓練 API 引進了批次作業。
- UX 調整，包括可查看使用了哪些網域來訓練反覆項目。
- 已更新 [C# SDK 和範例](https://github.com/Microsoft/Cognitive-CustomVision-Windows)。