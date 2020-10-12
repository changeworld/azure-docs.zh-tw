---
title: Azure DevTest Labs 中的網路隔離
description: 瞭解 Azure DevTest Labs 中的網路隔離。
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: fbd2725cd3677e03cadbe0ae1f060b141f5d212b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88875658"
---
# <a name="network-isolation-in-devtest-labs"></a>DevTest Labs 中的網路隔離

[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)可作為安全性界限，讓您的 azure 資源與公用網際網路隔離。 您也可以將 Azure 虛擬網路加入至您的內部部署網路，以安全地連線到您的內部內部部署資源。 在 DevTest Labs 中，您可以選擇將 [所有的實驗室虛擬機器](devtest-lab-configure-vnet.md) 和 [環境隔離到您的網路](connect-environment-lab-virtual-network.md) ，以確保實驗室資源遵循組織網路原則。 

作為實驗室擁有者，您也可以選擇完全隔離實驗室，也就是將虛擬機器和環境隔離到選取的網路，您也可以隔離在訂用帳戶中建立的實驗室儲存體帳戶和金鑰保存庫。 本文將逐步引導您建立網路隔離的實驗室。 

另請參閱下列文章：

- [DevTest Labs 如何使用實驗室儲存體帳戶](encrypt-storage.md)
- [DevTest Labs 如何使用金鑰保存庫](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> 目前只支援新實驗室建立的網路隔離。

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>在實驗室建立期間啟用網路隔離的步驟

1. 在實驗室建立期間，移至 [ **網路** ] 索引標籤。
1. 您可以選取實驗室將為您建立的 **預設** 網路，或從下拉式清單中選取現有的網路。 您只能選取與實驗室位於相同區域和訂用帳戶中的網路。 

    > [!div class="mx-imgBorder"]
    > ![建立實驗室](./media/network-isolation/create-lab.png)
1. 選取子網。

    > [!div class="mx-imgBorder"]
    > ![建立子網](./media/network-isolation/create-lab-subnet.png)
1. 如果您選擇將實驗室資源 (實驗室儲存體帳戶和金鑰保存庫) 到預設網路，則不需要採取進一步的動作，而且實驗室將會處理後續的資源隔離。
 
    > [!div class="mx-imgBorder"]
    > ![網路隔離](./media/network-isolation/isolate-lab-resources.png)
1. 如果您選擇將實驗室資源 (實驗室儲存體帳戶和金鑰保存庫) 至您選取的現有網路，則必須在實驗室建立後完成下列步驟，以確保實驗室在隔離模式中繼續運作。 
 
    > [!div class="mx-imgBorder"]
    > ![隔離資源](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > 實驗室擁有者必須先完成這些步驟，才能在實驗室中設定及/或建立任何資源。

### <a name="steps-to-follow-post-lab-creation"></a>在實驗室建立後遵循的步驟

1. 在實驗室的首頁上，選取 [**總覽**] 頁面上的**資源群組**。 您應該會看到包含實驗室之資源群組的 [ **資源群組** ] 頁面。 
 
   > [!div class="mx-imgBorder"]
   > ![Contoso 實驗室](./media/network-isolation/contoso-lab.png)
1. 選取實驗室的 Azure 儲存體帳戶。 實驗室儲存體帳戶的命名慣例為：<*labNameWithoutInvalidCharacters* > *<4 位數的數位*>。 例如，如果實驗室名稱是 contosolab，則可以 acontosolab1234 儲存體帳戶名稱。
 
   > [!div class="mx-imgBorder"]
   > ![Contoso 測試](./media/network-isolation/contoso-test.png)
1. 在儲存體帳戶上，移至 [防火牆和虛擬網路]，並確定已核取 [允許信任的 Microsoft 服務存取此儲存體帳戶] 核取方塊。 因為 [DevTest Labs 是受信任的 Microsoft 服務](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)，所以此選項可讓實驗室以網路隔離模式正常運作。 

   > [!div class="mx-imgBorder"]
   > ![Contoso 實驗室防火牆](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. 接著，按一下 [ **+ 新增現有的虛擬網路**]，選取您在建立實驗室時選取的虛擬網路和子網，然後按一下 [ **啟用**]。 

   > [!div class="mx-imgBorder"]
   > ![Contoso 我的 vnet](./media/network-isolation/contoso-lab-my-vnet.png)
5.  一旦成功啟用所選虛擬網路的服務端點，請按一下 [ **新增**]。 

   > [!div class="mx-imgBorder"]
   > ![加入](./media/network-isolation/contoso-firewall-add.png)
 
如此一來，Azure 儲存體將允許來自新增之虛擬網路的輸入連線，並讓實驗室在網路隔離模式中順利運作。 

您也可以選擇將這些步驟自動化，以針對多個實驗室設定此設定。 

[深入瞭解如何使用 PowerShell 和 CLI 來管理 Azure 儲存體的預設網路存取規則](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=/azure/virtual-network/toc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>在網路隔離模式中使用實驗室時要記住的事項

### <a name="accessing-labs-storage-account-outside-the-lab"></a>在實驗室外部存取實驗室的儲存體帳戶 

在網路隔離的實驗室中，如需將 VHD 上傳至實驗室的儲存體帳戶來建立自訂映射的動作，實驗室擁有者必須明確地啟用從允許的端點存取儲存體帳戶。 您可以建立虛擬機器，並從該虛擬機器安全地存取實驗室的儲存體帳戶。 

[深入瞭解如何從虛擬機器私下存取儲存體帳戶](../private-link/create-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>從實驗室匯出使用量資料 

在網路隔離的實驗室中，若要 [匯出實驗室的個人使用資料](personal-data-delete-export.md)，實驗室擁有者將明確地需要提供儲存體帳戶，並在帳戶內產生 blob 來儲存資料。 

如果未提供儲存體帳戶，這項作業將會在網路隔離模式下失敗，因為實驗室的儲存體帳戶無法供實驗室使用，以防客戶未提供任何儲存體帳戶。 

[深入瞭解如何在指定的儲存體帳戶中匯出實驗室使用方式資料](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>後續步驟

[使用 PowerShell 及 Azure Resource Manager 範本自動建立或修改實驗室](devtest-lab-use-arm-and-powershell-for-lab-resources.md)
