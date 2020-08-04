---
title: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: 644d58c3d1c60611b0d22d2757da089313fa12b6
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423694"
---
將 RBAC 角色指派給安全性主體之前，請先決定安全性主體應該具備的存取範圍。 最佳做法的要求是，最好只授與最小的可能範圍。

下列清單說明您可以將 Azure Blob 和佇列資源的存取範圍設定在什麼層級 (從最小的範圍開始)：

- **個別容器。** 在此範圍中，角色指派會套用至容器中的所有 Blob，以及容器屬性和中繼資料。
- **個別佇列。** 在此範圍中，角色指派會套用至佇列中的訊息，以及佇列屬性和中繼資料。
- **儲存體帳戶。** 在此範圍中，角色指派會套用至所有容器及其 Blob，或套用至所有佇列和其訊息。
- **資源群組。** 在此範圍中，角色指派會套用至資源群組中所有儲存體帳戶內的所有容器或佇列。
- **訂用帳戶。** 在此範圍中，角色指派會套用至訂用帳戶中所有資源群組的所有儲存體帳戶內所包含的所有容器或佇列。
- **管理群組。** 在此範圍中，角色指派會套用至管理群組的所有訂用帳戶中，所有資源群組的所有儲存體帳戶內所包含的所有容器或佇列。

如需 Azure 角色指派和範圍的詳細資訊，請參閱[什麼是 Azure 角色型存取控制 (Azure RBAC)？](../articles/role-based-access-control/overview.md)。
