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
ms.openlocfilehash: 854931273ffebd653615a44dbfa9ce1b846655c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333421"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD 傳遞驗證代理程式：版本發行歷程記錄 
 
在內部部署環境中啟用傳遞驗證的代理程式會定期更新以提供新功能。 本文列出引進新功能時所加入的版本和功能。 發行新版本時，傳遞驗證代理程式會自動更新。 

以下是相關主題： 

- [使用 Azure AD 傳遞驗證進行使用者登入](how-to-connect-pta.md) 
- [Azure AD 傳遞驗證代理程式安裝](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>發行狀態： 
2020/04/09：已發行可供下載

### <a name="new-features-and-improvements"></a>新功能和改進

- 已在安裝時新增目標為雲端環境的支援。 組合可以釘選到指定的雲端環境。



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>發行狀態 
1/22/2019：已發行以供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- 新增服務匯流排可靠通道的支援，以針對輸出連線新增另一層連線恢復功能 
- 在代理程式註冊期間強制執行 TLS 1。2 

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
- 修正在代理程式中造成記憶體流失的 bug。 
- 已更新 Azure 服務匯流排版本，其中包含連接器超時問題的錯誤修正。 
### <a name="new-features-and-improvements"></a>新功能和改進 
- 已新增代理程式與 Azure AD 服務之間以 websocket 為基礎的連線支援，以改善連線恢復功能

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>發行狀態 
11/25/2017：已發行以供下載  
### <a name="fixed-issues"></a>已修正的問題 
- 修正與預設 proxy 案例的 DNS 快取相關的錯誤 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>發行狀態 
10/17/2017：已發行以供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- 已新增輸出連線的 DNS 快取功能，以新增 DNS 失敗的恢復功能 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>發行狀態 
08/31/2017：已發行以供下載  
### <a name="new-features-and-improvements"></a>新功能和改進 
- Azure AD 傳遞驗證代理程式的 GA 版本 

## <a name="next-steps"></a>後續步驟

- [使用 Azure Active Directory 傳遞驗證來進行使用者登入](how-to-connect-pta.md)
