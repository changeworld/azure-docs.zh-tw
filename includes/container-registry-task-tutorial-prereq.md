---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 40cc1856a5e943ca5596e7d11712febadd30e3ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67133430"
---
## <a name="prerequisites"></a>Prerequisites

### <a name="get-sample-code"></a>取得範例程式碼

本教學課程假設您已完成[上一個教學課程](../articles/container-registry/container-registry-tutorial-quick-task.md)中的步驟，並已派生和複製範例存放庫。 如果您尚未完成上一個教學課程的[必要條件](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites)一節中的步驟，請先加以完成，再繼續操作。

### <a name="container-registry"></a>容器登錄

您的 Azure 訂用帳戶中必須要有 Azure 容器登錄，才能完成本教學課程。 如果您需要登錄，請參閱[上一個教學課程](../articles/container-registry/container-registry-tutorial-quick-task.md)或[快速入門：使用 Azure CLI 建立容器登錄](../articles/container-registry/container-registry-get-started-azure-cli.md)。

## <a name="create-a-github-personal-access-token"></a>建立 GitHub 個人存取權杖

為了在認可至 Git 存放庫時觸發工作，ACR 工作需要以個人存取權杖 (PAT) 存取存放庫。 如果您還沒有 PAT，請依照下列步驟在 GitHub 中產生 PAT：

1. 瀏覽至 GitHub 上的 PAT 建立頁面 (https://github.com/settings/tokens/new )
1. 輸入權杖的簡短**說明**，例如「ACR 工作示範」
1. 選取 ACR 的範圍以存取存放庫。 若要如同本教學課程一般地存取公用存放庫，請在**存放庫**底下，啟用 **repo:status** 和 **public_repo**

   ![GitHub 中的個人存取權杖產生頁面的螢幕擷取畫面][build-task-01-new-token]

   > [!NOTE]
   > 若要產生 PAT 以存取「私人」  存放庫，請選取完整**存放庫**控制的範圍。

1. 選取 [產生權杖]  按鈕 (系統可能會要求您確認密碼)
1. 在**安全的位置**複製並儲存產生的權杖 (當您在下一節定義工作時，將會使用此權杖)

   ![GitHub 中已產生的個人存取權杖的螢幕擷取畫面][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
