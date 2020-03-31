---
title: 在客戶註冊期間禁用電子郵件驗證
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure 活動目錄 B2C 中的客戶註冊期間禁用電子郵件驗證。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 369b4e13baa344a71a51b358ef810d1a66b4b6ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126741"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>在 Azure 活動目錄 B2C 中註冊客戶期間禁用電子郵件驗證

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

按照以下步驟禁用電子郵件驗證：

1. 登錄到 Azure[門戶](https://portal.azure.com)
1. 使用頂部功能表中的**目錄 + 訂閱**篩選器選擇包含 Azure AD B2C 租戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 選取 [使用者流程]****。
1. 選擇要為其禁用電子郵件驗證的使用者流。 例如 *，B2C_1_signinsignup*。
1. 選擇**頁面配置**。
1. 選擇**本地帳戶註冊頁面**。
1. 在 **"使用者"屬性**下，選擇**電子郵件地址**。
1. 在 **"需要驗證"** 下拉下拉清單中，選擇 **"否**"。
1. 選取 [儲存]****。 現在禁用此使用者流的電子郵件驗證。

## <a name="next-steps"></a>後續步驟

- 瞭解如何自訂[Azure 活動目錄 B2C 中的使用者介面](customize-ui-overview.md)

