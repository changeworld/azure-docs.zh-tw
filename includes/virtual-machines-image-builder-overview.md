---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 658910dc4291375c7b2ab22e88c599b970b885af
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80419204"
---
標準化虛擬機 (VM) 映像允許組織遷移到雲並確保部署的一致性。 映像通常包括預定義的安全和配置設置以及必要的軟體。 設置自己的映射管道需要時間、基礎結構和設置,但使用 Azure VM 映射生成器,只需提供描述映射的簡單配置,將其提交到服務,並生成映射並分發映射即可。
 
Azure VM 映射生成器(Azure 映像產生器)允許您從基於 Windows 或 Linux 的 Azure 應用商店映像、現有自定義映射或紅帽企業 Linux (RHEL) ISO 開始,並開始添加自己的自定義項。 由於映射生成器是建立在[HashiCorp 打包器](https://packer.io/)之上的,因此還可以導入現有的打包器外殼預配器腳本。 您還可以在[Azure 共用映射庫中](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)指定希望將映射託管的位置作為託管映射或 VHD。

> [!IMPORTANT]
> Azure 映射生成器當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="preview-features"></a>預覽功能

對於預覽,支援以下功能:

- 創建黃金基線映射,包括您的最低安全性和企業配置,並允許各部門進一步自定義它以滿足其需求。
- 修補現有圖像,圖像生成器將允許您持續修補現有的自定義圖像。
- 將映射生成器連接到現有虛擬網路,以便連接到現有配置伺服器(DSC、Chef、Puppet等)、檔案共享或任何其他可路由的伺服器/服務。
- 與 Azure 共用映像庫整合,允許您全域分發、版本和縮放映射,並為您提供映射管理系統。
- 與現有映射生成管道整合,只需從管道呼叫映像生成器,或使用簡單的預覽映射生成器 Azure DevOps 任務。
- 將現有映射自定義管道遷移到 Azure。 使用現有文稿、命令和進程自定義映射。
- 以 VHD 格式創建圖像。
 

## <a name="regions"></a>區域
Azure 映射生成器服務將在這些區域中預覽。 圖像可以分佈在這些區域之外。
- 美國東部
- 美國東部 2
- 美國中西部
- 美國西部
- 美國西部 2
- 北歐
- 西歐

## <a name="os-support"></a>作業系統支援
AIB 將支援 Azure 應用商店基礎作業系統映像:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 企業/企業多會話/專業
- Windows 2016
- 視窗 2019

RHEL ISO 支援正在被棄用,請查看範本文檔以瞭解更多詳細資訊。

## <a name="how-it-works"></a>運作方式


![Azure 映像產生器的概念繪圖](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure 映射生成器是一個完全託管的 Azure 服務,由 Azure 資源提供程式訪問。 Azure 映射生成器過程有三個主要部分:源、自定義和分發,這些部分在範本中表示。 下圖顯示了元件及其某些屬性。 
 


**影像產生器程序** 

![Azure 映像產生器程序的概念繪圖](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. 將圖像範本創建為 .json 檔。 此 .json 檔包含有關映射源、自定義項和分發的資訊。 [Azure 映射生成器 GitHub 儲存庫](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)中有多個範例。
1. 將其提交到服務,這將在指定的資源組中創建圖像範本專案。 在後台,圖像生成器將根據需要下載源映射或 ISO 和文稿。 這些資源存儲在訂閱中自動創建的單獨資源組中,其格式為:IT_\<目標資源組>_\<範本名稱>。 
1. 創建圖像範本後,可以生成映射。 在後台,映射生成器使用範本和源檔在IT_\<目標資源組>_\<範本名稱>資源組中創建 VM(預設大小:Standard_D1_v2)、網路、公共 IP、NSG 和儲存。
1. 作為映射創建的一部分,圖像生成器會根據範本分發映射,然後刪除為進程創建的IT_\<目標資源組>_\<範本名稱>資源組中的其他資源。


## <a name="permissions"></a>權限

要允許 Azure VM 映射生成器將映像分發到託管映射或共用映射庫,您需要為資源組中的服務" Azure 虛擬機器映射生成器」(應用 ID:cf32a0cc-373c-47c9-9156-0db11f6a6dfc)提供"參與者"許可權。 

如果使用現有的自定義託管映射或映射版本,則 Azure 映射生成器將需要對這些資源組的最小"讀取器"存取許可權。

可以使用 Azure CLI 分配存取權限:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

您可以使用 PowerShell 分配存取權限:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


如果未找到服務帳戶,則可能意味著要添加角色分配的訂閱尚未為資源提供程式註冊。


## <a name="costs"></a>費用
使用 Azure 映像產生器創建、構建和儲存映像時,將會產生一些計算、網路和儲存成本。 這些成本與手動創建自定義映射的成本類似。 對於資源,您將按 Azure 費率收費。 

在映射創建過程中,檔被下載並存儲在`IT_<DestinationResourceGroup>_<TemplateName>`資源組中,這將產生較小的儲存成本。 如果不想保留這些,請在映像產生後移除**映像樣本**。
 
映射生成器使用 D1v2 VM 大小以及 VM 所需的儲存和網路創建 VM。 這些資源將持續在生成過程的持續時間內,並在映射生成器完成創建映射後刪除。 
 
Azure 映射生成器將映射分發到所選區域,這可能會產生網路出口費用。
 
## <a name="next-steps"></a>後續步驟 
 
要嘗試 Azure 映像產生器,請參閱有關建[構 Linux](../articles/virtual-machines/linux/image-builder.md)或[Windows](../articles/virtual-machines/windows/image-builder.md)映像的文章。
 
 
