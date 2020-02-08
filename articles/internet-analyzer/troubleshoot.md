---
title: Azure 網際網路分析器疑難排解 |Microsoft Docs
description: Azure Internet Analyzer 的疑難排解參考。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069212"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure 網際網路分析器疑難排解

本文包含常見的網際網路分析器問題的疑難排解步驟。

## <a name="things-to-keep-in-mind"></a>要牢記在心的事項
- 用戶端腳本必須內嵌至**HTTPS**網站。 如果腳本在純文字（**HTTP://** ）或本機（**file://** ）網站中執行，則不會收集度量。
- 只有當網際網路分析器設定檔的用戶端腳本已內嵌至接收實際使用者流量的應用程式時，才會收集測量資料。 綜合流量（例如，Azure WebApp 效能測試）通常不會執行內嵌的 JAVAscript 程式碼，因此不會由該類型的流量產生任何測量。

## <a name="azure-portal"></a>Azure 入口網站
**[計分卡] 區段中的 [未針對選取的篩選組合產生計分卡]**
- 每日會產生計分卡（在每日結束時，UTC 時間）。
- 只有針對選取的篩選組合（測試、時間週期、國家（地區）等等）收集超過100的度量時，才會產生計分卡。

**測試中的一個或兩個端點的「總測量計數」為零**
- 時間序列和測量計數一小時會計算一次，因此您必須至少等候這段時間，才會顯示新的度量資料。
- 網際網路分析器只會計算其分析的成功度量（例如 HTTP 200 回應）。 如果測試中的一個或兩個端點無法連線或傳回非200的 HTTP 程式碼，則會以零的總量值來顯示。

## <a name="next-steps"></a>後續步驟
閱讀 [Internet Analyzer 常見問題集](internet-analyzer-faq.md)
