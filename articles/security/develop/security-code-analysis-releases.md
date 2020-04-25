---
title: Microsoft 安全性程式碼分析版本
description: 本文說明 Microsoft 安全性程式碼分析延伸模組即將推出的版本
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
ms.openlocfilehash: d4281d3b6132e551283a71cd1801ef462fbfc68c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146132"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft 安全性程式碼分析版本與藍圖

Microsoft 安全性程式碼分析小組與開發人員支援合作，非常榮幸地宣佈 MSCA 延伸模組最近和即將推出的增強功能。 請參閱下面的藍圖。

![版次](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-in-april-2020"></a>認證掃描器 v2.0：在2020年4月發行

### <a name="innovations--improvements"></a>創新 & 改進

- **核心引擎**

   - 接近線性執行時間的平均效能升級（25%）
   - 內容/以辨識項為基礎的搜尋和排名，以提高準確度
   - 改善一般密碼偵測和明顯預留位置的相符邏輯（例如，fakePassword）

- **涵蓋範圍**-支援25個以上的秘密類型，包括下列要求的前幾個：

   - 網狀架構帳戶憑證複雜密碼
   - 用戶端密碼/API 金鑰
   - HTTP 授權標頭
   - Amazon S3 用戶端秘密存取金鑰
   - Azure Active Directory 用戶端存取權杖
   - Azure 函式主機/API 金鑰
   - Power BI 存取金鑰
   - Azure Resource Manager 範本密碼模式

- **輸出**

   - 支援 SARIF 2.1 和 CSV 檔案輸出檔案格式

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v 1.6.0：發行于2020年4月

### <a name="improvements"></a>改善

- 功能：更新至最終的 SARIF v2 （版本2.1.16）。 這可在命令列上傳遞雜湊時啟用結果快取，以遞迴方式分析具有多份掃描目標的目錄時，大幅改善效能。
- BUG 修正：修正 BA2021 中的打字錯誤。DoNotMarkWritableSectionsAsExecutable 輸出。
- 效能：針對 managed 元件的所有非混合模式排除 PDB 載入，包括 IL 程式庫（預先編譯的時間）二進位檔。
- 錯誤負修正：確認與二進位檔一起放置的 PDB 實際上符合分析下的二進位檔
- 功能：提供--local-symbol-directory 引數來指定其他（本機、非符號伺服器） PDB 的查閱位置
- 誤報修正：針對產生的 .NET core 原生啟動程式 exe （不是使用者可控制的程式碼）略過 PDB 驅動的分析。

## <a name="whats-next-in-fy20"></a>FY20 的下一步是什麼？

- JAVA 安全性分析工具
- Python 安全性分析工具
- 不想要將適用于 TypeScript 和 JavaScript 的 TS 不起毛取代

## <a name="next-steps"></a>後續步驟

如需有關如何上架及安裝 Microsoft 安全性程式碼分析的指示，請參閱我們的上線[和安裝指南](security-code-analysis-onboard.md)。

如果您有更多關於擴充功能和所提供工具的問題，請查看我們的[常見問題頁面](security-code-analysis-faq.md)。
