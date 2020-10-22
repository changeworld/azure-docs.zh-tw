---
title: 包含檔案
description: 包含檔案
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 6e5885e076222cd23ba127f3be41c1218f327ca0
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379777"
---
- 每個受控識別都會計入 Azure AD 租用戶中的物件配額限制，如 [Azure AD 服務限制](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md)中所述。
-   可建立受控識別的速率有下列限制：

    1. 每個 Azure 區域的每個 Azure AD 租用戶：每 20 秒 200 個建立作業。
    2. 每個 Azure 區域的每個 Azure 訂用帳戶：每 20 秒 40 個建立作業。

- 在您建立使用者指派的受控身分識別時，僅支援使用英數字元 (0-9、a-z 和 A-Z) 和連字號 (-)。 若要讓指派至虛擬機器或虛擬機器擴展集正常運作，名稱限制為 24 個字元。
