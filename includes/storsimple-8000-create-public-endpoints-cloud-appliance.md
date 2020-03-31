---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 1cf5bbdad555c50c418851904f36a578522843b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174041"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>在雲端設備上建立公用端點

1. 登入 Azure 入口網站。
2. 移至 [虛擬機器]****，然後選取並按一下要用來作為雲端設備的虛擬機器。
    
3. 您必須建立網路安全性群組 (NSG) 規則，才可控制虛擬機器的進出流量。 執行下列步驟來建立 NSG 規則。
    1. 選擇 [網路安全性群組]****。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. 按一下顯示的預設網路安全性群組。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. 選取 [輸入安全性規則]****。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. 按一下 [+ 新增]**** 可建立輸入安全性規則。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        在 [新增輸入安全性規則] 刀鋒視窗中：

        1. 對於**名稱**，鍵入終結點的以下名稱：WinRMHttps。
        
        2. 針對 [優先順序]****，選取小於 1000 的數字 (這是預設規則的優先順序)。 值越高，優先順序就越低。

        3. 將 [來源]**** 設定為 [任何]****。

        4. 針對 [服務]****，選取 [WinRM]****。 **通訊協定**會自動設為 **TCP**，且**連接埠範圍**會設為 **5986**。

        5. 按一下 **"確定"** 以創建規則。

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. 最後一個步驟是將您的網路安全性群組與子網路或特定網路介面建立關聯。 執行下列步驟，可將您的網路安全性群組與子網路建立關聯。
    1. 移至 [子網路]****。
    2. 按一下 [+ 關聯]****。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. 選取您的虛擬網路，然後選取適當的子網路。
    4. 按一下 **"確定"** 以創建規則。

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

建立規則之後，您可以檢視其詳細資料，以確認公用虛擬 IP (VIP) 位址。 請記下此位址。


