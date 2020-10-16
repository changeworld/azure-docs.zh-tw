---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131627"
---
| 機制 | 影響範圍 |限制 | 支援的許可權層級 |
|---|---|---|---|
| RBAC | 儲存體帳戶、容器。 <br>訂用帳戶或資源群組層級的跨資源 RBAC 角色指派。 | 2000訂用帳戶中的 RBAC 角色指派 | RBAC 角色 (內建或自訂)  |
| Acl| 目錄、檔案 |32 ACL 專案 (實際上是每個檔案和每個目錄) 的 ACL 專案。 存取和預設 Acl 各有各自的 32 ACL 專案限制。 |ACL 許可權|
