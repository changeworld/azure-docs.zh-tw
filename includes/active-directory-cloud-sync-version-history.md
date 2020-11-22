---
ms.openlocfilehash: 46c27fa2073ea1178fdf01ea3cbf96fe86954a6a
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255101"
---
本文列出已發行 Azure Active Directory Connect 布建代理程式的版本和功能。 Azure AD 團隊會定期以新的特性和功能更新布建代理程式。 發行新版本時，會自動更新布建代理程式。 

Microsoft 提供最新代理程式版本的直接支援，以及之前的一個版本。

## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>發行狀態

2020年11月 XX 日：已發行以供下載

### <a name="new-features-and-improvements"></a>新功能和改進

* 對[gMSA](../articles/active-directory/cloud-provisioning/how-to-prerequisites.md#group-managed-service-accounts)的支援
* 在差異同步處理期間，最大可支援1500個成員的群組 () 
* 支援成員大小高達15K 的大型群組
* 初始同步改進
* Advanced verbose 記錄
* [AADCloudSyncTools PowerShell 模組](../articles/active-directory/cloud-provisioning/reference-powershell.md)簡介
* 已修正允許將代理程式安裝在非英文伺服器的限制
* 只有範圍 (中的物件才支援 PHS 篩選，我們會同步處理所有物件的密碼雜湊) 
* 已修正代理程式中的記憶體流失問題
* 改進的布建記錄


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>發行狀態

2019年12月4日：已發行供下載

### <a name="new-features-and-improvements"></a>新功能和改進

* 包含 [Azure AD Connect 雲端](../articles/active-directory/cloud-provisioning/what-is-cloud-provisioning.md) 布建的支援，可將使用者、連絡人和群組資料從內部部署 Active Directory 同步處理至 Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>發行狀態

2019年9月9日：已發行以供自動更新

### <a name="new-features-and-improvements"></a>新功能和改進

* 能夠設定額外的追蹤和記錄來偵測布建代理程式問題
* 只提取對應中所設定的 Azure AD 屬性，以改善同步處理效能的能力

### <a name="fixed-issues"></a>已修正的問題

* 修正當 Azure AD 連接失敗時，代理程式進入沒有回應狀態的錯誤（bug）
* 修正從 Azure Active Directory 讀取二進位資料時，造成問題的錯誤（bug）
* 修正代理程式無法更新與雲端混合式身分識別服務信任的 bug

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>發行狀態

2019年1月23日：已發行供下載

### <a name="new-features-and-improvements"></a>新功能和改進

* 改頭換面布建代理程式和連接器架構，以獲得更佳的效能、穩定性和可靠性 
* 使用 UI 驅動的安裝 wizard 簡化布建代理程式設定 
* 新增自動代理程式更新的支援


