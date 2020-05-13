---
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 11a9b8609218a6cf56a789b18094d048e26d4af8
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83343332"
---
標準化的虛擬機器（VM）映射可讓組織遷移至雲端，並確保部署的一致性。 映射通常包含預先定義的安全性和設定，以及必要的軟體。 設定您自己的映射處理管線需要時間、基礎結構和設定，但使用 Azure VM 映射產生器時，只需提供簡單的設定來描述您的映射、將它提交至服務，然後建立映射並加以散發。
 
Azure VM 映射產生器（Azure 映射產生器）可讓您從以 Windows 或 Linux 為基礎的 Azure Marketplace 映射、現有的自訂映射或 Red Hat Enterprise Linux （RHEL） ISO 開始，並開始新增您自己的自訂專案。 因為映射產生器是建立在[HashiCorp Packer](https://packer.io/)上，您也可以匯入現有的 Packer shell 布建程式腳本。 您也可以在[Azure 共用映射資源庫](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)中，指定您想要裝載映射的位置，做為受控映射或 VHD。

> [!IMPORTANT]
> Azure 映射產生器目前為公開預覽版。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="preview-features"></a>預覽功能

針對預覽版本，支援下列功能：

- 建立黃金基準影像，其中包括您的最低安全性和公司設定，並允許部門根據其需求進一步進行自訂。
- 修補現有的映射，映射產生器可讓您持續修補現有的自訂映射。
- 將映射產生器連接到現有的虛擬網路，以便連接到現有的設定伺服器（DSC、Chef、Puppet 等）、檔案共用，或任何其他可路由傳送的伺服器/服務。
- 與 Azure 共用映射資源庫整合，可讓您全域散發、版本及調整影像，並提供映射管理系統。
- 與現有的映射組建管線整合，只需從管線呼叫影像產生器，或使用簡單的預覽影像產生器 Azure DevOps 工作。
- 將現有的映射自訂管線遷移至 Azure。 使用您現有的腳本、命令和進程來自訂映射。
- 建立 VHD 格式的映射以支援 Azure Stack。
 

## <a name="regions"></a>區域
Azure 映射產生器服務會在這些區域中提供預覽。 映射可以散佈在這些區域之外。
- 美國東部
- 美國東部 2
- 美國中西部
- 美國西部
- 美國西部 2
- 北歐
- 西歐

## <a name="os-support"></a>OS 支援
AIB 將支援 Azure Marketplace 基本 OS 映射：
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6、7。7
- CentOS 7.6、7。7
- SLES 12 SP4
- SLES 15、SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise 多會話/專業版
- Windows 2016
- Windows 2019

不支援 RHEL Iso 支援。
## <a name="how-it-works"></a>運作方式


![Azure 映射產生器的概念圖](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure 映射產生器是完全受控的 Azure 服務，可供 Azure 資源提供者存取。 Azure 映射產生器流程有三個主要部分：來源、自訂和散發，這些都是以範本表示。 下圖顯示元件及其部分屬性。 
 


**影像產生器進程** 

![Azure 映射產生器進程的概念繪圖](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. 建立映射範本作為 json 檔案。 此 json 檔案包含映射來源、自訂和散發的相關資訊。 [Azure 映射](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)產生器 GitHub 存放庫中有多個範例。
1. 將它提交給服務，這會在您指定的資源群組中建立映射範本成品。 在背景中，映射產生器會視需要下載來源映射或 ISO 和腳本。 這些會儲存在您的訂用帳戶中自動建立的個別資源群組中，格式為： IT_ \< DestinationResourceGroup>_ \< TemplateName>。 
1. 建立映射範本之後，您就可以建立映射。 在背景影像產生器中，會使用範本和來源檔案來建立 VM （預設大小： Standard_D1_v2）、網路、公用 IP、NSG，以及 IT_ \< DestinationResourceGroup>_ \< TemplateName> 資源群組中的儲存體。
1. 作為映射建立的一部分，映射產生器會根據範本散發映射，然後刪除 IT_ DestinationResourceGroup 中的其他資源， \<>_ \< TemplateName 為該進程建立的> 資源群組。


## <a name="permissions"></a>權限
當您註冊（AIB）時，這會授與 AIB 服務許可權，以建立、管理和刪除暫存資源群組（IT_ *），並具有將資源新增至映射組建所需的許可權。 這是由在成功註冊期間，在您的訂用帳戶中提供的 AIB 服務主體名稱（SPN）所完成。

若要允許 Azure VM 映射產生器將映射發佈至受控映射或共用映射資源庫，您必須建立 Azure 使用者指派的身分識別，其具有讀取和寫入映射的許可權。 如果您要存取 Azure 儲存體，則這將需要讀取私人容器的許可權。

一開始，您必須[建立 Azure 使用者指派的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli)檔，以瞭解如何建立身分識別。

一旦您擁有要授與許可權的身分識別，若要這麼做，您可以使用 Azure 自訂角色定義，然後將使用者指派的受控識別指派為使用自訂角色定義。

[這裡](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)會詳細說明許可權，而範例會示範如何執行此操作。

> [注意！]先前使用 AIB 時，您會使用 AIB SPN，並將 SPN 許可權授與映射資源群組。 我們即將離開此模型，以允許未來的功能。 從2020年6月1日開始，映射產生器將不會接受沒有使用者指派之身分識別的範本，現有的範本將必須以使用者身分[識別](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#identity)重新提交至服務。 這裡的範例已示範如何建立使用者指派的身分識別，並將其新增至範本。

## <a name="costs"></a>費用
建立、建立和儲存映射時，您將會產生一些計算、網路和儲存體成本。 這些成本類似于手動建立自訂映射所產生的成本。 對於資源，您將以 Azure 費率向您收費。 

在映射建立過程中，會下載檔案並 `IT_<DestinationResourceGroup>_<TemplateName>` 將其儲存在資源群組中，這將會產生少量的儲存成本。 如果您不想要保留這些，請在映射組建之後刪除**映射範本**。
 
映射產生器會使用 D1v2 VM 大小、儲存體，以及 VM 所需的網路功能，來建立 VM。 這些資源會在建立程式期間持續，而且一旦影像產生器完成影像的建立後，就會刪除。 
 
Azure 映射產生器會將映射散發至您選擇的區域，這可能會產生網路輸出費用。
 
## <a name="next-steps"></a>後續步驟 
 
若要試用 Azure 映射產生器，請參閱建立[Linux](../articles/virtual-machines/linux/image-builder.md)或[Windows](../articles/virtual-machines/windows/image-builder.md)映射的文章。
 
