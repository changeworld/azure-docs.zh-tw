---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/20/2020
ms.author: glenga
ms.openlocfilehash: 574756aa9d9bac4aa42febf6a4fca4c62014db3f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "84732461"
---
1. 在 Visual Studio Code 中，選取 F1 以開啟命令選擇區。 在命令選擇區中，搜尋並選取 **Azure Functions：部署至函式應用程式**。

1. 如果您尚未登入，系統會提示您**登入 Azure**。 從瀏覽器登入之後，請返回 Visual Studio Code。 如果您有多個訂用帳戶，請**選取包含函式應用程式的訂用帳戶**。

1. 在 Azure 中選取您現有的函式應用程式。 當您收到有關回覆寫函式應用程式中所有檔案的警告時，請選取 [部署]**** 以確認警告並繼續。

專案已重建、重新封裝並上傳至 Azure。 現有的專案會由新的封裝取代，而函式應用程式會重新啟動。