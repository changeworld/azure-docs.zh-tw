---
title: 為 Azure 應用商店驗證 VM 映像
description: 說明如何測試和提交 VM 映像供 Azure Marketplace 認證。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: dsindona
ms.openlocfilehash: ce1e001b9cafff83a3f9bf546d6903cc4a4f450f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273489"
---
# <a name="certify-your-vm-image"></a>認證您的 VM 映像

> [!IMPORTANT]
> 從 2020 年 4 月 13 日開始,我們將開始將 Azure 虛擬機產品的管理移到合作夥伴中心。 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照[Azure VM 映像認證](https://aks.ms/CertifyVMimage)中的說明來管理遷移的優惠。

建立和部署虛擬機器 (VM) 後，必須測試並提交 VM 映像以供 Azure Marketplace 認證。 本文說明可在何處取得 *Azure 認證的認證測試工具*，並解釋如何使用此工具認證您的 VM 映像，以及如何將驗證結果上傳至您 VHD 所在的 Azure 容器。 


## <a name="download-and-run-the-certification-test-tool"></a>下載並執行認證測試工具

Azure 認證的認證測試工具是以本機 Windows 電腦執行，但可測試 Azure 為基礎的 Windows 或 Linux 虛擬機器。  測試工具會驗證您的使用者 VM 映像是否與 Microsoft Azure 相容，確認您 VHD 的準備工作是否有依循指示並達到要求。 此工具會輸出相容性報告，需將此報告上傳至 [Cloud Partner 入口網站](https://cloudpartner.azure.com)以要求 VM 認證。

1. 下載並安裝最新版本的 [Azure 認證的認證測試工具](https://www.microsoft.com/download/details.aspx?id=44299)。 
2. 開啟認證工具，然後按一下 [啟動新測試]****。
3. 從 [測試資訊] **** 畫面，輸入測試回合的**測試名稱**。
4. 選取您 VM 適用的 [平台]****，亦即 `Windows Server` 或 `Linux`。 所選的平台會影響其餘的選項。
5. 如果您的 VM 使用此資料庫服務，請選取 [Test for Azure SQL Database]\(Azure SQL Database 測試\)**** 核取方塊。

   ![認證測試工具初始頁面](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>將認證工具連線至 VM 映像

  此工具會透過 [PowerShell](https://docs.microsoft.com/powershell/) 連線至以 Windows 為基礎的虛擬機器，並透過 [SSH.Net](https://www.ssh.com/ssh/protocol/) 連線至 Linux 虛擬機器。

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>將認證工具連線至 Linux VM 映像

1. 選取 [SSH 驗證]**** 模式：`Password Authentication` 或 `key File Authentication`。
2. 如果使用以密碼為基礎的驗證，請輸入**虛擬機器 DNS 名稱**、**使用者名稱**和**密碼**。  或是也可以變更預設的 [SSH 連接埠]**** 號碼。

     ![Linux VM 映像的密碼驗證](./media/publishvm_026.png)

3. 如果使用以金鑰檔案為基礎的驗證，請輸入**虛擬機器 DNS 名稱**、**使用者名稱**和**私密金鑰**位置等值。  或是也可以提供 [複雜密碼]**** 或是變更預設的 [SSH 連接埠]**** 號碼。

     ![Linux VM 映像的檔案驗證](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**將認證工具連接到 Windows 型 VM 映像**
1. 輸入完整的**虛擬機器 DNS 名稱** (例如：`MyVMName.Cloudapp.net`)。
2. 輸入 [使用者名稱]**** 和 [密碼]**** 的值。

   ![Windows VM 映像的密碼驗證](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>執行認證測試

在認證工具中提供適用於您 VM 映像的參數值後，請選取 [測試連線]****，以確認至 VM 的連線是否有效。 驗證連線後，選取 [下一步] **** 啟動測試。  測試完成時，資料表會顯示測試結果 (通過/失敗/警告)。  下列範例顯示的是 Linux 虛擬機器測試的測試結果。 

![Linux VM 映像的認證測試結果](./media/publishvm_029.png)

如果任何一項測試失敗，您的映像即*未*通過認證。 此時請檢查需求與失敗訊息，按指示進行變更，然後再重新執行測試。 

執行自動化測試後，系統會要求您在**問卷**畫面上，提供 VM 映像的其他資訊。  其中有兩個索引標籤必須完成。  [一般評量]**** 索引標籤是**是非**題，而 [核心自訂]**** 則是選擇題和簡答題。  請完成兩個索引標籤上的問題，然後選取 [下一步]****。

![認證工具問卷](./media/publishvm_030.png)

您可在最後一個畫面提供其他資訊，像是 Linux VM 映像的 SSH 存取資訊，或是尋找例外狀況時所需的評量失敗說明。 

除了問卷調查的答案之外，最後按一下 [產生報告]****，還可下載已執行測試案例的測試結果和記錄檔。 請將結果儲存在與您 VHD 相同的容器中。

![儲存認證測試結果](./media/publishvm_031.png)


## <a name="next-steps"></a>後續步驟

接下來，您必須[針對每個提交至市集的 VHD 產生統一資源識別項](./cpp-get-sas-uri.md)。 
