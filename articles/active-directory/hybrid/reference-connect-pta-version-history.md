---
title: Azure AD 傳遞驗證：版本發行歷程記錄 |Microsoft Docs
description: 本文列出 Azure AD 傳遞驗證代理程式的所有版本
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75b127f8429650d46af9f171ed7ff03692f1499e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81379909"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD 傳遞驗證代理程式：版本發行歷程記錄 
 
在內部部署安裝的代理程式（可啟用傳遞驗證）會定期更新，以提供新功能。 本文列出引進新功能時所新增的版本和功能。 發行新版本時，會自動更新傳遞驗證代理程式。 

以下是相關主題： 

- [使用 Azure AD 傳遞驗證的使用者登入](how-to-connect-pta.md) 
- [Azure AD 傳遞驗證代理程式安裝](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>發行狀態： 
04/09/2020：已發行以供下載

### <a name="new-features-and-improvements"></a>新功能和改進

- 已新增在安裝時將目標設為雲端環境的支援。 配套可以釘選到指定的雲端環境。



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>發行狀態 
1/22/2019：已發行以供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- 新增服務匯流排可靠通道的支援，為輸出連線新增另一層連線恢復功能 
- 在代理程式註冊期間強制使用 TLS 1。2 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>發行狀態 
4/10/2018：已發行以供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- Web 通訊端連接支援 
- 將 TLS 1.2 設定為代理程式的預設通訊協定 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>發行狀態 
1/31/2018：已發行以供下載  
### <a name="fixed-issues"></a>已修正的問題 

- 已修正導致代理程式發生記憶體流失的 bug。 
- 已更新 Azure 服務匯流排版本，其中包含連接器超時問題的錯誤修正。 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>發行狀態 
11/26/2017：已發行以供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- 已新增代理程式與 Azure AD 服務之間以 websocket 為基礎之連線的支援，以改善連接恢復功能 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>發行狀態 
11/25/2017：已發行以供下載  
### <a name="fixed-issues"></a>已修正的問題 
- 已修正與 DNS 快取有關預設 proxy 案例的錯誤 
 
## <a name="153890"></a>1.5.389.0 版 
### <a name="release-status"></a>發行狀態 
10/17/2017：已發行以供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- 已新增輸出連線的 DNS 快取功能，以從 DNS 失敗中新增復原 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>發行狀態 
08/31/2017：已發行以供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- GA 版本的 Azure AD 傳遞驗證代理程式 

## <a name="next-steps"></a>後續步驟

- [使用 Azure Active Directory 傳遞驗證來進行使用者登入](how-to-connect-pta.md)