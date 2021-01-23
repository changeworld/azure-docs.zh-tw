---
title: '將遠端桌面套用至雲端服務 (延伸支援) '
description: '啟用雲端服務的遠端桌面 (延伸支援) '
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 53f873013a6f16ce5a28ee5d915afa556057f643
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744215"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>將遠端桌面延伸模組套用至 Azure 雲端服務 (延伸支援) 

即使在部署應用程式之後，Azure 入口網站也會使用遠端桌面延伸模組來啟用遠端桌面。 您雲端服務的遠端桌面設定可讓您啟用遠端桌面、更新本機系統管理員帳戶、選取用於驗證的憑證，以及設定這些憑證的到期日。 

## <a name="apply-remote-desktop--extension"></a>套用遠端桌面延伸模組
1. 流覽至您想要啟用遠端桌面的雲端服務，然後在左側流覽窗格中選取 [ **遠端桌面]** 。

    :::image type="content" source="media/remote-desktop-1.png" alt-text="影像顯示在 Azure 入口網站中選取 [遠端桌面] 選項":::

2. 選取 [新增]  。
3. 選擇要啟用遠端桌面的角色。
4. 針對 [使用者名稱]、[密碼]、[到期日] 和 [憑證] (填寫必要欄位，) 不需要。

    :::image type="content" source="media/remote-desktop-2.png" alt-text="影像會顯示如何輸入連接到遠端桌面所需的資訊。":::

5. 完成後，選取 [儲存]。 在您的角色執行個體準備好接受連線之前，會需要幾分鐘的時間。

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>連接到已啟用遠端桌面的角色實例
在角色上啟用遠端桌面之後，您就可以直接從 Azure 入口網站起始連接。

1. 按一下 [ **角色和實例** ] 以開啟實例設定。

    :::image type="content" source="media/remote-desktop-3.png" alt-text="影像顯示在設定分頁中選取 [角色和實例] 選項。":::

2. 選取已設定遠端桌面的角色實例。
3. 按一下 **[連線]** 以下載遠端桌面連線檔案。

    :::image type="content" source="media/remote-desktop-4.png" alt-text="影像會顯示在 Azure 入口網站中選取背景工作角色實例。":::
    
4. 開啟檔案以連接到角色實例。


## <a name="next-steps"></a>後續步驟 
- 請參閱雲端服務的 [部署必要條件](deploy-prerequisite.md) (延伸支援) 。
- 請 [參閱雲端服務的常見問題，](faq.md) (延伸支援) 。
- 使用 [Azure 入口網站](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [範本](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)，將雲端服務部署 (延伸支援) 。