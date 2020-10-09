---
title: Azure Active Directory 作業參考指南
description: 此操作參考指南說明保護和維護身分識別和存取管理、驗證、治理和作業所應採取的檢查和動作
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74535310"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Azure Active Directory 作業參考指南

此操作參考指南說明保護和維護下列領域所應採取的檢查和動作：

- 身分**[識別和存取管理](active-directory-ops-guide-iam.md)**-能夠管理身分識別的生命週期及其權利。
- **[驗證管理](active-directory-ops-guide-auth.md)** -能夠管理認證、定義驗證經驗、委派指派、測量使用量，以及根據企業安全性狀態定義存取原則。
- **[治理](active-directory-ops-guide-govern.md)** ：評定和證明存取權的能力，授與非特殊許可權和特殊許可權的身分識別，並對環境進行審核及控制變更。
- **[作業](active-directory-ops-guide-ops.md)** -將作業優化 Azure Active Directory (Azure AD) 。

這裡的某些建議可能不適用於所有客戶的環境，例如，如果您的組織使用密碼雜湊同步，AD FS 的最佳作法可能不適用。

> [!NOTE]
> 這些建議是從發行日期開始的最新狀態，但可能會隨著時間而改變。 組織應持續評估其身分識別實務，因為 Microsoft 產品和服務會隨著時間而演進。 當組織訂閱不同的 Azure AD Premium 授權時，建議可能會變更。 例如，Azure AD Premium P2 會包含更多治理建議。

## <a name="stakeholders"></a>利益 相關 者

本參考指南中的每一節都會建議指派專案關係人，以順利規劃和執行重要工作。 下表列出本指南中所有專案關係人的清單：

| 利害關係人 | 描述 |
| :- | :- |
| IAM 作業小組 | 此小組負責管理身分識別和存取管理系統的日常作業 |
| 生產力小組 | 此小組擁有及管理生產力應用程式，例如電子郵件、檔案共用與共同作業、立即訊息和會議。 |
| 應用程式擁有者 | 此小組擁有公司的特定應用程式，而且通常是組織的技術觀點。 |
| InfoSec 架構小組 | 此小組會規劃及設計組織的資訊安全做法。 |
| InfoSec 作業小組 | 這個小組會執行並監視 InfoSec 架構小組的已實行資訊安全性實務。 |

## <a name="next-steps"></a>後續步驟

開始使用身分 [識別和存取管理檢查和動作](active-directory-ops-guide-iam.md)。
