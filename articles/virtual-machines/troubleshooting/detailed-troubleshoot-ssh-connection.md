---
title: Azure VM 的詳細 SSH 疑難排解 | Microsoft Docs
description: 針對連線到 Azure 虛擬機器的問題更詳細的 SSH 疑難排解步驟
keywords: ssh 連線被拒, ssh 錯誤, azure ssh, SSH 連線失敗
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ee6d437915f6c87ce9ef5f9c711d90793a96048c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920122"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>連線到 Azure 中 Linux VM 之問題的詳細 SSH 疑難排解步驟
SSH 用戶端無法連線至 VM 上的 SSH 服務，可能涉及許多原因。 如果您已經完成較為 [一般的 SSH 疑難排解步驟](troubleshoot-ssh-connection.md)，您必須進一步針對連線問題進行疑難排解。 這篇文章會引導您完成詳細的疑難排解步驟，以判斷 SSH 連線失敗的位置和解決方法。

## <a name="take-preliminary-steps"></a>採取預備步驟
下列圖表顯示相關的元件。

![顯示 SSH 服務元件的圖表](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

下列步驟可協助您隔離失敗的來源，並找出解決方案或因應措施。

1. 在入口網站中檢查 VM 的狀態。
   在 [Azure 入口網站](https://portal.azure.com)中，選取 [虛擬機器] > VM 名稱。

   VM 的狀態窗格應該會顯示 [正在執行]。 向下捲動以顯示計算、儲存體和網路資源的近期活動。

2. 選取 [設定] 以檢查端點、IP 位址、網路安全性群組及其他設定。

   VM 應該有一個為 SSH 流量定義的端點，您可以在 [端點] 或 **[網路安全性群組](../../virtual-network/security-overview.md)** 中檢視該端點。 使用 Resource Manager 來建立之 VM 中的端點會儲存在網路安全性群組中。 請確認是否已將規則套用至網路安全性群組，以及子網路中是否有參考這些規則。

若要確認網路連線，請檢查設定的端點，並判斷您是否可以透過另一個通訊協定 (例如 HTTP 或另一個服務) 連線到 VM。

在這些步驟之後，請再試一次 SSH 連線。

## <a name="find-the-source-of-the-issue"></a>找出問題來源
如果下列方面發生問題或設定錯誤，您電腦上的 SSH 用戶端可能就無法連線到 Azure VM 上的 SSH 服務：

* [SSH 用戶端電腦](#source-1-ssh-client-computer)
* [組織邊緣裝置](#source-2-organization-edge-device)
* [雲端服務端點和存取控制清單 (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [網路安全性群組](#source-4-network-security-groups)
* [以 Linux 為基礎的 Azure VM](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>來源 1：SSH 用戶端電腦
若要排除電腦為失敗來源的可能性，請確認您的電腦是否能 SSH 連線至另一部以 Linux 為基礎的內部部署電腦。

![強調 SSH 用戶端電腦元件的圖表](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

如果連線失敗，請檢查您電腦上是否有下列問題：

* 正封鎖輸入或輸出 SSH 流量的本機防火牆設定 (TCP 22)
* 正阻止 SSH 連線的本機安裝用戶端 Proxy 軟體
* 正阻止 SSH 連線的本機安裝網路監視軟體
* 其他類型的安全性軟體，這會監視流量或允許/不允許特定類型的流量。

如果有上述任何情況發生，請暫時停用該軟體，然後再嘗試 SSH 連線到內部部署的電腦，以找出電腦連線被封鎖的原因。 接著，請和網路管理員合作，修正軟體設定以允許 SSH 連線。

如果您使用憑證驗證，請確認您在主目錄中擁有 ssh 資料夾的下列權限：

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (或您儲存私密金鑰的任何其他檔案)
* Chmod 644 ~/.ssh/known_hosts (包含您已透過 SSH 連接的主機)

## <a name="source-2-organization-edge-device"></a>來源 2：組織邊緣裝置
若要排除組織邊緣裝置為失敗來源的可能性，請確認直接連線到網際網路的電腦能 SSH 連線到您的 Azure VM。 如果您是透過站對站 VPN 或 Azure ExpressRoute 連線來存取 VM，請跳到 [來源 4：網路安全性群組](#nsg)。

![強調組織邊緣裝置的圖表](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

如果您沒有直接連線到網際網路的電腦，則請將新的 Azure VM 建立在它自己的資源群組或雲端服務中，並且使用這個新的 VM。 如需詳細資訊，請參閱 [在 Azure 中建立執行 Linux 的虛擬機器](../linux/quick-create-cli.md)。 當您完成測試後，請刪除此資源群組或 VM 及雲端服務。

如果您可以對直接連線到網際網路的電腦建立 SSH 連線，請檢查組織邊緣裝置的下列項目：

* 內部防火牆是否導致網際網路 SSH 流量受到封鎖
* Proxy 伺服器是否阻止 SSH 進行連線
* 在邊緣網路裝置上執行的入侵偵測或網路監視軟體是否阻止 SSH 進行連線

請和網路管理員合作，修正組織邊緣裝置的設定，允許網際網路的 SSH 流量。

## <a name="source-3-cloud-service-endpoint-and-acl"></a>來源 3：雲端服務端點和 ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> 此來源僅適用於使用傳統部署模型所建立的 VM。 針對使用 Resource Manager 來建立的 VM，請跳到 [來源 4：網路安全性群組](#nsg)。

若要排除雲端服務端點和 ACL 為失敗來源的可能性，請確認同一個虛擬網路中的其他 Azure VM 是否能使用 SSH 進行連線。

![強調雲端服務端點和 ACL 的圖表](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

如果您在相同的虛擬網路中沒有其他 VM，您可以輕鬆建立一部 VM。 如需詳細資訊，請參閱 [使用 CLI 在 Azure 上建立 Linux VM](../linux/quick-create-cli.md)。 當您完成測試後，請刪除額外的 VM。

如果您可以與相同虛擬網路中的 VM 建立 SSH 連線，請檢查下列方面：

* **目標 VM 上的 SSH 流量端點組態。** 此端點的私用 TCP 連接埠應符合 VM 上 SSH 服務正在接聽的 TCP 連接埠， 預設連接埠為 22。 請在 Azure 入口網站中，選取 [虛擬機器] > VM 名稱 > [設定] > [端點]，來確認 SSH TCP 連接埠號碼。
* **目標虛擬機器上的 SSH 流量端點 ACL。** ACL 可讓您指定要根據來源 IP 位址允許或拒絕來自網際網路的連入流量。 設定錯誤的 ACL 會阻止送至端點的連入 SSH 流量。 檢查您的 ACL，確保允許來自您的 Proxy 或其他邊緣伺服器之公用 IP 位址的連入流量。 如需詳細資訊，請參閱 [關於網路存取控制清單 (ACL)](../../virtual-network/virtual-networks-acl.md)。

若要排除端點是問題來源的可能性，請移除目前的端點、建立另一個端點，然後指定 SSH 名稱 (TCP 連接埠 22 作為公用及私用連接埠號碼)。 如需詳細資訊，請參閱 [在 Azure 中設定虛擬機器的端點](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>來源 4：網路安全性群組
網路安全性群組可讓您更精確地控制受允許的輸入和輸出流量。 您可以在 Azure 虛擬網路中建立跨越子網路和雲端服務的規則。 請檢查您的網路安全性群組規則，以確保允許往來網際網路的 SSH 流量。
如需詳細資訊，請參閱 [關於網路安全性群組](../../virtual-network/security-overview.md)。

您也可以使用「IP 確認」來驗證 NSG 組態。 如需詳細資訊，請參閱 [Azure 網路監視概觀](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)。 

## <a name="source-5-linux-based-azure-virtual-machine"></a>來源 5：以 Linux 為基礎的 Azure 虛擬機器
最後一個可能的問題來源就是 Azure 虛擬機器本身。

![強調以 Linux 為基礎的 Azure 虛擬機器的圖表](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

如果您還沒這麼做，請依照 [為以 Linux 為基礎的虛擬機器重設密碼](../linux/reset-password.md)中的指示操作。

再次嘗試從您的電腦連線。 如果仍然失敗，以下是一些可能的問題：

* SSH 服務未在目標虛擬機器上執行。
* SSH 服務未在 TCP 連接埠 22 上接聽。 若要進行測試，請在本機電腦上安裝 telnet 用戶端，然後執行 "telnet *cloudServiceName*.cloudapp.net 22"。 此步驟可判斷虛擬機器是否允許對 SSH 端點進行輸入和輸出通訊。
* 目標虛擬機器上的本機防火牆具有防止輸入或輸出 SSH 流量的規則。
* 在 Azure 虛擬機器上執行的入侵偵測或網路監視軟體正在阻止 SSH 連線。

## <a name="additional-resources"></a>其他資源
如需疑難排解應用程式存取的詳細資訊，請參閱[針對存取在 Azure 虛擬機器上執行的應用程式進行疑難排解](../linux/troubleshoot-app-connection.md)
