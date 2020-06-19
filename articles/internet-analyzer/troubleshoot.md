---
title: Azure Internet Analyzer 疑難排解 | Microsoft Docs
description: Azure Internet Analyzer 的疑難排解參考。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 0c9e203d7e8be7b24c711f73e2152a7745a57dac
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745474"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure Internet Analyzer 疑難排解

本文包含常見 Internet Analyzer 問題的疑難排解步驟。

## <a name="things-to-keep-in-mind"></a>要牢記在心的事項
- 用戶端指令碼必須內嵌到 **HTTPS** 網站中。 如果指令碼在純文字 (**http://** ) 或本機 (**file://** ) 網站中執行，則不會收集測量。
- 只有當 Internet Analyzer 設定檔的用戶端指令碼已內嵌至接收實際使用者流量的應用程式時，才會收集測量資料。 綜合流量 (例如，Azure WebApp 效能測試) 通常不會執行內嵌的 Javascript 程式碼，因此該類型的流量不會產生任何測量。

## <a name="azure-portal"></a>Azure 入口網站
**計分卡區段中的「尚未針對所選的篩選條件組合產生計分卡」**
- 每天都會產生計分卡 (在每天結束時，UTC 時間)。
- 只有針對所選的篩選條件組合 (測試、時間週期、國家/地區等) 收集超過 100 個測量時，才會產生計分卡。

**測試中一或兩個端點的「總測量計數」為零**
- 時間序列和測量計數會每小時計算一次，因此您必須至少等候這段時間，新的測量資料才會顯示。
- Internet Analyzer 只會計算其分析的成功測量 (例如 HTTP 200 回應)。 如果測試中有一或兩個端點無法觸及或傳回非 200 的 HTTP 代碼，則會以零總測量來顯示。

## <a name="next-steps"></a>後續步驟
閱讀 [Internet Analyzer 常見問題集](internet-analyzer-faq.md)
