---
title: 配置權杖 - Azure 活動目錄 B2C |微軟文檔
description: 瞭解如何在 Azure 活動目錄 B2C 中配置權杖存留期和相容性設置。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189612"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>在 Azure 活動目錄 B2C 中配置權杖

在本文中，您將瞭解如何在 Azure 活動目錄 B2C （Azure AD B2C） 中配置[權杖的存留期和相容性](tokens-overview.md)。

## <a name="prerequisites"></a>Prerequisites

[建立使用者流程](tutorial-create-user-flows.md)，讓使用者註冊並登入您的應用程式。

## <a name="configure-token-lifetime"></a>配置權杖存留期

您可以在任何使用者流上配置權杖存留期。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器，然後選擇包含 Azure AD B2C 租戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
4. 選擇**使用者流（策略）。**
5. 打開以前創建的使用者流。
6. 選取 [內容]****。
7. 在**權杖存留期**下，調整以下屬性以滿足您的應用程式需求：

    ![Azure 門戶中的權杖存留期屬性設置](./media/configure-tokens/token-lifetime.png)

8. 按一下 [儲存]****。

## <a name="configure-token-compatibility"></a>配置權杖相容性

1. 選擇**使用者流（策略）。**
2. 打開以前創建的使用者流。
3. 選取 [內容]****。
4. 在**權杖相容性設置**下，調整以下屬性以滿足您的應用程式需求：

    ![Azure 門戶中的權杖相容性屬性設置](./media/configure-tokens/token-compatibility.png)

5. 按一下 [儲存]****。

## <a name="next-steps"></a>後續步驟

深入了解如何[使用存取權杖](access-tokens.md)。



