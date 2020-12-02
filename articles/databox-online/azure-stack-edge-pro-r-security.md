---
title: Azure Stack Edge Pro R 安全性 |Microsoft Docs
description: 描述保護您 Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 裝置、服務和內部部署和雲端中之資料的安全性和隱私權功能。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: f7d81d14ca561e6d4d897994088b2fc01b2c7701
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466379"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 的安全性和資料保護

[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]

當您採用新的技術時，安全性是一項重要的考慮，特別是當該技術與機密或專屬的資料搭配使用時。 Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 可協助您確保只有授權的實體可以查看、修改或刪除您的資料。

本文說明 Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 安全性功能，可協助保護每個解決方案元件和其中儲存的資料。

解決方案包含四個彼此互動的主要元件：

- 裝載 **在 Azure 公用或 Azure Government 雲端中的 Azure Stack Edge 服務**。 您用來建立裝置訂單的管理資源、設定裝置，然後追蹤訂單的完成。
- **Azure Stack Edge 堅固的裝置**。 傳送給您的堅固實體裝置，可讓您將內部部署資料匯入 Azure 公用或 Azure Government 雲端。 裝置可 Azure Stack Edge Pro R 或 Azure Stack Edge 迷你 R。
- **連線到裝置的用戶端/主機**。 基礎結構中的用戶端，可連線到裝置並包含需要保護的資料。
- **雲端儲存體**。 Azure 雲端平臺中儲存資料的位置。 此位置通常是連結至您所建立之 Azure Stack Edge 資源的儲存體帳戶。

## <a name="service-protection"></a>服務保護

Azure Stack Edge 服務是在 Azure 中託管的管理服務。 此服務可用來設定和管理裝置。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>裝置保護

堅固裝置是一種內部部署裝置，可在本機處理資料，然後將資料傳送至 Azure，以協助轉換您的資料。 您的裝置：

- 需要啟用金鑰才能存取 Azure Stack Edge 服務。
- 會受到裝置密碼的任何時間保護。
- 是鎖定的裝置。 裝置基礎板管理控制器 (BMC) 和 BIOS 都受到密碼保護。 BMC 受到受限的使用者存取保護。
- 啟用安全開機，可確保裝置只會使用 Microsoft 提供的受信任軟體開機。
- Windows Defender 應用程式控制 (WDAC) 執行。 WDAC 可讓您只執行您在程式碼完整性原則中定義的受信任應用程式。
- 具有可執行以硬體為基礎、安全性相關功能的信賴平臺模組 (TPM) 。 具體而言，TPM 會管理及保護必須保存在裝置上的秘密和資料。
- 只有必要的埠會在裝置上開啟，而且會封鎖所有其他埠。 如需詳細資訊，請參閱 [裝置的埠需求](azure-stack-edge-pro-r-system-requirements.md) 清單。
- 會記錄裝置硬體和軟體的所有存取。 
    - 針對裝置軟體，系統會收集來自裝置之輸入和輸出流量的預設防火牆記錄檔。 這些記錄會配套在支援套件中。
    - 針對裝置硬體，裝置上會記錄所有裝置底座的事件，例如裝置底座的開啟和關閉。

    如需包含硬體和軟體入侵事件的特定記錄檔，以及如何取得記錄檔的詳細資訊，請移至 [收集 advanced security 記錄](azure-stack-edge-gpu-troubleshoot.md)檔。


### <a name="protect-the-device-via-activation-key"></a>透過啟用金鑰保護裝置

只有授權的 Azure Stack Edge Pro R 或 Azure Stack Edge 迷你 R 裝置才能加入您在 Azure 訂用帳戶中建立的 Azure Stack Edge 服務。 若要授權裝置，您必須使用啟用金鑰來啟用 Azure Stack Edge 服務的裝置。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

