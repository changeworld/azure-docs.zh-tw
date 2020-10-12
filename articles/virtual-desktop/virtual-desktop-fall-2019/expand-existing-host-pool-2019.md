---
title: 使用新的工作階段主機展開現有 Windows 虛擬桌面 (傳統) 主機集區-Azure
description: 如何使用 Windows 虛擬桌面 (傳統) 中的新工作階段主機來擴充現有的主機集區。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61cf28b0f1ebee6a0312ec3f23f22b01c6c4919e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009166"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts-in-windows-virtual-desktop-classic"></a>在 Windows 虛擬桌面 (傳統) 中，以新的工作階段主機展開現有的主機集區

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../expand-existing-host-pool.md)。

當您在主機集區中提高使用量時，您可能需要使用新的工作階段主機來擴充現有的主機集區，以處理新的負載。

本文將告訴您如何使用新的工作階段主機來擴充現有的主機集區。

## <a name="what-you-need-to-expand-the-host-pool"></a>擴充主機集區所需的內容

開始之前，請確定您已使用下列其中一種方法，建立主機集區和工作階段主機虛擬機器 (Vm) ：

- [Azure Marketplace 供應專案](create-host-pools-azure-marketplace-2019.md)
- [GitHub Azure Resource Manager 範本](create-host-pools-arm-template.md)
- [使用 PowerShell 建立主機集區](create-host-pools-powershell-2019.md)

當您第一次建立主機集區和工作階段主機 Vm 時，您也需要下列資訊：

- VM 大小、映射和名稱前置詞
- 網域加入和 Windows 虛擬桌面租使用者系統管理員認證
- 虛擬網路名稱及子網路名稱

接下來三個區段是您可用來展開主機集區的三種方法。 您可以使用您熟悉的任何部署工具來進行。

>[!NOTE]
>在部署階段，如果先前的工作階段主機 VM 資源目前已關閉，您將會看到錯誤訊息。 發生這些錯誤的原因是 Azure 無法執行 PowerShell DSC 擴充功能，以驗證工作階段主機 Vm 是否已正確註冊至您現有的主機集區。 您可以放心地忽略這些錯誤，也可以在開始部署程式之前，先啟動現有主機集區中的所有工作階段主機 Vm，以避免發生錯誤。

## <a name="redeploy-from-azure"></a>從 Azure 重新部署

如果您已經使用 [Azure Marketplace](create-host-pools-azure-marketplace-2019.md) 供應專案或 [GitHub Azure Resource Manager 範本](create-host-pools-arm-template.md)建立主機集區和工作階段主機 vm，您可以從 Azure 入口網站重新部署相同的範本。 重新部署範本會自動將您輸入的所有資訊重新進入至原始範本，但密碼除外。

以下說明如何重新部署 Azure Resource Manager 範本以擴充主機集區：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從 Azure 入口網站頂端的搜尋列中，搜尋 **資源群組** ，然後選取 [ **服務**] 底下的專案。
3. 尋找並選取您在建立主機集區時所建立的資源群組。
4. 在瀏覽器左側的面板中，選取 [ **部署**]。
5. 為您的主機集區建立程式選取適當的部署：
     - 如果您建立了具有 Azure Marketplace 供應專案的原始主機集區，請選取以 **wvd-布建主機集**區開頭的部署。
     - 如果您使用 GitHub Azure Resource Manager 範本建立原始的主機集區，請選取名為 [ **Microsoft 範本**] 的部署。
6. 選取 [重新 **部署**]。

     >[!NOTE]
     >如果您選取 [重新 **部署**] 時範本未自動重新部署，請在瀏覽器左側的面板中選取 [ **範本** ]，然後選取 [ **部署**]。

7. 選取包含現有主機集區中目前工作階段主機 Vm 的資源群組。

     >[!NOTE]
     >如果您看到錯誤，指出您要選取不同的資源群組，即使您輸入的資源群組正確無誤，請選取另一個資源群組，然後選取原始資源群組。

