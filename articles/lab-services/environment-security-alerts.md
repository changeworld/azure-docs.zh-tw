---
title: Azure DevTest Labs 中環境的安全性警示
description: 本文說明如何在 DevTest Labs 中查看環境的安全性警示，並採取適當的動作。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992231"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure DevTest Labs 中環境的安全性警示
本文說明如何在 Azure DevTest Labs 中查看環境的安全性警示。 

## <a name="prerequisites"></a>必要條件
目前，您只可以針對部署到實驗室的環境來查看安全性警示。 若要測試或使用這項功能，請將環境部署至您的實驗室。 

## <a name="view-security-alerts-for-an-environment"></a>查看環境的安全性警示

1. 在您實驗室的首頁上，選取左側功能表上的 [**安全性警示**]。 您應該會看到安全性警示的數目（[高]、[中] 和 [低]）。

    ![安全性警示-總覽](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 以滑鼠右鍵按一下最後一個資料行中的三個點（...），然後選取 [**查看安全性警示**]。 

    ![檢視安全性警示](./media/environment-security-alerts/view-security-alerts-menu.png)
3. 您會看到有關警示和 advisor 建議的更多詳細資料。 

    ![檢視安全性警示](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>後續步驟
若要深入瞭解環境，請參閱下列文章：

- [使用 Azure Resource Manager 範本來建立多個 vm 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [設定及使用公用環境](devtest-lab-configure-use-public-environments.md)