如需詳細資訊，請參閱 [取得啟用金鑰](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>透過密碼保護裝置

密碼可確保只有經過授權的使用者可以存取您的資料。 Azure Stack Edge Pro R 裝置開機時，會處於鎖定狀態。

您可以：

- 透過瀏覽器連線至裝置的本機 web UI，然後提供密碼以登入裝置。
- 從遠端連線至透過 HTTP 的裝置 PowerShell 介面。 預設會開啟遠端系統管理。 遠端系統管理也會設定為只使用足夠的系統管理 (JEA) 來限制使用者可以執行的動作。 接著，您可以提供裝置密碼以登入裝置。 如需詳細資訊，請參閱 [從遠端連線到您的裝置](azure-stack-edge-gpu-connect-powershell-interface.md)。
- 裝置上的本機 Edge 使用者對裝置的存取權有限，可進行初始設定和疑難排解。 在裝置、資料傳輸和儲存體上執行的計算工作負載，都可以從 Azure 公用或政府入口網站存取雲端中的資源。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- 使用本機 web UI 來 [變更密碼](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password)。 如果您變更密碼，請務必通知所有遠端存取使用者，讓他們不會在登入時發生問題。

### <a name="establish-trust-with-the-device-via-certificates"></a>透過憑證建立裝置信任

Azure Stack Edge 堅固裝置可讓您攜帶自己的憑證，並安裝這些憑證以用於所有公用端點。 如需詳細資訊，請移至 [上傳您的憑證](azure-stack-edge-j-series-manage-certificates.md#upload-certificates)。 如需可在裝置上安裝的所有憑證清單，請移至 [ [管理裝置上的憑證](azure-stack-edge-j-series-manage-certificates.md)]。

- 當您在裝置上設定計算時，會建立 IoT 裝置和 IoT Edge 裝置。 對稱存取金鑰會自動指派給這些裝置。 基於安全性最佳做法的考量，這些金鑰會透過 IoT 中樞服務定期輪替。

## <a name="protect-your-data"></a>保護您的資料

本節說明保護傳輸和儲存資料的安全性功能。

### <a name="protect-data-at-rest"></a>保護靜態資料的安全

裝置上的所有待用資料都會進行雙重加密、控制資料的存取權，一旦停用裝置之後，資料就會安全地從資料磁片上清除。

#### <a name="double-encryption-of-data"></a>資料的雙重加密

您磁片上的資料會受到兩層加密的保護：

- 第一個加密層是資料磁片區上的 BitLocker XTS-AES 256 位加密。
- 第二層是具有內建加密的硬碟。
- OS 磁片區具有 BitLocker 作為單一加密層。

> [!NOTE]
> OS 磁片具有單一層 BitLocker XTS-AES-256 軟體加密。

當裝置啟動時，系統會提示您儲存包含修復金鑰的金鑰檔，以協助復原裝置上的資料（如果裝置未開機）。 檔案中有兩個金鑰：

- 其中一個金鑰會復原作業系統磁片區上的裝置設定。
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- 第二個金鑰會將資料磁片中的硬體加密解除鎖定。

> [!IMPORTANT]
> 將金鑰檔儲存在裝置本身之外的安全位置。 如果裝置未開機，而且您沒有金鑰，可能會導致資料遺失。

- 某些復原案例會提示您輸入已儲存的金鑰檔。 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>限制存取資料

存取儲存在共用和儲存體帳戶中的資料會受到限制。

- 存取共用資料的 SMB 用戶端需要與共享相關聯的使用者認證。 建立共用時，會定義這些認證。
- 存取共用的 NFS 用戶端必須在建立共用時明確地新增其 IP 位址。
- 在裝置上建立的 Edge 儲存體帳戶為本機，並受到資料磁片上的加密所保護。 這些 Edge 儲存體帳戶對應的 Azure 儲存體帳戶會受到訂用帳戶和與 Edge 儲存體帳戶相關聯的 2 512 位儲存體存取金鑰所保護 (這些金鑰與) Azure 儲存體帳戶相關聯的金鑰不同。 如需詳細資訊，請參閱 [保護儲存體帳戶中的資料](#protect-data-in-storage-accounts)。
- BitLocker XTS-AES 256-位加密用來保護本機資料。

#### <a name="secure-data-erasure"></a>保護資料抹除

當裝置進行硬重設時，會在裝置上執行安全抹除。 安全抹除會在磁片上使用 NIST SP 800-88r1 清除來執行資料清除。

### <a name="protect-data-in-flight"></a>保護傳輸中的資料

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>保護儲存體帳戶中的資料

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- 定期輪替並 [同步處理您的儲存體帳戶金鑰](azure-stack-edge-j-series-manage-storage-accounts.md) ，以協助保護您的儲存體帳戶免于未經授權的使用者。

## <a name="manage-personal-information"></a>管理個人資訊

Azure Stack Edge 服務會在下列情況下收集個人資訊：

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

若要查看可存取或刪除共用的使用者清單，請依照 Azure Stack Edge 的 [ [管理共用](azure-stack-edge-j-series-manage-shares.md)] 中的步驟執行。

如需詳細資訊，請參閱 [信任中心](https://www.microsoft.com/trustcenter)上的 Microsoft 隱私權原則。

## <a name="next-steps"></a>後續步驟

[部署您的 Azure Stack Edge Pro R 裝置](azure-stack-edge-gpu-deploy-prep.md)
