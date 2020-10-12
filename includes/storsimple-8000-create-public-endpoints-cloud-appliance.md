---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5a286753e438b7d65f3d33a82669c4f7e79a282
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86544100"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>在雲端設備上建立公用端點

1. 登入 Azure 入口網站。
2. 移至 [虛擬機器]****，然後選取並按一下要用來作為雲端設備的虛擬機器。
    
3. 您必須建立網路安全性群組 (NSG) 規則，才可控制虛擬機器的進出流量。 執行下列步驟來建立 NSG 規則。
    1. 選擇 [網路安全性群組]****。
        ![虛擬機器頁面的螢幕擷取畫面。 在 [設定] 區段中，[網路安全性群組] 會反白顯示。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. 按一下顯示的預設網路安全性群組。
        ![[網路安全性群組] 頁面的螢幕擷取畫面。 預設的網路安全性群組會反白顯示。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. 選取 [輸入安全性規則]。
        ![頁面的螢幕擷取畫面，其中顯示預設網路安全性群組的屬性。 在流覽窗格中，會反白顯示輸入安全性規則。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. 按一下 [+ 新增]**** 可建立輸入安全性規則。
        ![輸入安全性規則頁面的螢幕擷取畫面。 加號和單字會彼此相鄰，並且會反白顯示。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        在 [新增輸入安全性規則] 刀鋒視窗中：

        1. 針對 [ **名稱**]，輸入下列端點的名稱： WinRMHttps。
        
        2. 針對 [優先順序]****，選取小於 1000 的數字 (這是預設規則的優先順序)。 值越高，優先順序就越低。

        3. 將 [來源]**** 設定為 [任何]****。

        4. 針對 [服務]****，選取 [WinRM]****。 **通訊協定**會自動設為 **TCP**，且**連接埠範圍**會設為 **5986**。

        5. 按一下 **[確定]** 以建立規則。

            ![[新增輸入安全性規則] 分頁的螢幕擷取畫面。 這些值會依照程式中的說明填入，而 [確定] 按鈕會反白顯示。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. 最後一個步驟是將您的網路安全性群組與子網路或特定網路介面建立關聯。 執行下列步驟，可將您的網路安全性群組與子網路建立關聯。
    1. 移至 [子網路]****。
    2. 按一下 [+ 關聯]****。
        ![[子網] 頁面的螢幕擷取畫面。 加號和單字之間的關聯是彼此連續的，並且會反白顯示。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. 選取您的虛擬網路，然後選取適當的子網路。
    4. 按一下 **[確定]** 以建立規則。

        ![[關聯子網] 頁面的螢幕擷取畫面。 已選取 [虛擬網路]，[確定] 按鈕會反白顯示。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

建立規則之後，您可以檢視其詳細資料，以確認公用虛擬 IP (VIP) 位址。 請記下此位址。


