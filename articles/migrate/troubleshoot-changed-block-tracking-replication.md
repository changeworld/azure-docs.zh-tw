---
title: 針對無代理程式 VMware VM 遷移中的複寫問題進行疑難排解
description: 取得複寫週期失敗的協助
author: anvar-ms
ms.manager: bsiva
ms.author: anvar
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: da1f7ce1474513fd9de286495f59aca63d8628b6
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377201"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>針對無代理程式 VMware VM 遷移中的複寫問題進行疑難排解

本文說明當您使用 Azure Migrate：伺服器遷移無代理程式方法複寫內部部署 VMware Vm 時，可能會遇到的一些常見問題和特定錯誤。

當您使用無代理程式複寫方法複寫 VMware 虛擬機器時，虛擬機器&#39;s (vmdk) 的資料會複寫到您 Azure 訂用帳戶中的複本受控磁片。 當 VM 的複寫開始時，會在複寫磁片的完整複本時，進行初始複寫週期。 初始複寫完成後，累加式複寫週期會定期排程，以傳送先前複寫週期以來發生的任何變更。

您可能偶爾會看到 VM 的複寫週期失敗。 這些失敗的原因可能是因為內部部署網路設定中的問題，到 Azure Migrate 的雲端服務後端發生問題。 在本文中，我們將：

 - 告訴您如何監視複寫狀態和解決錯誤。
 - 列出一些經常發生的複寫錯誤，並建議其他修復這些錯誤的步驟。

## <a name="monitor-replication-status-using-the-azure-portal"></a>使用 Azure 入口網站監視複寫狀態

使用下列步驟來監視虛擬機器的複寫狀態：

  1. 移至 Azure 入口網站 Azure Migrate 中的 [伺服器] 頁面。
  ![影像1](./media/troubleshoot-changed-block-tracking-replication/image0.png)
  1. 在 [伺服器遷移] 磚中，按一下 [複寫伺服器]，以流覽至 [複寫機器] 頁面。
  ![影像2](./media/troubleshoot-changed-block-tracking-replication/image1.png)
  1. 您將會看到一份複寫伺服器的清單，以及其他資訊，例如狀態、健全狀況、上次同步時間等等。健全狀況資料行會指出 VM 目前的複寫健康情況。 健全狀況資料行中的「重大」或「警告」值通常表示 VM 先前的複寫週期失敗。 若要取得更多詳細資料，請以滑鼠右鍵按一下 VM，然後選取 [錯誤詳細資料]。 [錯誤詳細資料] 頁面包含有關錯誤的資訊，以及如何進行疑難排解的其他詳細資料。 您也會看到 [最近使用的事件] 連結，可用來流覽至 VM 的 [事件] 頁面。
  ![影像3](./media/troubleshoot-changed-block-tracking-replication/image2.png)
  1. 按一下 [最近使用的事件] 以查看 VM 先前的複寫週期失敗。 在 [事件] 頁面中，尋找 VM 的「複寫週期失敗」或「磁片的複寫週期失敗」類型的最新事件。
  ![影像4](./media/troubleshoot-changed-block-tracking-replication/image3.png)
  1. 按一下事件可瞭解錯誤的可能原因，以及建議的補救步驟。 使用提供的資訊來疑難排解和補救錯誤。
 ![影像5](./media/troubleshoot-changed-block-tracking-replication/image4.png)

## <a name="common-replication-errors"></a>一般複寫錯誤

本節說明一些常見的錯誤，以及您可以如何進行疑難排解。

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>嘗試複寫 Vm 時 Key Vault 操作失敗錯誤

**錯誤：** 「Key Vault 操作失敗。 作業：設定受管理的儲存體帳戶，Key Vault：金鑰保存庫-名稱，儲存體帳戶：儲存體帳戶名稱失敗，錯誤： "

