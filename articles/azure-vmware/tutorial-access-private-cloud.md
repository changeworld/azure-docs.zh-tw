---
title: 教學課程 - 了解如何存取您的私人雲端
description: 了解如何存取 Azure VMware 解決方案 (AVS) 私人雲端
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 32dc5c173789996d2559eda1153b1509e10e5984
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497959"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-avs-private-cloud"></a>教學課程：了解如何存取 Azure VMware 解決方案 (AVS) 私人雲端

在預覽期間，AVS 不允許您使用內部部署 vCenter 來管理您的私人雲端。 您必須透過 jumpbox 來執行本機 vCenter 執行個體的額外設定和連線。 

在本教學課程中，您會在上一個教學課程中建立的資源群組中建立適用於 jumpbox 的 Windows 虛擬機器 [教學課程：在 Azure 中設定 VMware 私人雲端的網路功能](tutorial-configure-networking.md)，並登入 vCenter。 這個 VM 位於您建立的相同虛擬網路上，並提供對 vCenter 和 NSX Manager 的存取權。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立用來連線到 vCenter 的 Windows 虛擬機器
> * 從您的虛擬機器登入 vCenter

## <a name="create-a-new-windows-virtual-machine"></a>建立新的 Windows 虛擬機器

在資源群組中，選取 [+新增] 然後搜尋並選取 [Microsoft Windows 10]，然後按一下 [建立]。

:::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="新增適用於 jumpbox 的新 Windows 10 VM" border="true":::

在欄位中輸入必要資訊，然後選取 [檢閱+建立]。 如需欄位詳細資訊，請參閱下列表格。

| 欄位 | 值 |
| --- | --- |
| **訂用帳戶** | 此值已填入資源群組所屬的訂用帳戶。 |
| **資源群組** | 已為目前的資源群組填入此值。 這應該是您在上一個教學課程中建立的資源群組。 |
| **虛擬機器名稱** | 輸入 VM 的唯一名稱。 |
| **區域** | 選取 VM 的地理位置。 |
| **可用性選項** | 將預設值保持選取狀態。 |
| **映像** | 選取 VM 映像。 |
| **大小** | 保留預設大小的值。 |
| **驗證類型**  | 選取 [密碼]。 |
| **使用者名稱** | 輸入使用者名稱以登入 VM。 |
| **密碼** | 輸入密碼以登入 VM。 |
| **確認密碼** | 輸入密碼以登入 VM。 |
| **公用輸入連接埠** | 選取 [無]。 如果您選取無，表示只有在要存取 VM 時，才能使用 [JIT 存取](../security-center/security-center-just-in-time.md#jit-configure)來控制存取權。  |

上述資訊皆輸入之後，按一下 [檢閱+建立]。 驗證通過後，選取 [建立] 以啟動虛擬機器建立流程。

:::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="建立適用於 jumpbox 的新 Windows 10 VM" border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>連線到私人雲端的本機 vCenter

從 jumpbox 中，使用 VMware vCenter SSO 登入 vSphere 用戶端。 使用雲端管理員使用者名稱登入 vSphere 用戶端；看到有關潛在安全性風險的警告時，請接受安全性風險並繼續進行。使用單一登入認證登入 VMware vCenter，並確認已成功顯示使用者介面。

在 Azure 入口網站中選取您的私人雲端，然後在**概觀**檢視中選取 [識別 > 預設]。 隨即顯示私人雲端 vCenter 和 NSX-T 管理員的 URL 和登入認證。

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="顯示私人雲端 vCenter 和 NSX 管理員 URL 和認證" border="true":::

瀏覽至您在上一個步驟建立的虛擬機器，並連線到虛擬機器。 如需如何連線至虛擬機器的詳細步驟，請參閱[連線至虛擬機器](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)

在 Windows VM 中開啟瀏覽器，並在兩個索引標籤中瀏覽至 vCenter 和 NSX-T 管理員 URL。 在 vCenter 索引標籤中，輸入上一個步驟中的 `cloudadmin@vmcp.local` 使用者認證。

:::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="登入私人雲端 vCenter" border="true":::

:::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter 入口網站" border="true":::

在瀏覽器的第二個索引標籤中，登入 NSX-T 管理員。

:::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="本機私人雲端 NSX 管理員首頁" border="true":::

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立用來連線到 vCenter 的 Windows 虛擬機器
> * 從您的虛擬機器登入 vCenter

繼續進行下一個教學課程，以了解如何縮放您的 AVS 私人雲端。

> [!div class="nextstepaction"]
> [縮放 AVS 私人雲端](tutorial-scale-private-cloud.md)