8. 針對 *_artifactsLocation*輸入下列 URL： `https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. 輸入您想要加入至 *Rdsh 實例數目*的新工作階段主機總數。 例如，如果您要將主機集區從五個工作階段主機擴充至八個，請輸入 **8**。
10. 輸入您用於現有網域 UPN 的現有網域密碼。 請勿變更使用者名稱，因為這會在您執行範本時造成錯誤。
11. 輸入您為租使用者系統 *管理員 Upn 或應用程式識別碼*輸入的使用者或應用程式識別碼所使用的相同租使用者系統管理員密碼。同樣地，請不要變更使用者名稱。
12. 完成提交以展開您的主機集區。

## <a name="run-the-azure-marketplace-offering"></a>執行 Azure Marketplace 供應專案

遵循 [使用 Azure Marketplace 建立主機集](create-host-pools-azure-marketplace-2019.md) 區中的指示，直到您 [執行 Azure Marketplace 供應專案以布建新的主機集](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool)區為止。 當您進入該點時，您必須為每個索引標籤輸入下列資訊：

### <a name="basics"></a>基本概念

在您第一次建立主機集區和工作階段主機 Vm 時，此區段中的所有值都應該符合您所提供的值，但 *預設桌面使用者*除外：

1.    針對 [ *訂*用帳戶]，選取您第一次建立主機集區的訂用帳戶。
2.    針對 [ *資源群組*]，選取現有的主機集區工作階段主機 vm 所在的相同資源群組。
3.    針對 [ *區域*]，選取現有的主機集區工作階段主機 vm 所在的相同區域。
4.    在 [ *Hostpool 名稱*] 中，輸入現有主機集區的名稱。
5.    針對 [ *桌面類型*]，選取符合現有主機集區的桌上型電腦類型。
6.    針對 [ *預設桌面使用者*]，輸入您想要登入 Windows 虛擬桌面用戶端的任何其他使用者的逗號分隔清單，並在 Azure Marketplace 供應專案完成後存取桌面。 例如，如果您想要指派 user3@contoso.com 和 user4@contoso.com 存取，請輸入 user3@contoso.com user4@contoso.com 。
7.    選取 **[下一步：設定虛擬機器]**。

>[!NOTE]
>除了 *預設桌面使用者*，所有欄位都必須完全符合現有主機集區中設定的內容。 如果出現不相符的情況，將會產生新的主機集區。

### <a name="configure-virtual-machines"></a>設定虛擬機器

此區段中的所有參數值都應該符合您第一次建立主機集區和工作階段主機 Vm 時所提供的值，但 Vm 總數除外。 您輸入的 Vm 數目將是擴充的主機集區中的 Vm 數目：

1. 選取符合現有工作階段主機 Vm 的 VM 大小。

    >[!NOTE]
    >如果您要尋找的特定 VM 大小未出現在 VM 大小選取器中，原因是我們尚未將其上架到 Azure Marketplace 工具中。 若要要求某個 VM 大小，請在 [Windows 虛擬桌面 UserVoice 論壇](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)中建立要求或附議現有要求。

2. 自訂 *使用量設定檔*、 *使用者總數*及 *虛擬機器參數數目* ，以選取您想要在主機集區中擁有的工作階段主機總數。 例如，如果您要將主機集區從五個工作階段主機擴充至八個，請設定這些選項來取得8部虛擬機器。
3. 輸入虛擬機器的名稱前置詞。 比方說，如果您輸入名稱「前置詞」，則虛擬機器會稱為 "prefix-0"、"prefix-1"，依此類推。
4. 選取 **[下一步：虛擬機器設定]**。

### <a name="virtual-machine-settings"></a>虛擬機器設定

此區段中的所有參數值都應該符合您第一次建立主機集區和工作階段主機 Vm 時所提供的值：

1. 針對 [ *映射來源* ] 和 [ *映射作業系統版本*]，輸入您第一次建立主機集區時所提供的相同資訊。
2. 針對 *AD 網域加入 UPN* 和相關密碼，輸入您第一次建立主機集區以將 vm 加入 Active Directory 網域時所提供的相同資訊。 這些認證將用來在您的虛擬機器上建立本機帳戶。 您可以重設這些本機帳戶，稍後再變更其認證。
3. 針對虛擬網路資訊，請針對您現有的主機集區工作階段主機 Vm 所在的位置，選取相同的虛擬網路和子網。
4. 選取 **[下一步]：設定 Windows 虛擬桌面資訊**。

### <a name="windows-virtual-desktop-information"></a>Windows 虛擬桌面資訊

此區段中的所有參數值都應該符合您第一次建立主機集區和工作階段主機 Vm 時所提供的值：

1. 針對 [Windows 虛擬桌面租用戶群組名稱]  ，輸入包含您租用戶的租用戶群組。 除非系統將特定租用戶群組名稱提供給您，否則將它保留為預設值。
2. 針對 [Windows 虛擬桌面租用戶名稱]  ，輸入您將在其中建立此主機集區的租用戶名稱。
3. 指定您第一次建立主機集區和工作階段主機 Vm 時所使用的相同認證。 如果您使用服務主體，請輸入您的服務主體所在 Azure Active Directory 實例的識別碼。
4. 選取 **[下一步]： [檢查 + 建立]**。

## <a name="run-the-github-azure-resource-manager-template"></a>執行 GitHub Azure Resource Manager 範本

遵循 [執行 Azure Resource Manager 範本](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) 中的指示來布建新的主機集區，並提供所有相同的參數值，但不包括 *Rdsh 的實例數目*。 執行範本之後，請輸入您想要在主機集區中的工作階段主機 Vm 數目。 例如，如果您要將主機集區從五個工作階段主機擴充至八個，請輸入 **8**。

## <a name="next-steps"></a>後續步驟

現在您已擴充現有的主機集區，您可以登入 Windows 虛擬桌面用戶端，在使用者會話中進行測試。 您可以使用下列任何一個用戶端連線到會話：

- [與 Windows 桌面用戶端連線](connect-windows-7-10-2019.md)
- [與 Web 用戶端連線](connect-web-2019.md)
- [與 Android 用戶端連線](connect-android-2019.md)
- [與 macOS 用戶端連線](connect-macos-2019.md)
- [與 iOS 用戶端連線](connect-ios-2019.md)
