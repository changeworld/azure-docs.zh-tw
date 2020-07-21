---
title: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: c222869df561a9a36ebd69eb9ae09fa688ba0086
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518670"
---
將 RBAC 角色指派給安全性主體之前，請先決定安全性主體應該具備的存取範圍。 最佳做法規定，最好只授與最少的可能範圍。

下列清單說明從最小範圍開始，您可以將 Azure blob 和佇列資源存取範圍的層級：

- **個別容器。** 在此範圍中，角色指派會套用至容器中的所有 blob，以及容器屬性和中繼資料。
- **個別佇列。** 在此範圍中，角色指派會套用到佇列中的訊息，以及佇列屬性和中繼資料。
- **儲存體帳戶。** 在此範圍中，角色指派會套用至所有容器及其 blob，或所有佇列和其訊息。
- **資源群組。** 在此範圍中，角色指派會套用至資源群組中所有儲存體帳戶內的所有容器或佇列。
- **訂用帳戶。** 在此範圍中，角色指派會套用至訂用帳戶中所有資源群組中所有儲存體帳戶內的所有容器或佇列。
- **管理群組。** 在此範圍中，角色指派會套用至管理群組中所有訂用帳戶內所有資源群組中所有儲存體帳戶的所有容器或佇列。

如需有關 RBAC 角色指派和範圍的詳細資訊，請參閱[什麼是 Azure 角色型存取控制（AZURE RBAC）？](../articles/role-based-access-control/overview.md)。
