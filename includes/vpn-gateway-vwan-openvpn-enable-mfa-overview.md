---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471490"
---
如果希望在授予存取權限之前提示使用者進行第二個身份驗證因素，則可以配置 Azure 多重要素驗證 （MFA）。 您可以根據每個使用者配置 MFA，也可以通過[條件訪問](../articles/active-directory/conditional-access/overview.md)利用 MFA。

* 每個使用者的 MFA 可以不加付費。 當每個使用者啟用 MFA 時，將提示使用者對綁定到 Azure AD 租戶的所有應用程式進行第二因數身份驗證。 有關步驟，請參閱[選項 1。](#peruser)
* 條件訪問允許對如何提升第二個因素進行更細細微性的控制。 它可以允許僅將 MFA 分配給 VPN，並排除綁定到 Azure AD 租戶的其他應用程式。 有關步驟，請參閱[選項 2。](#conditional)