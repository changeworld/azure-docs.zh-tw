---
title: Azure AD Connect 布建代理程式：版本發行歷程記錄 |Microsoft Docs
description: 本文列出 Azure AD Connect 布建代理程式的所有版本，並說明新功能和已修正的問題
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: 9e05d1a85f17800ddf4d77e4e4acba6396a8da47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84781560"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect 布建代理程式：版本發行歷程記錄

本文列出已發行 Azure Active Directory Connect 布建代理程式的版本和功能。 Azure AD 團隊會定期以新的特性和功能更新布建代理程式。 發行新版本時，會自動更新布建代理程式。 

Microsoft 提供最新代理程式版本的直接支援，以及之前的一個版本。

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>發行狀態

2019年12月4日：已發行供下載

### <a name="new-features-and-improvements"></a>新功能和改進

* 包含 [Azure AD Connect 雲端](../cloud-provisioning/what-is-cloud-provisioning.md) 布建的支援，可將使用者、連絡人和群組資料從內部部署 Active Directory 同步處理至 Azure AD


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

