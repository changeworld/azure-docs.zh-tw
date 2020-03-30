---
title: 設置公共標識
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何建立可跨多個資料科學虛擬機器使用的一般使用者帳戶。 您可以使用 Azure Active Directory 或內部部署 Active Directory，對資料科學虛擬機器驗證使用者。
keywords: 深度學習, 資料科學工具, 資料科學虛擬機器, 地理空間分析, 小組資料科學流程
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70208139"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>在資料科學虛擬機器上設置通用標識

在 Microsoft Azure 虛擬機器 （VM）（包括資料科學虛擬機器 （DSVM） 中，您可以在預配 VM 時創建本地使用者帳戶。 接著，使用者便可使用這些認證向 VM 進行驗證。 如果使用者需要訪問多個 VM，則管理認證可能會非常麻煩。 一個很好的解決方案是通過基於標準的標識提供程式部署通用使用者帳戶和管理。 通過此方法，可以使用一組憑據訪問 Azure 上的多個資源，包括多個 DSVM。

Active Directory 是一種流行的標識提供程式，在 Azure 上作為雲服務和本地目錄都支援。 您可以使用 Azure Active Directory (Azure AD) 或內部部署 Active Directory，在獨立的 DSVM 上或 Azure 虛擬機器擴展集內的 DSVM 叢集上驗證使用者。 做法是將 DSVM 執行個體加入 Active Directory 網域。

如果您已有活動目錄，則可以將其用作公共標識提供程式。 如果沒有活動目錄，則可以通過[Azure 活動目錄域服務](https://docs.microsoft.com/azure/active-directory-domain-services/)（Azure AD DS） 在 Azure 上運行託管活動目錄實例。

[Azure AD](https://docs.microsoft.com/azure/active-directory/)的文檔提供了詳細的[管理說明](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)，包括有關將 Azure AD 連接到本地目錄（如果有）的指導。

本文介紹如何使用 Azure AD DS 在 Azure 上設置完全託管的活動目錄域服務。 然後，您可以將 DSVM 加入託管的活動目錄域。 此方法使使用者能夠通過公共使用者帳戶和憑據訪問 DSVM（和其他 Azure 資源）池。

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>在 Azure 上設定完全受控的 Active Directory 網域

Azure AD DS 可透過在 Azure 上提供完全受控的服務，讓您輕鬆地管理您的身分識別。 在這個 Active Directory 網域上，您會管理使用者和群組。 要在目錄中設置 Azure 託管的活動目錄域和使用者帳戶，請按照以下步驟操作：

1. 在 Azure 入口網站中，將使用者新增至 Active Directory： 

   1. 使用目錄的全域管理員的帳戶登錄到[Azure 活動目錄管理中心](https://aad.portal.azure.com)。
    
   1. 選取 [Azure Active Directory]，然後選取 [使用者和群組]。********
    
   1. 在 **"使用者"和"組**"中，選擇 **"所有使用者**"，然後選擇 **"新使用者**"。
   
           The **User** pane opens:
      
      ![[使用者] 窗格](./media/add-user.png)
    
   1. 輸入使用者的詳細資料，例如「名稱」**** 和「使用者名稱」****。 使用者名稱的網域名稱部分必須是初始預設網域名稱 "[網域名稱].onmicrosoft.com"，或是已驗證的非同盟[自訂網域名稱](../../active-directory/add-custom-domain.md)，例如"contoso.com"。
    
   1. 複製或記下產生的使用者密碼，以便在此程序完成後，將它提供給使用者。
    
   1. (選擇性) 您可以開啟使用者的 [設定檔]****、[群組]**** 或 [目錄角色]****，然後在其中填入資訊。 
    
   1. 在 **"使用者**"下，選擇 **"創建**"。
    
   1. 安全地將生成的密碼分發給新使用者，以便他們可以登錄。

1. 建立 Azure AD DS 執行個體。 使用 Azure 門戶（"創建實例和配置基本設置"部分）按照[啟用 Azure 活動目錄域服務](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)中的說明進行操作。 請務必更新 Active Directory 中現有的使用者密碼，以便同步 Azure AD DS 中的密碼。 將 DNS 添加到 Azure AD DS 也很重要，如該部分中的"完成 Azure 門戶"基礎視窗中的欄位以創建 Azure AD DS 實例"下所述。

1. 在上一步的"創建和配置虛擬網路"部分創建的虛擬網路中創建單獨的 DSVM 子網。
1. 在 DSVM 子網中創建一個或多個 DSVM 實例。
1. 按照[說明](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm )將 DSVM 添加到活動目錄。 
1. 裝載 Azure 檔共用以承載家庭或筆記本目錄，以便可以將工作區裝載在任何電腦上。 （如果您需要嚴格的檔級許可權，則需要在一個或多個 VM 上運行網路檔案系統 [NFS]。

   1. [建立 Azure 檔案服務共用](../../storage/files/storage-how-to-create-file-share.md)。
    
   2.  將此共用裝載到 Linux DSVM 上。 在 Azure 門戶中的存儲帳戶中選擇"為 Azure 檔共用 **"** 時，將顯示要在 Linux DSVM 上的 bash shell 中運行的命令。 此命令看起來如下︰
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. 例如，假設您在 /data/工作區中裝載了 Azure 檔共用。 現在，為共用中的每個使用者創建目錄：/資料/工作區/使用者 1、/資料/工作區/使用者 2 等。 在每個使用者的工作區中建立 `notebooks` 目錄。 
1. 在 `$HOME/userx/notebooks/remote` 中建立 `notebooks` 的符號連結。   

現在，活動目錄實例中的使用者託管在 Azure 中。 通過使用 Active Directory 認證，使用者可以登錄到加入 Azure AD DS 的任何 DSVM（SSH 或 JupyterHub）。 由於使用者工作區位於「Azure 檔案服務」共用上，因此使用者在使用 JupyterHub 時，可以從任何 DSVM 存取其筆記本和其他工作。

針對自動調整，您可以使用虛擬機器擴展集來建立 VM 的集區，這些 VM 都是以這種方式加入網域，而且已掛接共用磁碟。 使用者可以登錄到虛擬機器規模集中的任何可用電腦，並可以訪問保存其筆記本的共用磁片。 

## <a name="next-steps"></a>後續步驟

* [安全地儲存用來存取雲端資源的認證](dsvm-secure-access-keys.md)



