---
title: Microsoft 安全性程式碼分析版本
description: 本文說明即將發行的 Microsoft Security Code 分析擴充功能
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 33ce2a496caa52609d8bdf8c92e29064ca4ae349
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85362035"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft 安全性程式碼分析版本和藍圖

Microsoft 安全性程式碼分析小組與開發人員支援合作，很榮幸地宣佈 MSCA 延伸模組最近和即將推出的增強功能。


## <a name="credential-scanner-v20-released-in-april-2020"></a>認證掃描器2.0 版：于2020年4月發行

### <a name="innovations--improvements"></a>創新 & 改進

- **核心引擎**

   - 接近線性執行時間的平均效能升級（25%）
   - 以內容/辨識項為基礎的搜尋和排名，以提高精確度
   - 改善一般密碼偵測和明顯預留位置的相符邏輯 (例如，fakePassword) 

- **涵蓋範圍** -支援25種以上的秘密類型，包括下列最上層要求：

   - 網狀架構帳戶憑證複雜密碼
   - 用戶端密碼/API 金鑰
   - HTTP 授權標頭
   - Amazon S3 用戶端秘密存取金鑰
   - Azure Active Directory 用戶端存取權杖
   - Azure Function Master/API 金鑰
   - Power BI 存取金鑰
   - Azure Resource Manager 範本密碼模式

- **輸出**

   - 支援 SARIF 2.1 和 CSV 檔案輸出檔案格式

## <a name="binskim-v160-released-in-april-2020"></a>Binskim 等 v 1.6.0：于2020年4月發行

### <a name="improvements"></a>改善項目

- 功能：更新至最終的 SARIF v2 (版本 2.1.16) 。 當您在命令列上傳遞--雜湊時，此更新會啟用結果快取，以遞迴方式分析具有多個掃描目標複本的目錄時，會大幅改善效能。
- BUG 修正：修正 BA2021 中的打字問題。DoNotMarkWritableSectionsAsExecutable 輸出。
- 效能：排除 managed 元件之所有非混合模式的 PDB 載入，包括 IL 程式庫 (預先編譯) 二進位檔的時間。
- 誤否定修正：確認放置於二進位檔的 PDB 實際上符合分析下的二進位檔
- FEATURE：提供--------)  (---------------------
- 錯誤正面修正：跳過產生的 .NET core 原生啟動載入器 exe (的 PDB 導向分析，這不是使用者可控制的程式碼) 。

## <a name="whats-next-in-q3-cy20"></a>第3季 CY20 中的下一步是什麼？

- JAVA 安全性分析工具
- Python 安全性分析工具
- 不起毛來取代 TypeScript 和 JavaScript 的 TS 不起毛
- Resource Manager 範本分析工具

## <a name="tool-deprecation-notification"></a>工具淘汰通知

### <a name="microsoft-security-risk-detection-msrd-is-deprecated-on-june-26-2020"></a>Microsoft 安全性風險偵測 (MSRD) 已在 26 2020 年6月淘汰。

已淘汰的 MSRD 模糊化服務將會取代為適用于 Azure 的開放原始碼自我裝載開發人員模糊化平臺。 此平臺目前正在開發和測試，與許多 Microsoft 核心產品小組合作。 這個模糊化平臺會整合 sanitizers，並允許內建于 CI/CD 管線的自動調整、學習模糊測試，隨著時間隨軟體專案成長。 此平臺的開放原始碼版本已排程于2020的後半部。

## <a name="next-steps"></a>後續步驟

如需如何上架及安裝 Microsoft 安全性程式碼分析的指示，請參閱我們的上線 [和安裝指南](security-code-analysis-onboard.md)。

如果您有更多有關延伸模組和所提供工具的問題，請參閱我們的 [常見問題頁面](security-code-analysis-faq.md)。
