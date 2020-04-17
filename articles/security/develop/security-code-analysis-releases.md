---
title: 微軟安全程式分析版本
description: 本文介紹了即將發佈的 Microsoft 安全代碼分析擴展
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a07ce7437d664baca0cfdc310dbc2631f41fdbcc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462031"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>微軟安全代碼分析發佈和路線圖

Microsoft 安全代碼分析團隊與開發人員支援部門合作,自豪地宣佈最近和即將推出的 MSCA 擴展的增強功能。 請參閱下面的路線圖。

![版次](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-on-april-1-2020"></a>認證器 v2.0: 2020 年 4 月 1 日發布

### <a name="innovations--improvements"></a>創新&改進

- **核心引擎**

   - 平均性能提升 25%,接近線性運行時間
   - 基於上下文/證據的搜索和排名,提高準確性
   - 對明顯占位符(例如,假密碼)的一般密碼偵測和符合邏輯的改進

- **覆蓋範圍**- 支援 25 多種機密類型,包括以下頂級請求:

   - 結構帳號憑證密碼短語
   - 用戶端金鑰/API 金鑰
   - HTTP 授權標頭
   - 亞馬遜 S3 用戶端金鑰
   - Azure 動作目錄客戶端存取權杖
   - Azure 函數主/API 金鑰
   - 電源 BI 存取金鑰
   - Azure 資源管理員樣本密碼模式

- **輸出**

   - 支援 SARIF 2.1 與 CSV 檔案輸出檔案格式

## <a name="binskim-v160-to-be-released-on-april-2020"></a>賓斯基姆 v1.6.0: 將於 2020 年 4 月發佈

### <a name="improvements"></a>改善項目

- 特點:更新到最終的 SARIF v2(版本 2.1.16)。 這樣,當傳遞命令行上的哈希時,可實現結果緩存,這在遞歸分析具有多個掃描目標副本的目錄時顯著提高性能。
- BUG 修復:修復 BA2021 中的拼寫錯誤。不可標記可執行節作為可執行輸出。
- 性能:消除託管程式集的所有非混合模式的 PDB 載入,包括 IL 庫(提前編譯)二進位檔案。
- FALSE 否定修復:驗證放置在二進位檔案旁邊的 PDB 是否與正在分析的二進位檔案實際匹配
- 特點:提供 --本地端符號目錄參數,以指定其他(本地、非符號伺服器)PDB 查找位置
- FALSE 積極修復:跳過 PDB 驅動的分析,用於生成的 .NET 核心本機引導 exe(這是使用者可控制的代碼)。

## <a name="whats-next-in-fy20"></a>20 財年的下一步是什麼?

- Java 安全分析工具
- Python 安全分析工具
- ES Lint 用於取代 TS Lint 以進行型態文稿和 JavaScript

## <a name="next-steps"></a>後續步驟

有關如何載入和安裝 Microsoft 安全程式分析的說明,請參閱我們的[載入和安裝指南](security-code-analysis-onboard.md)。

如果您對擴展和提供的工具有任何疑問,請查看我們的[常見問題頁面](security-code-analysis-faq.md)。
