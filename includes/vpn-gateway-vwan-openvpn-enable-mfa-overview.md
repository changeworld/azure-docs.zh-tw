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
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471490"
---
如果您想要在授與存取權之前提示使用者提供第二個驗證因素，您可以設定 Azure 多重要素驗證（MFA）。 您可以根據每個使用者來設定 MFA，或透過[條件式存取](../articles/active-directory/conditional-access/overview.md)來運用 mfa。

* 每位使用者的 MFA 可以免費啟用。 啟用每位使用者的 MFA 時，系統會提示使用者對系結至 Azure AD 租使用者的所有應用程式進行第二因素驗證。 如需步驟，請參閱[選項 1](#peruser) 。
* 條件式存取可讓您更精細地控制第二個因素的升級方式。 它可以只允許將 MFA 指派給 VPN，並排除其他系結至 Azure AD 租使用者的應用程式。 如需步驟，請參閱[選項 2](#conditional) 。