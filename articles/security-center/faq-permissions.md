---
title: Azure 資訊安全中心常見問題-關於許可權的問題
description: 此常見問題會回答有關 Azure 資訊安全中心中許可權的問題，此產品可協助您預防、偵測及回應威脅。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 88d6207bf44d1ec04fde4f74ac9295412ea88fbc
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87824608"
---
# <a name="permissions"></a>權限

## <a name="how-do-permissions-work-in-azure-security-center"></a>Azure 資訊安全中心中的許可權如何發揮作用？

Azure 資訊安全中心使用[azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/role-assignments-portal.md)，它會提供可在 azure 中指派給使用者、群組和服務的[內建角色](../role-based-access-control/built-in-roles.md)。

資訊安全中心會評估資源的組態，以識別安全性問題與弱點。 在「資訊安全中心」中，當您獲指派為資源所屬的訂用帳戶或資源群組「擁有者」、「參與者」或「讀取者」角色時，您只會看到與資源相關的項目。

請參閱[Azure 資訊安全中心的權限](security-center-permissions.md)以深入了解角色與資訊安全中心允許的動作。



## <a name="who-can-modify-a-security-policy"></a>誰可以修改安全性原則？

若要修改安全性原則，您必須是安全性系統管理員或該訂用帳戶的擁有者或參與者。

若要了解如何設定安全性原則，請參閱[在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md)。