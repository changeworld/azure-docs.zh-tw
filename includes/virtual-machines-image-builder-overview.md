---
author: ''
ms.author: danielsollondon
ms.date: 08/03/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: daberry
ms.openlocfilehash: 5c028fc1abd77bda1a41857a7a7c77da1ad1b2d2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025941"
---
標準化虛擬機器 (VM) 映像可讓組織遷移至雲端，並確保部署的一致性。 映像通常包含預先定義的安全性和組態設定，以及必要的軟體。 設定您自己的映像處理管線需要一些時間、基礎結構和設定，但透過 Azure VM Image Builder，只要提供簡單的組態來描述您的映像、將其提交至服務，然後就會建立映像並加以散發。
 
Azure VM Image Builder (Azure Image Builder) 可讓您從以 Windows 或 Linux 為基礎的 Azure Marketplace 映像、現有的自訂映像或 Red Hat Enterprise Linux (RHEL) ISO 著手，並開始新增自己的自訂項目。 因為 Image Builder 建置在 [HashiCorp Packer](https://packer.io/) 上，所以您也可匯入現有的 Packer Shell 佈建工具指令碼。 您也可以在 [Azure 共用映像庫](../articles/virtual-machines/windows/shared-image-galleries.md)中，指定您要裝載映像的位置，作為受控映像或 VHD。

> [!IMPORTANT]
> Azure Image Builder 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="preview-features"></a>預覽功能

在預覽版中，支援下列功能：

- 建立金級基準映像 (包括您的最小安全性和公司組態)，並允許部門根據其需求進一步加以自訂。
- 修補現有的映像，Image Builder 可讓您持續修補現有的自訂映像。
- 將 Image Builder 連線到現有的虛擬網路，您便可連線到現有的設定伺服器 (DSC、Chef、Puppet 等)、檔案共用，或任何其他路由式伺服器/服務。
- 與 Azure 共用映像庫整合，可讓您全域散發、設定版本及調整映像，並為您提供映像管理系統。
- 與現有的映像建置管線整合，只要從管線呼叫 Image Builder，或使用簡單的預覽 Image Builder Azure DevOps 工作即可。
- 將現有的映像自訂管線遷移至 Azure。 使用您現有的指令碼、命令和程序來自訂映像。
- 建立 VHD 格式的映像以支援 Azure Stack。
 

## <a name="regions"></a>區域
Azure Image Builder 服務會在下列區域中提供預覽。 映像可以在這些區域之外散發。
- 美國東部
- 美國東部 2
- 美國中西部
- 美國西部
- 美國西部 2
- 北歐
- 西歐

## <a name="os-support"></a>OS 支援
AIB 將支援 Azure Marketplace 基底 OS 映像：
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6、7.7
- CentOS 7.6、7.7
- SLES 12 SP4
- SLES 15、SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise 多工作階段/專業版
- Windows 2016
- Windows 2019

不再提供 RHEL ISO 支援。

## <a name="how-it-works"></a>運作方式

Azure Image Builder 是完全受控的 Azure 服務，可供 Azure 資源提供者存取。 Azure Image Builder 程序有三個主要部分：來源、自訂和散發，這些都會在範本中表現。 下圖顯示元件及其部分屬性。 
 

**Image Builder 程序** 

![Azure Image Builder 程序的概念圖](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. 以 .json 檔案形式建立映像範本。 此 .json 檔案包含映像來源、自訂和散發相關資訊。 [Azure Image Builder GitHub 存放庫](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)中有多個範例。
1. 將其提交給服務，將會在您指定的資源群組中建立映像範本成品。 在背景中，Image Builder 會下載來源映像或 ISO，並視需要下載指令碼。 這些會儲存在您的訂用帳戶中自動建立的個別資源群組中，格式如下： IT_ \<DestinationResourceGroup> _ \<TemplateName> 。 
1. 建立映像範本後，您就可以建置映像。 在背景影像產生器中，會使用範本和來源檔案來建立 VM (預設大小： Standard_D1_v2) 、網路、公用 IP、NSG，以及 IT_ \<DestinationResourceGroup> _ 資源群組中的儲存體 \<TemplateName> 。
1. 在建立映射時，影像產生器會根據範本散發映射，然後刪除 IT_ \<DestinationResourceGroup> _ \<TemplateName> 資源群組中為此進程所建立的其他資源。


## <a name="permissions"></a>權限
當您註冊 (AIB) 時，這會授與建立、管理和刪除暫存資源群組 (IT_*) 的 AIB 服務權限，並具有在該群組中新增資源的權利，這些都是映像建置的必要項目。 這是在成功註冊期間，由您的訂用帳戶中提供的 AIB 服務主體名稱 (SPN) 所完成。

若要允許 Azure VM Image Builder 將映像散發至受控映像或共用映像庫，您必須建立 Azure 使用者指派的身分識別，其具有讀取和寫入映像的權限。 如果您要存取 Azure 儲存體，則需要讀取私人容器的權限。

一開始，您必須遵循[建立 Azure 使用者指派的受控識別](../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)文件，以了解如何建立身分識別。

一旦擁有您需要授與權限的身分識別，若要這麼做，您可使用 Azure 自訂角色定義，然後指派使用者所指派的受控識別以使用自訂角色定義。

[這裡](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)會詳細說明權限，而範例會示範其實作方式。

> [!Note]
> 先前使用 AIB 時，您會使用 AIB SPN，並將 SPN 權限授予映像資源群組。 我們即將脫離此模型，以允許使用未來的功能。 從 2020 年 5 月 26 日起，Image Builder 不會接受沒有使用者所指派身分識別的範本，必須將現有的範本重新提交至具有[使用者身分識別](../articles/virtual-machines/linux/image-builder-json.md?bc=%252fazure%252fvirtual-machines%252fwindows%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json#identity)的服務。 這裡的範例已示範如何建立使用者指派的身分識別，並將其新增至範本。 如需詳細資訊，請檢閱這份有關這項變動和版本更新的[文件](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information)。

## <a name="costs"></a>費用
使用 Azure Image Builder 建立、建置和儲存映像時，您會產生一些計算、網路和儲存成本。 這些成本類似於手動建立自訂映像所產生的成本。 對於各項資源，系統會以您的 Azure 費率向您收費。 

在映像建立過程中，檔案會下載並儲存在 `IT_<DestinationResourceGroup>_<TemplateName>` 資源群組中，這會產生少許的儲存成本。 如果您不想保留這些檔案，請在映像建置之後，刪除 [映像範本]。
 
Image Builder 會建立使用 D1v2 VM 大小的 VM，以及該 VM 所需的儲存體和網路功能。 這些資源將會在建置期間持續存在，而一旦 Image Builder 完成映像建立，就會遭到刪除。 
 
Azure Image Builder 會將映像散發至您選擇的區域，這可能會產生網路輸出費用。

## <a name="hyper-v-generation"></a>Hyper-V 世代
影像產生器目前只支援建立 Hyper-v 世代 (Gen1) 1 映射至 Azure 共用映射庫 (SIG) 或受控映射。 如果您想要建立 Gen2 映射，則需要使用來源 Gen2 映射，並將其散發至 VHD。 之後，您必須從 VHD 建立受控映射，並將其插入至 SIG 作為 Gen2 映射。
 
## <a name="next-steps"></a>後續步驟 
 
若要試用 Azure Image Builder，請參閱建置 [Linux](../articles/virtual-machines/linux/image-builder.md) 或 [Windows](../articles/virtual-machines/windows/image-builder.md) 映像的文章。
