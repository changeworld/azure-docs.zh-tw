---
title: 在 Azure DevTest Labs 中使用客戶管理的金鑰將 OS 磁片加密
description: 瞭解如何在 Azure DevTest Labs 中使用客戶管理的金鑰來加密作業系統（OS）磁片。
ms.topic: article
ms.date: 07/28/2020
ms.openlocfilehash: b9eb401521f6bd81efe3238dc05d07e4554c4f62
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542409"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中使用客戶管理的金鑰來加密作業系統（OS）磁片
伺服器端加密 (SSE) 可保護您的資料安全，並協助您符合組織安全性和合規性承諾。 根據預設，SSE 會自動將您儲存在 Azure （OS 和資料磁片）中受控磁片上的資料加密，以保存到雲端。 深入瞭解 Azure 上的[磁片加密](../virtual-machines/windows/disk-encryption.md)。 

在 DevTest Labs 中，建立為實驗室一部分的所有 OS 磁片和資料磁片都會使用平臺管理的金鑰進行加密。 不過，身為實驗室擁有者，您可以選擇使用您自己的金鑰來加密實驗室虛擬機器作業系統磁片。 如果您選擇使用自己的金鑰來管理加密，您可以指定**客戶管理的金鑰**，以用於加密實驗室 OS 磁片中的資料。 若要深入瞭解具有客戶管理金鑰的伺服器端加密（SSE）和其他受控磁片加密類型，請參閱[客戶管理的金鑰](../virtual-machines/windows/disk-encryption.md#customer-managed-keys)。 此外，請參閱[使用客戶管理的金鑰的限制](../virtual-machines/windows/disks-enable-customer-managed-keys-portal.md#restrictions)。


> [!NOTE]
> - 目前只有 DevTest Labs 中的 OS 磁片才支援使用客戶管理的金鑰進行磁片加密。 
> 
> - 此設定會套用至實驗室中新建立的 OS 磁片。 如果您選擇在某個時間點變更磁片加密集，則實驗室中較舊的磁片將繼續使用先前的磁片加密集保持加密狀態。 

下一節說明實驗室擁有者如何使用客戶管理的金鑰來設定加密。

## <a name="pre-requisites"></a>必要條件

1. 如果您沒有磁片加密集，請遵循這篇文章來[設定 Key Vault 和磁片加密集](../virtual-machines/windows/disks-enable-customer-managed-keys-portal.md#set-up-your-azure-key-vault)。 請注意下列磁片加密集的需求： 

    - 磁片加密組必須與**您的實驗室位於相同的區域和訂用**帳戶。 
    - 確定您（實驗室擁有者）至少具有將用來加密實驗室 OS 磁片之磁片加密集的**讀取者層級存取權**。  
2. 若要讓實驗室處理所有實驗室 OS 磁片的加密，實驗室擁有者必須明確授與實驗室**系統指派**的身分識別磁片加密集的許可權。 實驗室擁有者可以藉由完成下列步驟來執行此動作：

    > [!IMPORTANT]
    > 您必須針對在8/1/2020 或之後建立的實驗室執行這些步驟。 在該日期之前建立的實驗室不需要採取任何動作。

    1. 請確定您是 Azure 訂用帳戶層級的「[使用者存取系統管理員」角色](../role-based-access-control/built-in-roles.md#user-access-administrator)的成員，才能管理使用者對 azure 資源的存取權。 
    1. 在 [**磁片加密集**] 頁面上，選取左側功能表上的 **[存取控制（IAM）** ]。 
    1. 選取工具列上的 [ **+ 新增**]，然後選取 [**新增角色指派**]。  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="新增角色管理-功能表":::
    1. 在 [**新增角色指派**] 頁面上，選取 [**讀取**者] 角色或允許更多存取權的角色。 
    1. 輸入將使用磁片加密設定的實驗室名稱，然後從下拉式清單中選取實驗室名稱（實驗室的系統指派身分識別）。 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="選取實驗室的系統管理身分識別":::        
    1. 在工具列上選取 [儲存]。 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="儲存角色指派":::
3. 使用 [**訂**用帳戶**system-assigned identity** **Virtual Machine Contributor**  ->  **存取控制（IAM）** ] 頁面，將實驗室系統指派的身分識別新增至虛擬機器參與者角色。 這些步驟與先前步驟中的步驟類似。 

    > [!IMPORTANT]
    > 您必須針對在8/1/2020 或之後建立的實驗室執行這些步驟。 在該日期之前建立的實驗室不需要採取任何動作。

    1. 流覽至 Azure 入口網站中的 [**訂**用帳戶] 頁面。 
    1. 選取 [存取控制 (IAM)]  。 
    1. 選取工具列上的 [ **+ 新增**]，然後選取 [**新增角色指派**]。 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="訂用帳戶-> 存取控制（IAM）頁面":::
    1. 在 [**新增角色指派**] 頁面上，選取角色的 [**虛擬機器參與者**]。
    1. 輸入實驗室名稱，然後從下拉式清單中選取**實驗室名稱**（實驗室的系統指派身分識別）。 
    1. 在工具列上選取 [儲存]。 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>使用客戶管理的金鑰來加密實驗室作業系統磁片 

1. 在 Azure 入口網站的實驗室首頁上，選取左側功能表上的 [設定**和原則**]。 
1. 在 [設定**和原則**] 頁面上，選取 [**加密**] 區段中的 [**磁片（預覽）** ]。 根據預設，**加密類型**會設定為**使用平臺管理金鑰**的待用加密。

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="[設定和原則] 頁面的 [磁片] 索引標籤":::
1. 針對 [**加密類型**]，從下拉式清單中選取 [**使用客戶管理的金鑰進行靜態加密**]。 
1. 針對 [**磁片加密集**]，選取您稍早建立的磁片加密集。 這是系統指派的實驗室身分識別所能存取的相同磁片加密集。
1. 在工具列上選取 [儲存]。 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="使用客戶管理的金鑰來啟用加密":::
1. 在含有下列文字的訊息方塊上：*此設定會套用至實驗室中新建立的電腦。舊的 OS 磁片仍會使用舊的磁片加密組進行加密*，請選取 **[確定]**。 

    設定完成後，實驗室 OS 磁片會使用磁片加密集提供的客戶管理金鑰進行加密。 

## <a name="next-steps"></a>後續步驟
查看下列文章： 

- [Azure 磁碟加密](../virtual-machines/windows/disk-encryption.md)。 
- [客戶管理的金鑰](../virtual-machines/windows/disk-encryption.md#customer-managed-keys) 