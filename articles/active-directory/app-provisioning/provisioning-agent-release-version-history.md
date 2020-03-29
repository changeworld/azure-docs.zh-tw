---
title: Azure AD 連接預配代理：版本發佈歷史記錄 |微軟文檔
description: 本文列出了 Azure AD 連接配置代理的所有版本，並介紹了新功能和修復問題
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183239"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD 連接預配代理：版本發佈歷史記錄
本文列出了已發佈的 Azure 活動目錄連接預配代理的版本和功能。 Azure AD 團隊定期更新具有新功能和功能的預配代理。 預配代理在發佈新版本時會自動更新。 

微軟提供最新的代理版本和之前的一個版本的直接支援。

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>發行狀態

2019 年 12 月 4 日：發佈供下載

### <a name="new-features-and-improvements"></a>新功能和改進

* 包括支援[Azure AD 連接雲配置](../cloud-provisioning/what-is-cloud-provisioning.md)，以同步使用者、連絡人和組資料（從本地活動目錄到 Azure AD）


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>發行狀態

2019 年 9 月 9 日：發佈用於自動更新

### <a name="new-features-and-improvements"></a>新功能和改進

* 能夠配置其他跟蹤和日誌記錄以調試預配代理問題
* 能夠僅獲取映射中配置的 Azure AD 屬性，以提高同步性能

### <a name="fixed-issues"></a>已修正的問題

* 修復了代理在 Azure AD 連接故障出現問題時進入無回應狀態的 Bug
* 修復了從 Azure 活動目錄中讀取二進位資料時導致問題的錯誤
* 修復了代理未能與雲混合標識服務續訂信任的錯誤

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>發行狀態

2019 年 1 月 23 日：發佈供下載

### <a name="new-features-and-improvements"></a>新功能和改進

* 改進了配置代理和連接器架構，以更好的性能、穩定性和可靠性 
* 使用 UI 驅動的安裝精靈簡化預配代理配置 
* 添加了對自動代理更新的支援