**錯誤：** 「Key Vault 操作失敗。 作業：產生共用存取簽章定義，Key Vault：金鑰保存庫名稱，儲存體帳戶：儲存體帳戶名稱失敗，錯誤： "

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

發生此錯誤的原因通常是因為 Key Vault 的使用者存取原則未授與目前登入的使用者設定要 Key Vault 管理的儲存體帳戶所需的許可權。 若要在金鑰保存庫上檢查使用者存取原則，請移至金鑰保存庫的入口網站上的 [金鑰保存庫] 頁面，然後選取 [存取原則] 

當入口網站建立金鑰保存庫時，它也會新增使用者存取原則，授與目前登入的使用者權限來設定要 Key Vault 管理的儲存體帳戶。 這可能會因為兩個原因而失敗

- 登入的使用者是客戶 Azure 租使用者 (CSP 訂用帳戶的遠端主體，而且登入的使用者是夥伴管理員) 。 在此情況下，因應措施是刪除金鑰保存庫、登出入口網站，然後使用客戶租使用者中的使用者帳戶登入 (而不是遠端主體) 然後重試作業。 CSP 合作夥伴通常會在客戶 Azure Active Directory 租使用者中擁有可使用的使用者帳戶。 如果不是，他們可以在客戶 Azure Active Directory 租使用者中自行建立新的使用者帳戶，以新使用者的身分登入入口網站，然後重試複寫作業。 使用的帳戶必須將擁有者或參與者 + 使用者存取系統管理員許可權授與資源群組上的帳戶， (遷移專案資源群組) 

- 另一種情況可能會發生這種情況，就是當一位使用者 (user1) 嘗試在一開始設定複寫併發生失敗時，但已建立金鑰保存庫 (以及適當地指派給此使用者) 的使用者存取原則。 現在，我們會在稍後將不同的使用者 (使用者 2) 嘗試設定複寫，但設定受管理的儲存體帳戶或產生 SAS 定義作業失敗，因為沒有對應至金鑰保存庫中使用者2的使用者存取原則。

**解決** 方法：若要解決此問題，請在 keyvault 授與使用者管理人員許可權，以設定受管理的儲存體帳戶並產生 SAS 定義的使用者存取原則。 您可以使用下列 Cmdlet，在 Azure PowerShell 中執行此動作：

$userPrincipalId = $ (>get-azurermaduser-UserPrincipalName "user2_email_address" ) 。Id

Set-AzureRmKeyVaultAccessPolicy-VaultName "keyvaultname"-ObjectId $userPrincipalId-PermissionsToStorage get、list、delete、set、update、regeneratekey、getsas、listsas、deletesas、setsas、recover、backup、restore、清除


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**錯誤 ID：** 181008

**錯誤訊息：** VM： VMName。 錯誤：遇到狀態 & ' [' SnapshotReplication. SnapshotReplicationEngine + WaitingForArtefactsDisposalPreCycle ' ( ' WaitingForArtefactsDisposalPreCycle ' ) ] ' 的超時事件 ' DisposeArtefactsTimeout '。

**可能的原因：**

嘗試將資料複寫至 Azure 的元件可能已關閉或沒有回應。 可能的原因包括：

- Azure Migrate 設備中執行的閘道服務已關閉。
- 閘道服務遇到服務匯流排/事件中樞/設備儲存體帳戶的連線問題。

**識別 DisposeArtefactsTimedOut 的確切原因和對應的解決方式：**

1. 請確定 Azure Migrate 設備已啟動並執行。
2. 檢查閘道服務是否正在應用裝置上執行：
   1.  使用遠端桌面登入 Azure Migrate 設備，並執行下列動作。

   2.  開啟 Microsoft 服務 MMC 嵌入式管理單元 (執行 > services.msc) ，然後檢查「Microsoft Azure 閘道服務」是否正在執行。 如果服務已停止或未執行，請啟動服務。 或者，您可以開啟命令提示字元或 PowerShell，並執行： "Net Start asrgwy"

