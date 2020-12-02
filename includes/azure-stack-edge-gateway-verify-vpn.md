---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466264"
---
若要確認 VPN，您可以建立只能由您建立的虛擬網路存取的儲存體帳戶。 遵循下列步驟來建立此儲存體帳戶，並將其與您建立的虛擬網路建立關聯：

1. 建立儲存體帳戶。 您可以使用您打算搭配 Azure Stack Edge 裝置使用的儲存體帳戶。 嘗試從選取的網路) 以外的外部網路存取儲存體帳戶 (。 儲存體帳戶應可供存取。
2. 在 Azure 入口網站中，移至儲存體帳戶。 
3. 移至 [ **防火牆和虛擬網路**]。 在 [ **允許存取來源**] 的右窗格中，選擇 [ **選取的網路**]。 在 [ **使用虛擬網路保護儲存體帳戶**] 中，選擇 [ **+ 新增現有的虛擬網路]。**

    ![確認 VPN 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. 在 [ **新增網路** ] 分頁中：

    - 選取訂用帳戶。 這是與您的 Azure Stack Edge/資料箱閘道資源相關聯的相同訂用帳戶。 
    - 從下拉式清單中，選擇要與此儲存體帳戶相關聯的虛擬網路。 選取您在上一個步驟中建立的虛擬網路。
    - 在 [ **子網**] 中，選擇 [*_預設_* _] 和 [_GatewaySubnet *]。 此虛擬網路/子網組合將會啟用服務端點。 啟用存取需要最多15分鐘的時間才能完成。
    - 選取 [啟用]。

    ![確認 VPN 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. 儲存 **設定**。

    ![確認 VPN 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. 儲存設定之後，您可以看到已啟用虛擬網路的子網。

    ![確認 VPN 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. 若要確認資料現在只會透過 VPN，請遵循下列步驟： 
    - 嘗試從選取的網路) 以外的外部網路存取儲存體帳戶 (。 儲存體帳戶應該無法存取。 
    - 嘗試從您在所選網路中啟用的虛擬網路/子網存取儲存體帳戶。 儲存體帳戶應可供存取。 
 
只有在已啟用 VPN 的情況下，您才可以存取此儲存體帳戶。 如果您停用 VPN，您也需要調整儲存體帳戶的網路設定。 

如需詳細資訊，請移至 [設定 Azure 儲存體防火牆和虛擬網路](../articles/storage/common/storage-network-security.md)。 

