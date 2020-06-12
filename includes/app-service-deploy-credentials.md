---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: cephalin
ms.openlocfilehash: c3fa57dd162fbbfbf0d46f73bffc78f279ef2968
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649112"
---
* **使用者層級認證**︰一組用於整個 Azure 帳戶的認證。 它可以用來將任何應用程式部署至 Azure 帳戶有權存取的任何訂用帳戶的 App Service 中。 其為呈現在入口網站 GUI 中的預設集合 (例如應用程式[資源頁面](../articles/azure-resource-manager/management/manage-resources-portal.md#manage-resources)的 [概觀] 和 [屬性])。 透過角色型存取控制 (RBAC) 或共同管理員權限來為使用者授與應用程式存取權時，該使用者可以使用他們自己的使用者層級認證，直到存取權遭到撤銷為止。 請勿與其他 Azure 使用者共用這些認證。

* **應用程式層級認證**︰一組用於單個 應用程式的認證。 它只可以用來部署該應用程式。 每個應用程式的認證都會在建立應用程式時自動產生。 您無法手動設定它們，但可隨時重設。 若是要透過 (RBAC) 授與應用程式層級認證存取權的使用者，該使用者就必須是應用程式上的參與者或更高權限 (包括網站參與者內建角色)。 讀者不允許發佈且無法存取那些認證。