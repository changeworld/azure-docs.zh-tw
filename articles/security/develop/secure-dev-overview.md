---
title: 在 Microsoft Azure 上保護開發最佳實踐
description: 説明您開發更安全的代碼並在雲中部署更安全的應用程式的最佳做法。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c4314a0dcbbcb907ef4d6de0a2788cf04dfe1641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934857"
---
# <a name="secure-development-best-practices-on-azure"></a>在 Azure 上保護開發最佳實踐
本系列文章介紹了為雲開發應用程式時需要考慮的安全活動和控制措施。 介紹了 Microsoft 安全開發生命週期 （SDL） 的各個階段以及生命週期每個階段要考慮的安全問題和概念。 目標是説明您定義可在生命週期每個階段用於設計、開發和部署更安全應用程式的活動和 Azure 服務。

文章中的建議來自我們對 Azure 安全性的體驗以及客戶的經驗。 您可以使用這些文章作為開發專案特定階段應考慮的參考，但我們建議您至少從頭到尾閱讀所有文章。 閱讀所有文章將介紹您可能在專案早期階段遺漏的概念。 在發佈產品之前實現這些概念可以説明您構建安全軟體、滿足安全合規性要求並降低開發成本。

這些文章旨在成為構建和部署安全 Azure 應用程式的所有級別的軟體設計人員、開發人員和測試人員的資源。

## <a name="overview"></a>總覽

安全性是任何應用程式最重要的方面之一，它不是一件簡單的事。 幸運的是，Azure 提供了許多服務，可説明您在雲中保護應用程式。 這些文章解決了可以在軟體發展生命週期的每個階段實現的活動和 Azure 服務，以説明您開發更安全的代碼並在雲中部署更安全的應用程式。

## <a name="security-development-lifecycle"></a>安全性開發生命週期

遵循安全軟體發展的最佳實踐需要將安全性集成到軟體發展生命週期的每個階段，從需求分析到維護，而不管專案方法（[瀑布](https://en.wikipedia.org/wiki/Waterfall_model)、[敏捷](https://en.wikipedia.org/wiki/Agile_software_development)或[DevOps）。](https://en.wikipedia.org/wiki/DevOps) 在高調資料洩露和利用操作安全性漏洞之後，越來越多的開發人員認識到，在整個開發過程中需要解決安全性問題。

越晚修復開發生命週期中的問題，修復成本就越多。 安全問題也不例外。 如果忽略軟體發展早期階段的安全問題，則以下每個階段可能會繼承前一階段的漏洞。 您的最終產品將累積多個安全問題和違規的可能性。 將安全性構建到開發生命週期的每個階段可説明您及早發現問題，並説明您降低開發成本。

我們遵循 Microsoft[安全開發生命週期 （SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的各個階段，介紹可用於在生命週期每個階段實現安全軟體發展實踐的活動和 Azure 服務。

SDL 階段包括：

  - 訓練
  - 需求
  - 設計
  - 實作
  - 驗證
  - 版本
  - 回應

![安全性開發生命週期](./media/secure-dev-overview/01-sdl-phase.png)

在這些文章中，我們將 SDL 階段分組到設計、開發和部署中。

## <a name="engage-your-organizations-security-team"></a>與組織的安全團隊接洽

您的組織可能具有正式的應用程式安全程式，在開發生命週期中從頭到尾説明您開展安全活動。 如果您的組織有安全和合規團隊，請務必在開始開發應用程式之前與他們接洽。 在 SDL 的每個階段詢問他們是否有任何任務您遺漏了。

我們理解，許多讀者可能沒有安全或合規團隊參與。 這些文章可以説明您指導您在 SDL 每個階段需要考慮的安全問題和決策。

## <a name="resources"></a>資源

使用以下資源瞭解有關開發安全應用程式的更多資訊，並説明保護 Azure 上的應用程式：

[微軟安全開發生命週期 （SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – SDL 是微軟的軟體發展過程，可説明開發人員構建更安全的軟體。 它可以説明您滿足安全合規性要求，同時降低開發成本。

[開放 Web 應用程式安全專案 （OWASP）](https://www.owasp.org/index.php/Main_Page) – OWASP 是一個線上社區，可在 Web 應用程式安全領域生成免費提供的文章、方法、文檔、工具和技術。

[向左推，像老闆一樣](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca)——一系列線上文章，概述了開發人員應完成不同類型的應用程式安全活動，以創建更安全的代碼。

[微軟標識平臺](../../active-directory/develop/index.yml)– 微軟標識平臺是 Azure AD 標識服務和開發人員平臺的演變。 它是一個功能齊全的平臺，由身份驗證服務、開源庫、應用程式註冊和配置、完整的開發人員文檔、代碼示例和其他開發人員內容組成。 Microsoft 標識平臺支援行業標準協定，如 OAuth 2.0 和 OpenID 連接。

[Azure 解決方案的安全最佳實踐](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/)– 使用 Azure 設計、部署和管理雲解決方案時要使用的安全最佳實踐集合。 本文旨在成為 IT 專業人員的資源。 其中可包括建置和部署安全 Azure 解決方案的設計人員、架構設計師、開發人員和測試人員。

[Azure 上的安全和合規性藍圖](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview)– Azure 安全和合規性藍圖是可説明您構建和啟動符合嚴格法規和標準的雲支援應用程式的資源。

## <a name="next-steps"></a>後續步驟
在以下文章中，我們建議安全控制和活動，以説明您設計、開發和部署安全應用程式。

- [設計安全應用程式](secure-design.md)
- [開發安全的應用程式](secure-develop.md)
- [部署安全應用程式](secure-deploy.md)
