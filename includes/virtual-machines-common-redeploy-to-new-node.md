---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "67173984"
---
## <a name="use-the-azure-portal"></a>使用 Azure 入口網站
1. 選取您想要重新部署的 VM，然後選取下 [設定]** 刀鋒視窗中的 [重新部署]** 按鈕。 您可能需要向下捲動以查看 [支援與疑難排解] **** 區段，其中包含 [重新部署] 按鈕，如下列範例所示︰
   
    ![Azure VM 刀鋒視窗](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. 若要確認此作業，請選取 [重新部署]** 按鈕：
   
    ![重新部署 VM 刀鋒視窗](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. 當 VM 準備重新部署時，VM 的 [狀態]**** 會變更為 [正在更新]**，如下列範例所示︰
   
    ![VM 正在更新](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. 當 VM 在新的 Azure 主機上開機時，[狀態]**** 會接著變更為 [正在啟動]**，如下列範例所示︰
   
    ![VM 正在啟動](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. 在 VM 完成開機程序之後，[狀態]**** 則會回復為 [正在執行]**，表示已順利重新部署 VM︰
   
    ![VM 正在執行](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

