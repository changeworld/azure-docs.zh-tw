---
title: 在 Azure DevTest Labs 中使用客戶管理的金鑰來加密作業系統磁片
description: 瞭解如何在 Azure DevTest Labs 中使用客戶管理的金鑰來加密作業系統 (OS) 磁片。
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 26ef4ff1529483da9956c6dcc43807af0ffd6463
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341202"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中使用客戶管理的金鑰，將作業系統 (OS) 磁片加密
伺服器端加密 (SSE) 可保護您的資料安全，並協助您符合組織安全性和合規性承諾。 SSE 會自動將您儲存在 Azure 中的受控磁片上的資料加密 (OS 和資料磁片，並在將其保存到雲端時預設為) 。 深入瞭解 Azure 上的 [磁片加密](../virtual-machines/disk-encryption.md) 。 

在 DevTest Labs 內，所有建立為實驗室一部分的 OS 磁片和資料磁片都會使用平臺管理的金鑰進行加密。 不過，您可以選擇使用您自己的金鑰來加密實驗室虛擬機器 OS 磁片，作為實驗室擁有者。 如果您選擇使用您自己的金鑰管理加密，可以指定 **客戶管理的金鑰** ，以用於加密實驗室作業系統磁片中的資料。 若要深入瞭解伺服器端加密 (SSE) 使用客戶管理的金鑰和其他受控磁片加密類型，請參閱 [客戶管理的金鑰](../virtual-machines/disk-encryption.md#customer-managed-keys)。 此外，請參閱 [使用客戶管理金鑰的限制](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions)。

> [!NOTE]
> - 只有 DevTest Labs 中的 OS 磁片才支援使用客戶管理金鑰的磁片加密。 
> 
> - 此設定會套用至實驗室中新建立的 OS 磁片。 如果您選擇在某個時間點變更磁片加密集，則會繼續使用先前的磁片加密集，將實驗室中較舊的磁片保持加密狀態。 

下一節顯示實驗室擁有者如何使用客戶管理的金鑰來設定加密。

## <a name="pre-requisites"></a>必要條件

1. 如果您沒有磁片加密集，請遵循這篇文章來 [設定 Key Vault 和磁片加密集](../virtual-machines/disks-enable-customer-managed-keys-portal.md)。 請注意磁片加密集的下列需求： 

    - 磁片加密集必須與您的 **實驗室位於相同的區域和訂用帳戶中**。 
    - 確定您 (實驗室擁有者，) 至少具有將用來加密實驗室作業系統磁片之磁片加密集的 **讀取者層級存取權** 。 
1. 針對在8/1/2020 之前建立的實驗室，實驗室擁有者將需要確定已啟用實驗室系統指派的身分識別。 若要這樣做，實驗室擁有者可以移至其實驗室、按一下 [設定] **和 [原則**]、按一下 [身分 **識別 (預覽])** 分頁、將系統指派的身分識別 **狀態** 變更為 [ **開啟** ]，然後按一下 [ **儲存**]。 針對在8/1/2020 實驗室的系統指派身分識別之後建立的新實驗室，預設會啟用。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="受控金鑰":::
1. 實驗室擁有者必須在磁片加密集上明確授與實驗室 **系統指派** 的身分識別讀者角色，以及基礎 Azure 訂用帳戶上的虛擬機器參與者角色，才能讓實驗室處理所有實驗室作業系統磁片的加密。 實驗室擁有者可以完成下列步驟來執行此作業：

   
    1. 確定您是 Azure 訂用帳戶層級的「 [使用者存取系統管理員」角色](../role-based-access-control/built-in-roles.md#user-access-administrator) 的成員，如此您才能管理使用者對 azure 資源的存取權。 
    1. 在 [ **磁片加密集** ] 頁面上，選取左側功能表上的 [ **存取控制] (IAM)** 。 
    1. 選取工具列上的 [ **+ 新增** ]，然後選取 [ **新增角色指派**]。  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="新增角色管理-功能表":::
    1. 在 [ **新增角色指派** ] 頁面上，選取 [ **讀取** 者] 角色或允許更多存取權的角色。 
    1. 輸入將使用磁片加密集的實驗室名稱，然後從下拉式清單中選取實驗室名稱 (系統指派的身分識別) 實驗室的身分識別。 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="選取實驗室的系統管理身分識別":::        
    1. 在工具列上選取 [儲存]。 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="儲存角色指派":::
3. 使用 **訂** 用帳戶 **system-assigned identity** **Virtual Machine Contributor**  ->  **存取控制 (IAM)** 頁面，將實驗室的系統指派身分識別新增至虛擬機器參與者角色。 這些步驟與先前步驟中的步驟類似。 

    
    1. 流覽至 Azure 入口網站中的 [ **訂** 用帳戶] 頁面。 
    1. 選取 [存取控制 (IAM)]  。 
    1. 選取工具列上的 [ **+ 新增** ]，然後選取 [ **新增角色指派**]。 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="訂用帳戶 > 存取控制 (IAM) 頁面":::
    1. 在 [ **新增角色指派** ] 頁面上，為角色選取 [ **虛擬機器參與者** ]。
    1. 輸入實驗室名稱，然後從下拉式清單中選取實驗室 **名稱** (系統指派的身分識別) 實驗室的身分識別。 
    1. 在工具列上選取 [儲存]。 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>使用客戶管理的金鑰來加密實驗室作業系統磁片 

1. 在 Azure 入口網站的實驗室首頁上，選取左側功能表上的 [設定 **與原則** ]。 
1. 在 [設定 **與原則**] 頁面的 [**加密**] 區段中，選取 [**磁片 (預覽])** 。 根據預設， **加密類型** 會設定為 **使用平臺管理的金鑰** 進行待用加密。

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="設定和原則頁面的 [磁片] 索引標籤":::
1. 針對 [ **加密類型**]，請從下拉式清單中選取 [ **使用客戶管理的金鑰** 進行待用加密]。 
1. 針對 **磁片加密集**，請選取您稍早建立的磁片加密集。 它是系統指派的實驗室身分識別所能存取的相同磁片加密集。
1. 在工具列上選取 [儲存]。 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="使用客戶管理的金鑰來啟用加密":::
1. 在具有下列文字的訊息方塊上： *此設定將套用至實驗室中新建立的電腦。舊的 OS 磁片會以舊的磁片加密集保持加密* 狀態，請選取 **[確定]**。 

    一旦設定之後，實驗室作業系統磁片將會以使用磁片加密集提供的客戶管理金鑰進行加密。 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>如何驗證磁片是否已加密

1. 移至在實驗室中使用客戶管理的金鑰啟用磁片加密後所建立的實驗室虛擬機器。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="已啟用磁片加密的 VM":::
1. 按一下 VM 的資源群組，然後按一下 OS 磁片。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="VM 資源群組":::
1. 移至 [加密]，然後使用您選取的磁片加密集，驗證加密是否設定為客戶管理的金鑰。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="驗證加密":::
  
## <a name="next-steps"></a>後續步驟

查看下列文章： 

- [Azure 磁碟加密](../virtual-machines/disk-encryption.md)。 
- [客戶管理的金鑰](../virtual-machines/disk-encryption.md#customer-managed-keys)