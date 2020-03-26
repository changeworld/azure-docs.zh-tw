---
title: 快速入門 - 檢視使用者擁有的 Azure 資源存取權
description: 在本快速入門中，了解如何使用角色型存取控制 (RBAC) 和 Azure 入口網站，來檢視使用者或其他安全性主體所擁有的 Azure 資源存取權。
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b23c10fc2a551b8044b208911dbc048968b06564
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "74419614"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>快速入門：檢視使用者擁有的 Azure 資源存取權

您可以使用[角色型存取控制 (RBAC)](overview.md) 中的 [存取控制 (IAM)]  刀鋒視窗，來檢視使用者或另一個安全性主體所擁有的 Azure 資源存取權。 不過，有時候您只是要快速檢視單一使用者或另一個安全性主體的存取權。 這麼做的最簡單方式是使用 Azure 入口網站中的 [檢查存取]  功能。

## <a name="view-role-assignments"></a>檢視角色指派

 您用來檢視使用者存取權的方式，是列出其角色指派。 依照這些步驟來檢視訂用帳戶範圍內單一使用者、群組、服務主體或受控識別的角色指派。

1. 在 Azure 入口網站中，按一下 [所有服務]  ，然後按一下 [訂用帳戶]  。

1. 按一下您的訂用帳戶。

1. 按一下 [存取控制 (IAM)]  。

1. 按一下 [檢查存取權]  索引標籤。

    ![存取控制 - [檢查存取權] 索引標籤](./media/check-access/access-control-check-access.png)

1. 在 [尋找]  清單中，選取您要檢查其存取權的安全性主體類型。

1. 在搜尋方塊中，輸入字串以在目錄中搜尋顯示名稱、電子郵件地址或物件識別碼。

    ![檢查存取權選取清單](./media/check-access/check-access-select.png)

1. 按一下安全性主體以開啟 [指派]  窗格。

    ![[指派] 窗格](./media/check-access/check-access-assignments.png)

    在此窗格上，您可以看到已指派所選安全性主體的角色與範圍。 若此範圍中有任何拒絕指派，或有任何拒絕指派繼承到此範圍，它們將會被列出。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 RBAC 與 Azure 入口網站為使用者授與 Azure 資源的存取權](quickstart-assign-role-user-portal.md)
