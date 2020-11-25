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
ms.openlocfilehash: 8fba8aa577bcb3b5ef44d57c388a1f1de7494782
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995605"
---
如果您希望使用者在授與存取權之前，收到第二個驗證因素的提示，您可以設定 Azure AD Multi-Factor Authentication (MFA) 。 您可以根據每個使用者來設定 MFA，也可以透過 [條件式存取](../articles/active-directory/conditional-access/overview.md)來利用 mfa。

* 每位使用者的 MFA 可免費啟用。 針對每位使用者啟用 MFA 時，系統會提示使用者針對系結至 Azure AD 租使用者的所有應用程式，進行第二個因素驗證。 請參閱 [選項 1](#peruser) 以瞭解步驟。
* 條件式存取可讓您更精細地控制第二個因素的升級方式。 它可以只允許將 MFA 指派給 VPN，並排除系結至 Azure AD 租使用者的其他應用程式。 如需步驟，請參閱 [選項 2](#conditional) 。