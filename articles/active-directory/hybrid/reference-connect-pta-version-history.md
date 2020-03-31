---
title: Azure AD 直通身份驗證：版本發佈歷史記錄 |微軟文檔
description: 本文列出了 Azure AD 直通身份驗證代理的所有版本
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 11/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c5d0efe8e662544dc69356c6b17dd7eca6f3a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786447"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD 直通身份驗證代理：版本發佈歷史記錄 
 
在本地安裝的啟用直通身份驗證的代理會定期更新以提供新功能。 本文列出了引入新功能時添加的版本和功能。 發佈新版本時，直通身份驗證代理將自動更新。 

以下是相關主題： 

- [使用 Azure AD 直通身份驗證的使用者登錄](how-to-connect-pta.md) 
- [Azure AD 直通身份驗證代理安裝](how-to-connect-pta-quick-start.md) 



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>發行狀態 
2019/1/22：發佈供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- 添加了對服務匯流排可靠通道的支援，為出站連接添加另一層連接恢復能力 
- 在代理註冊期間強制執行 TLS 1.2 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>發行狀態 
2018/4/10：發佈供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- Web 通訊端連接支援 
- 將 TLS 1.2 設置為代理的預設協定 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>發行狀態 
2018/1/31：發佈供下載  
### <a name="fixed-issues"></a>已修正的問題 

- 修復了導致代理中某些記憶體洩漏的 Bug。 
- 更新了 Azure 服務匯流排版本，其中包括連接器超時問題的錯誤修復程式。 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>發行狀態 
2017/11/26：發佈供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- 添加了對代理和 Azure AD 服務之間基於 Websocket 的連接的支援，以提高連接恢復能力 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>發行狀態 
2017/11/25：發佈供下載  
### <a name="fixed-issues"></a>已修正的問題 
- 修復了與預設代理方案的 DNS 緩存相關的 Bug 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>發行狀態 
2017/10/17：發佈供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- 為出站連接添加 DNS 緩存功能，以添加 DNS 故障的恢復能力 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>發行狀態 
08/31/2017： 發佈供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- Azure AD 直通身份驗證代理的 GA 版本 

## <a name="next-steps"></a>後續步驟

- [使用 Azure Active Directory 傳遞驗證來進行使用者登入](how-to-connect-pta.md)