---
title: Azure Active Directory 作業參考指南
description: 此操作參考指南說明保護和維護身分識別和存取管理、驗證、治理和作業時，應採取的檢查和動作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74535310"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Azure Active Directory 作業參考指南

此操作參考指南說明保護和維護下欄區域時應採取的檢查和動作：

- 身分**[識別與存取管理](active-directory-ops-guide-iam.md)**-管理身分識別和其權利之生命週期的能力。
- **[驗證管理](active-directory-ops-guide-auth.md)**-能夠根據企業安全性狀態來管理認證、定義驗證體驗、委派指派、測量使用方式，以及定義存取原則。
- **[治理](active-directory-ops-guide-govern.md)**-能夠評估和證明存取權已授與非許可權和特殊許可權身分識別、audit 及控制環境的變更。
- **[作業](active-directory-ops-guide-ops.md)**-將作業優化 Azure Active Directory （Azure AD）。

這裡的一些建議可能不適用於所有客戶的環境，例如，如果您的組織使用密碼雜湊同步處理，則 AD FS 最佳作法可能不適用。

> [!NOTE]
> 這些建議是在發行日期之後的最新版本，但可能會隨著時間而改變。 組織應持續評估其身分識別實務，因為 Microsoft 產品和服務會隨著時間而演進。 當組織訂閱不同的 Azure AD Premium 授權時，建議可能會變更。 例如，Azure AD Premium P2 會包含更多治理建議。

## <a name="stakeholders"></a>方面

本參考指南中的每一節都建議指派專案關係人，以順利規劃和執行關鍵工作。 下表列出本指南中所有專案關係人的清單：

| 利害關係人 | 描述 |
| :- | :- |
| IAM 作業小組 | 這個小組負責管理身分識別與存取管理系統的日常作業 |
| 生產力小組 | 這個小組擁有和管理生產力應用程式，例如電子郵件、檔案共用和共同作業、立即訊息和會議。 |
| 應用程式擁有者 | 這個小組擁有來自企業的特定應用程式，而且通常是組織中的技術觀點。 |
| InfoSec 架構團隊 | 這個小組會規劃及設計組織的資訊安全性實務。 |
| InfoSec 營運小組 | 這個小組會執行並監視 InfoSec 架構小組的已實施資訊安全性實務。 |

## <a name="next-steps"></a>後續步驟

開始使用身分[識別和存取管理檢查和動作](active-directory-ops-guide-iam.md)。
