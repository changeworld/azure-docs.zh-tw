---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460521"
---
在將 RBAC 角色指派給安全主體之前，請確定安全主體應具有的訪問範圍。 最佳做法規定，最好只授予盡可能窄的範圍。

下面的清單描述了對 Azure Blob 和佇列資源的訪問的範圍級別，從最窄的範圍開始：

- **單個容器。** 在此作用域中，角色指派應用於容器中的所有 Blob 以及容器屬性和中繼資料。
- **單個佇列。** 在此作用域中，角色指派適用于佇列中的消息以及佇列屬性和中繼資料。
- **存儲帳戶。** 在此範圍內，角色指派適用于所有容器及其 Blob，或適用于所有佇列及其消息。
- **資源組。** 在此範圍內，角色指派應用於資源組中的所有存儲帳戶中的所有容器或佇列。
- **訂閱。** 在此範圍內，角色指派應用於訂閱中所有資源組中的所有存儲帳戶中的所有容器或佇列。

> [!IMPORTANT]
> 如果訂閱包含 Azure DataBricks 命名空間，則受訂閱範圍的角色將不會授予對 Blob 和佇列資料的許可權。 而是將角色範圍限定到資源組、存儲帳戶或容器或佇列。     