3. 檢查 Azure Migrate 設備和設備儲存體帳戶之間的連線能力問題： 

    在 Azure Migrate 設備中下載 azcopy 之後，請執行下列命令：
    
    _azcopy 工作臺 HTTPs：//[帳戶]. 核心/[container]？Sas_
    
    **執行效能基準測試的步驟：**
    
      1. [下載](../storage/common/storage-use-azcopy-v10.md) azcopy
        
      2. 在資源群組中尋找設備儲存體帳戶。 儲存體帳戶的名稱類似 migrategwsa \* \* \* \* \* \* \* \* \* \* 。 這是上述命令中參數 [account] 的值。
        
      3. 在 Azure 入口網站中搜尋您的儲存體帳戶。 確定您用來搜尋的訂用帳戶，與建立儲存體帳戶 (目標訂用帳戶) 的訂用帳戶相同。 移至 [Blob 服務] 區段中的 [容器]。 按一下 [+ 容器] 並建立容器。 將 [公用存取層級] 保留為預設選取的值。
        
      4. 移至 [設定] 下的 [共用存取簽章]。 選取 [允許的資源類型] 中的容器。 按一下 [產生 SAS 與連接字串]。 複製 SAS 值。
        
      5. 使用步驟2、3和4分別取得的值來取代帳戶、容器、SAS，以在命令提示字元中執行上述命令。
        
      或者， [下載](https://go.microsoft.com/fwlink/?linkid=2138967) Azure 儲存體探索設備，並嘗試將10個 blob 的 10 64 個 blob 上傳至儲存體帳戶。 如果沒有任何問題，則上傳應該會成功。
        
    **解決方式：** 如果此測試失敗，就會發生網路問題&#39;。 與您的當地網路團隊合作，檢查連線問題。 一般而言，可能會有一些會導致失敗的防火牆設定。
    
4.  檢查 Azure Migrate 設備與服務匯流排之間的連線能力問題：

    此測試會檢查 Azure Migrate 設備是否可與 Azure Migrate 雲端服務後端進行通訊。 設備會透過服務匯流排和事件中樞訊息佇列來與服務後端通訊。 若要驗證從設備到服務匯流排的連線，請 [下載](https://go.microsoft.com/fwlink/?linkid=2139104) Service Bus Explorer，並嘗試連線到設備服務匯流排並執行傳送訊息/接收訊息。 如果沒有問題，這應該會成功。

    **執行測試的步驟：**

    1. 從已在遷移專案中建立的服務匯流排複製連接字串
    2. 開啟 Service Bus Explorer
    3. 移至 [檔案]，然後連接
    4. 貼上連接字串，然後按一下 [連線]。
    5. 這會開啟服務匯流排命名空間
    6. 選取主題中的 Snapshot Manager。 以滑鼠右鍵按一下 Snapshot Manager，選取 [接收訊息] > 選取 [查看]，然後按一下 [確定]
    7. 如果連接成功，您會在主控台輸出上看到 [已接收的訊息]。 如果連線不成功，您會看到一則訊息，指出連接失敗
    
    **解決方式：** 如果此測試失敗，就會發生網路問題。 與您的當地網路團隊合作，檢查連線問題。 一般而言，可能會有一些會導致失敗的防火牆設定。

5. Azure Migrate 設備和 Azure Key Vault 之間的連線能力問題：

    此測試會檢查 Azure Migrate 設備和 Azure Key Vault 之間的連線能力問題。 Key Vault 用來管理用於複寫的儲存體帳戶存取權。
    
    **檢查連線能力的步驟：**
    
    1. 從對應至 Azure Migrate 專案的資源群組中的資源清單提取 Key Vault URI。
    
    1. 在 Azure Migrate 設備中開啟 PowerShell，然後執行下列命令：
    
    _測試-test-netconnection Key Vault URI-P 443_
    
    此命令將會嘗試 TCP 連線，並傳回輸出。
    
     - 在輸出中，檢查 " _TcpTestSucceeded_ " 欄位。 如果值為 " _True_ "，Azure Migrate 設備和 Azure Key Vault 之間沒有連線問題。 如果值為 "False"，就會發生連線問題。
    
    **解決方式：** 如果此測試失敗，Azure Migrate 設備和 Azure Key Vault 之間會發生連線問題。 與您的當地網路團隊合作，檢查連線問題。 一般而言，可能會有一些會導致失敗的防火牆設定。
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**錯誤 ID：** 1011

**錯誤訊息：** 上傳磁片 DiskPath 的資料，虛擬機器 VMName 的 DiskId;VMId 未在預期的時間內完成。

此錯誤通常表示執行複寫的 Azure Migrate 設備無法連線到 Azure 雲端服務，或複寫正在進行緩慢而導致複寫週期超時。

可能的原因包括：

- Azure Migrate 設備已關閉。
- 設備上的複寫閘道服務未執行。
- 複寫閘道服務在下列其中一個用於複寫的 Azure 服務元件中遇到連線問題：服務匯流排/事件中樞/Azure 快取儲存體帳戶/Azure Key Vault。
- 閘道服務在嘗試讀取磁片時，會在 vCenter 層級進行節流處理。

**找出根本原因並解決問題：**

1. 請確定 Azure Migrate 設備已啟動並執行。
2. 檢查閘道服務是否正在應用裝置上執行：
   1.  使用遠端桌面登入 Azure Migrate 設備，並執行下列動作。

   2.  開啟 Microsoft 服務 MMC 嵌入式管理單元 (執行 > services.msc) ，然後檢查「Microsoft Azure 閘道服務」是否正在執行。 如果服務已停止或未執行，請啟動服務。 或者，您可以開啟命令提示字元或 PowerShell，並執行： "Net Start asrgwy"。


3. **檢查 Azure Migrate 設備和快取儲存體帳戶之間的連線能力問題：** 

    在 Azure Migrate 設備中下載 azcopy 之後，請執行下列命令：
    
    _azcopy 工作臺 HTTPs：//[帳戶]. 核心/[container]？Sas_
    
    **執行效能基準測試的步驟：**
    
      1. [下載](../storage/common/storage-use-azcopy-v10.md) azcopy
        
      2. 在資源群組中尋找設備儲存體帳戶。 儲存體帳戶的名稱類似 migratelsa \* \* \* \* \* \* \* \* \* \* 。 這是上述命令中參數 [account] 的值。
        
      3. 在 Azure 入口網站中搜尋您的儲存體帳戶。 確定您用來搜尋的訂用帳戶，與建立儲存體帳戶 (目標訂用帳戶) 的訂用帳戶相同。 移至 [Blob 服務] 區段中的 [容器]。 按一下 [+ 容器] 並建立容器。 將 [公用存取層級] 保留為預設選取的值。
        
      4. 移至 [設定] 下的 [共用存取簽章]。 選取 [允許的資源類型] 中的容器。 按一下 [產生 SAS 與連接字串]。 複製 SAS 值。
        
      5. 使用步驟2、3和4分別取得的值來取代帳戶、容器、SAS，以在命令提示字元中執行上述命令。
        
      或者， [下載](https://go.microsoft.com/fwlink/?linkid=2138967) Azure 儲存體探索設備，並嘗試將10個 blob 的 10 64 個 blob 上傳至儲存體帳戶。 如果沒有任何問題，則上傳應該會成功。
        
    **解決方式：** 如果此測試失敗，就會發生網路問題&#39;。 與您的當地網路團隊合作，檢查連線問題。 一般而言，可能會有一些會導致失敗的防火牆設定。
                
4.  **Azure Migrate 設備和 Azure 服務匯流排之間的連線能力問題：**

    此測試會檢查 Azure Migrate 設備是否可與 Azure Migrate 雲端服務後端進行通訊。 設備會透過服務匯流排和事件中樞訊息佇列來與服務後端通訊。 若要驗證從設備到服務匯流排的連線，請 [下載](https://go.microsoft.com/fwlink/?linkid=2139104) Service Bus Explorer，並嘗試連線到設備服務匯流排並執行傳送訊息/接收訊息。 如果沒有問題，這應該會成功。

    **執行測試的步驟：**
    
    1. 從對應于 Azure Migrate 專案的資源群組中建立的服務匯流排複製連接字串
    
    1. 開啟 Service Bus Explorer
    
    1. 移至檔案 > 連接
    
    1. 貼上您在步驟1中複製的連接字串，然後按一下 [連線]。
    
    1. 這會開啟服務匯流排命名空間。
    
    1. 選取 [命名空間] 主題中的 Snapshot Manager。 以滑鼠右鍵按一下 Snapshot Manager，選取 [接收訊息] > 選取 [查看]，然後按一下 [確定]。
    
    如果連接成功，您會在主控台輸出上看到 [已接收的訊息]。 如果連線不成功，您會看到一則訊息，指出連接失敗。
    
    **解決方式：** 如果此測試失敗，Azure Migrate 設備與服務匯流排之間會發生連線問題。 與您的當地網路團隊合作，檢查這些連線問題。 一般而言，可能會有一些會導致失敗的防火牆設定。
    
 5. **Azure Migrate 設備和 Azure Key Vault 之間的連線能力問題：**

    此測試會檢查 Azure Migrate 設備和 Azure Key Vault 之間的連線能力問題。 Key Vault 用來管理用於複寫的儲存體帳戶存取權。
    
    **檢查連線能力的步驟：**
    
    1. 從對應至 Azure Migrate 專案的資源群組中的資源清單提取 Key Vault URI。
    
    1. 在 Azure Migrate 設備中開啟 PowerShell，然後執行下列命令：
    
    _測試-test-netconnection Key Vault URI-P 443_
    
    此命令將會嘗試 TCP 連線，並傳回輸出。
    
    1. 在輸出中，檢查 " _TcpTestSucceeded_ " 欄位。 如果值為 " _True_ "，Azure Migrate 設備和 Azure Key Vault 之間沒有連線問題。 如果值為 "False"，就會發生連線問題。
    
    **解決方式：** 如果此測試失敗，Azure Migrate 設備和 Azure Key Vault 之間會發生連線問題。 與您的當地網路團隊合作，檢查連線問題。 一般而言，可能會有一些會導致失敗的防火牆設定。
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>嘗試提取變更的區塊時發生錯誤

錯誤訊息：「嘗試提取變更區塊時發生錯誤」

無代理程式複寫方法會使用 VMware 的變更區塊追蹤技術 (CBT) 將資料複寫至 Azure。 CBT 可讓伺服器遷移工具只追蹤和複寫自從上一個複寫週期以來已變更的區塊。 如果複寫虛擬機器的變更區塊追蹤已重設，或變更區塊追蹤檔案已損毀，就會發生此錯誤。

您可以透過下列兩種方式解決這個錯誤：

- 如果您已在觸發 VM 的複寫時選取 [是]，選擇 [自動修復複寫]，則工具會嘗試為您修復。 以滑鼠右鍵按一下 VM，然後選取 [修復複寫]。
- 如果您未選擇 [自動修復複寫]，或上述步驟未適用于您，請停止虛擬機器的複寫、重設虛擬機器上的 [變更區塊追蹤](https://go.microsoft.com/fwlink/?linkid=2139203) ，然後重新設定複寫。

VMware KB 2048201 中說明了可能導致 VMware vSphere 5.5 上的虛擬機器的 CBT 重設的一個已知問題，請參閱 [VMware KB：](https://go.microsoft.com/fwlink/?linkid=2138888) vSphere 5.x 中的儲存體 vMotion 作業之後重設變更的區塊追蹤。 如果您使用 VMware vSphere 5.5，請確實套用此 KB 中說明的更新。

或者，您也可以使用 VMware PowerCLI，在虛擬機器上 [重設 VMware 變更區塊追蹤]。

## <a name="an-internal-error-occurred"></a>發生內部錯誤

有時，您可能會遇到由於 VMware 環境/API 問題而發生的錯誤。 我們已在 VMware 環境相關的錯誤中識別出下列錯誤集。 這些錯誤具有固定格式。

_錯誤訊息：發生內部錯誤。[錯誤訊息]_

例如：錯誤訊息：發生內部錯誤。 [偵測到不正確快照集設定]。

下一節列出一些常見的 VMware 錯誤，以及您可以如何緩和這些錯誤。

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>錯誤訊息：發生內部錯誤。 [伺服器拒絕連接]

問題是已知的 VMware 問題，發生在 VDDK 6.7 中。 您必須停止在 Azure Migrate 設備中執行的閘道服務、 [從 VMWARE KB 下載更新](https://go.microsoft.com/fwlink/?linkid=2138889)，然後重新開機閘道服務。

停止閘道服務的步驟：

1. 按 Windows + R，開啟 services.msc。 按一下 [Microsoft Azure 閘道服務]，並將它停止。
2. 或者，您可以開啟命令提示字元或 PowerShell，並執行： Net Stop asrgwy。 請務必等到您收到訊息，指出服務已不再執行。

啟動閘道服務的步驟：

1. 按 Windows + R，開啟 services.msc。 以滑鼠右鍵按一下 [Microsoft Azure Gateway Service]，然後啟動它。
2. 或者，您可以開啟命令提示字元或 PowerShell，然後執行： Net Start asrgwy。

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>錯誤訊息：發生內部錯誤。 [偵測到不正確快照集設定。 ']

如果您有具有多個磁片的虛擬機器，如果您從虛擬機器移除磁片，可能會遇到此錯誤。 若要修復此問題，請參閱 [這篇 VMware 文章](https://go.microsoft.com/fwlink/?linkid=2138890)中的步驟。

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>錯誤訊息：發生內部錯誤。 [產生快照集無反應]

當快照集產生停止回應時，就會發生此問題。 發生此問題時，您可以看到 [建立快照集工作停止于95% 或 99%]。 請參閱此 [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138969) 以解決此問題。

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>錯誤訊息：發生內部錯誤。 [無法合併 VM 上的磁片 _[原因]_ ]

當我們在複寫週期結束時合併磁片時，操作會失敗。 請選取適當的 _原因_ 來解決問題，以遵循 [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138970)中的指示。

當 VMware 快照集相關的作業-建立、刪除或合併磁片失敗時，會發生下列錯誤。 遵循下一節中的指導方針來補救錯誤：

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>錯誤訊息：發生內部錯誤。 [另一個工作已經在進行中]

當有衝突的虛擬機器工作在背景中執行，或 vCenter Server 時間內的工作時，就會發生此問題。遵循下列 [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138891)提供的解決方案。

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>錯誤訊息：發生內部錯誤。 [在目前狀態中不允許操作]

當 vCenter Server 管理代理程式停止運作時，就會發生此問題。 若要解決此問題，請參閱下列 [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138971)的解決方案。

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>錯誤訊息：發生內部錯誤。 [快照集磁片大小無效]

這是已知的 VMware 問題，由 snapshot 指出的磁片大小會變成零。 遵循 [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138972)中提供的解決方案。

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>錯誤訊息：發生內部錯誤。 [記憶體配置失敗。 記憶體不足。]

當 NFC 主機緩衝區記憶體不足時，就會發生這種情況。 若要解決此問題，您必須將 VM (compute vMotion) ，移至具有可用資源的不同主機。

## <a name="next-steps"></a>後續步驟

繼續進行 VM 複寫，然後執行 [測試遷移](./tutorial-migrate-vmware.md#run-a-test-migration)。