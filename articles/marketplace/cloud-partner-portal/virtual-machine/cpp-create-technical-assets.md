---
title: 建立 Azure Marketplace 虛擬機器供應專案的技術資產
description: 說明如何在 Azure Marketplace 中為虛擬機器供應項目建立技術資產。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: dsindona
ms.openlocfilehash: c6ca017e18a83f0745140cba15d9c894e5a21c32
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148126"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>建立虛擬機器供應項目的技術資產

> [!IMPORTANT]
> 從2020年4月13日開始，我們會開始將您的 Azure 虛擬機器供應專案移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 請依照[建立 Azure 虛擬機器技術資產](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)中的指示來管理您遷移的供應專案。

本節會逐步說明如何建立和設定 Azure Marketplace 虛擬機器 (VM) 供應項目的技術資產。  虛擬機器包含兩個元件：解決方案的虛擬硬碟 (VHD)，以及選擇性的關聯資料磁碟。  

- *虛擬硬碟 (VHD)* 內有會與 Azure Marketplace 供應項目一同部署的作業系統和解決方案。 取決於虛擬機器為 Linux 架構、Windows 架構或是自訂架構，準備 VHD 的程序亦有差異。
- *資料磁碟*代表虛擬機器專用的永續性儲存體。 *請勿*使用解決方案 VHD (例如 `C:` 磁碟機) 來儲存永續性資訊。

VM 映像包含一個作業系統磁碟以及零或多個資料磁碟。 每個磁碟都需要一個 VHD。 即使空的資料磁碟也需要建立一個 VHD。
請務必設定虛擬機器作業系統、虛擬機器大小、要開啟的連接埠，以及數量上限為 15 個的附加資料磁碟。

> [!TIP] 
> 無論您使用哪一種作業系統，您只能加入 SKU 所需的最少數目資料磁碟。 客戶無法在部署時移除屬於映像一部分的磁碟，但可隨時在部署期間或之後新增磁碟。 

> [!IMPORTANT]
> *請勿變更新映像版本中的磁碟計數。*  如果您必須重新設定映像中的資料磁碟，請定義新的 SKU。 對於自動調整大小、透過 Azure Resource Manager 範本自動部署解決方案和其他情節而言，發佈磁碟計數相異的新映像版本有可能會毀損以新映像版本為基礎的新部署。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>基本技術知識

設計、建立及測試這些資產需要一些時間，而且需要 Azure 平臺和用來建立供應專案之技術的技術知識。 除了您的解決方案網域以外，您的工程小組也應具備下列 Microsoft 技術的知識： 
-    對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解 
-    如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)
-    具備 [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、[Azure 儲存體](https://azure.microsoft.com/services/?filter=storage)和 [Azure 網路](https://azure.microsoft.com/services/?filter=networking)的運用知識
-    具備 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 的運用知識
-    具備 [JSON](https://www.json.org/) 的運用知識


## <a name="suggested-tools"></a>建議的工具 

選擇下列其中一種指令碼環境，或兩者均使用，以協助管理 VHD 和虛擬機器：
-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Azure CLI](https://docs.microsoft.com/cli/azure)

此外也建議您新增下列工具至開發環境： 

-    [Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    延伸模組：[Azure Resource Manager 工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    延伸模組：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    延伸模組：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

也建議您至 [Azure 開發人員工具](https://azure.microsoft.com/tools/) 頁面查看可用的工具，若您使用 Visual Studio，也請查看 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)。


## <a name="next-steps"></a>後續步驟

在本節的後續文章中，會逐步說明建立及註冊虛擬機器資產的步驟：

1. [建立 Azure 相容虛擬硬碟](./cpp-create-vhd.md) (英文) 說明如何建立與 Azure 相容的 Linux 或 Windows 型 VHD。  包括調整大小、修補和準備虛擬機器以供上傳等最佳做法。

2. [連線至虛擬機器](./cpp-connect-vm.md) (英文) 說明如何遠端連線至新建立的虛擬機器並順利登入。  本文亦會說明如何停止虛擬機器以節省使用成本。

3. [設定虛擬機器](./cpp-configure-vm.md) (英文) 說明如何選擇正確的 VHD 大小、一般化映像、套用最近的更新 (修補程式)，以及排程自訂設定。

4. [從虛擬硬碟部署虛擬機器](./cpp-deploy-vm-vhd.md) (英文) 說明如何以 Azure 部署的 VHD 註冊虛擬機器。  文中會列出必要的工具，並說明如何使用這些工具來建立使用者虛擬機器映像，然後再使用 [Microsoft Azure 入口網站](https://ms.portal.azure.com/)或 PowerShell 指令碼將該映像部署至 Azure。 

5. [認證虛擬機器映像](./cpp-certify-vm.md) (英文) 說明如何測試和提交 VM 映像供 Azure Marketplace 認證。 其中會說明從何處取得*適用於 Azure 認證的認證測試工具* (英文) ，以及如何使用此工具來認證虛擬機器映像。 

6. [取得 SAS URI](./cpp-get-sas-uri.md) (英文) 說明如何取得適用於您 VM 映像的共用存取簽章 (SAS)。
 
在[一般共用存取簽章 URL 問題](./cpp-common-sas-url-issues.md) (英文) 支援文章中，會列出您在使用 SAS URI 時可能會遇到的一些問題，以及可能的對應解決方案。

完成以上所有步驟後，即準備完畢，可[發佈虛擬機器供應項目](./cpp-publish-offer.md) (英文) 至 Azure Marketplace。
