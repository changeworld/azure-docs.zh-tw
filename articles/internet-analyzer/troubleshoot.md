---
title: Azure 互聯網分析器故障排除 |微軟文檔
description: Azure Internet 分析器的故障排除參考。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069212"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure 互聯網分析器故障排除

本文包含常見 Internet 分析器問題的故障排除步驟。

## <a name="things-to-keep-in-mind"></a>要牢記在心的事項
- 用戶端腳本必須嵌入到**HTTPS**網站中。 如果腳本在純文字 **（HTTP://）** 或本地 **（file://**） 網站中運行，則不會收集度量值。
- 僅當 Internet Analyzer 設定檔的用戶端腳本已嵌入到接收實際使用者流量的應用程式中時，才會收集測量資料。 合成流量（例如，Azure WebApp 效能測試）通常不會執行嵌入式 JAVAscript 代碼，因此該類型的流量不會生成度量。

## <a name="azure-portal"></a>Azure 入口網站
**計分卡部分中的"尚未為選定的篩選器組合生成計分卡"**
- 計分卡每天生成（在每天結束時，UTC 時間）。
- 僅當為所選篩選器組合（測試、時間段、國家/地區等）收集了 100 多個測量值時，才會生成計分卡。

**測試中一個或兩個端點的"總測量計數"為零**
- 時間序列和測量計數每小時計算一次，因此您需要等待至少該時間才能顯示新的測量資料。
- 互聯網分析器僅計算其分析的成功測量（即 HTTP 200 回應）。 如果測試中的一個或兩個終結點無法訪問或返回非 200 HTTP 代碼，則它們將顯示為零總測量值。

## <a name="next-steps"></a>後續步驟
閱讀[互聯網分析器常見問題解答](internet-analyzer-faq.md)
