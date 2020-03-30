---
title: Azure 活動目錄操作參考指南
description: 此操作參考指南描述為確保和維護標識和訪問管理、身份驗證、治理和操作而應採取的檢查和操作
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535310"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Azure 活動目錄操作參考指南

此操作參考指南描述了為確保和維護以下區域而應採取的檢查和操作：

- **[身份和訪問管理](active-directory-ops-guide-iam.md)**- 管理身份及其權利生命週期的能力。
- **[身份驗證管理](active-directory-ops-guide-auth.md)**- 能夠管理認證、定義身份驗證體驗、委派分配、測量使用方式以及根據企業安全狀況定義訪問策略。
- **[治理](active-directory-ops-guide-govern.md)**- 評估和證明授予的非特權和特權標識、審核和控制環境更改的訪問的能力。
- **[操作](active-directory-ops-guide-ops.md)**- 優化操作 Azure 活動目錄 （Azure AD）。

此處的某些建議可能不適用於所有客戶的環境，例如，如果您的組織使用密碼雜湊同步，則 AD FS 最佳實踐可能不適用。

> [!NOTE]
> 這些建議自發布之日起是最新的，但可能會隨時間而變化。 隨著 Microsoft 產品和服務隨時間推移而變化，組織應持續評估其身份實踐。 當組織訂閱其他 Azure AD 高級許可證時，建議可能會更改。 例如，Azure AD 高級 P2 將包含更多治理建議。

## <a name="stakeholders"></a>利益 相關 者

本指南中的每個部分都建議分配利益相關者以成功規劃和實施要徑任務。 下表概述了本指南中所有利益相關者的清單：

| 利害關係人 | 描述 |
| :- | :- |
| IAM 運營團隊 | 此團隊負責管理身份和訪問管理系統的日常操作 |
| 生產力團隊 | 該團隊擁有並管理工作效率應用程式，如電子郵件、檔共用和協作、即時消息和會議。 |
| 應用程式擁有者 | 此團隊從企業擁有特定應用程式，通常從組織中的技術角度擁有該應用程式。 |
| InfoSec 架構團隊 | 該團隊計畫並設計組織的資訊安全實踐。 |
| InfoSec 運營團隊 | 此團隊運行並監視 InfoSec 體系結構團隊的已實施資訊安全實踐。 |

## <a name="next-steps"></a>後續步驟

開始使用[標識和訪問管理檢查和操作](active-directory-ops-guide-iam.md)。
