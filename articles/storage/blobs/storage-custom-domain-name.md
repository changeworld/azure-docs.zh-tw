---
title: 將自訂網域對應至 Azure Blob 儲存體端點
titleSuffix: Azure Storage
description: 將自訂域映射到 Azure 存儲帳戶中的 Blob 存儲或 Web 終結點。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 9d05677ec47851557594ef47499da653accad141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370469"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>將自訂網域對應至 Azure Blob 儲存體端點

您可以將自訂域映射到 Blob 服務終結點或[靜態網站](storage-blob-static-website.md)終結點。 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> 此映射僅適用于子域（例如： `www.contoso.com`。 如果希望 Web 終結點在根域（例如： `contoso.com`） 上可用，則必須使用 Azure CDN。 有關指南，請參閱本文[中啟用 HTTPS 的自訂域](#enable-https)映射部分。 由於您要訪問本文的該部分以啟用自訂域的根域，因此該部分中用於啟用 HTTPS 的步驟是可選的。 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>映射僅啟用 HTTP 的自訂域

此方法更容易，但僅啟用 HTTP 訪問。 如果存儲帳戶配置為需要通過 HTTPS[安全傳輸](../common/storage-require-secure-transfer.md)，則必須為自訂域啟用 HTTPS 存取權限。 

要啟用 HTTPS 訪問，請參閱本文中[啟用 HTTPS 的自訂域](#enable-https)映射部分。 

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>映射自訂域

> [!IMPORTANT]
> 完成配置時，自訂域將暫時對使用者不可用。 如果您的域當前支援具有需要零停機時間的服務等級協定 （SLA） 的應用程式，請按照本文零[停機時間部分的"映射自訂域"](#zero-down-time)中的步驟操作，以確保使用者可以在 DNS 映射進行期間訪問您的域。

如果您不關心域對使用者短暫不可用，請按照以下步驟操作。

：heavy_check_mark：步驟 1：獲取存儲終結點的主機名稱。

：heavy_check_mark：步驟 2：與域供應商創建正式名稱 （CNAME） 記錄。

：heavy_check_mark：步驟 3：向 Azure 註冊自訂域。 

：heavy_check_mark：步驟 4：測試自訂域。

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>第 1 步：獲取存儲終結點的主機名稱 

主機名稱是存儲終結點 URL，沒有協定識別碼和尾隨斜杠。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

2. 在功能表窗格中，**在"設置"** 下，選擇 **"屬性**"。  

3. 將主 Blob**服務終結點**或**主靜態網站終結點**的值複製到文字檔。 

4. 從該字串中刪除協定識別碼（*例如*HTTPS）和尾隨斜杠。 下表包含示例。

   | 終結點的類型 |  端點 | 主機名稱 |
   |------------|-----------------|-------------------|
   |blob 服務  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |靜態網站  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   將此值放在一邊供以後使用。

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>第 2 步：與域供應商創建正式名稱 （CNAME） 記錄

創建 CNAME 記錄以指向您的主機名稱。 CNAME 記錄是一種將來源網域名稱對應至目的地網域名稱的 DNS 記錄。

1. 登錄到域註冊商的網站，然後轉到該頁面以管理 DNS 設置。

   您可能會在名為 [Domain Name] \(網域名稱\)****、[DNS]**** 或 [Name Server Management] \(名稱伺服器管理\)**** 的區段中找到該頁面。

2. 查找用於管理 CNAME 記錄的部分。 

   您可能需要前往進階設定頁面，然後尋找 [CNAME]****、[Alias] \(別名\)**** 或 [Subdomains] \(子網域\)****。

3. 創建 CNAME 記錄。 作為該記錄的一部分，提供以下專案： 

   - 子域別名，如`www`或`photos`。 子域是必需的，根域不受支援。 
      
   - 本文前面在[獲取存儲終結點部分的主機名稱](#endpoint)中獲取的主機名稱。 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>步驟 3：向 Azure 註冊自訂域

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

2. 在功能表窗格中的 [Blob 服務]**** 底下，選取 [自訂網域]****。  

   ![自訂域選項](./media/storage-custom-domain-name/custom-domain-button.png "自訂域")

   [自訂網域]**** 窗格隨即開啟。

3. 在 **"功能變數名稱**文本"框中，輸入自訂域的名稱，包括子域  
   
   例如，如果您的域*是contoso.com，* 而您的子域別名是*www*，`www.contoso.com`請輸入 。 如果您的子域是*照片*，請輸入`photos.contoso.com`。

4. 要註冊自訂域，請選擇"**保存**"按鈕。

   CNAME 記錄通過功能變數名稱伺服器 （DNS） 傳播後，如果使用者具有適當的許可權，則可以使用自訂域查看 blob 資料。

#### <a name="step-4-test-your-custom-domain"></a>第 4 步：測試自訂域

若要確認自訂網域是否對應至 Blob 服務端點，請在儲存體帳戶內的公用容器中建立 Blob。 接著，在網頁瀏覽器中，使用以下格式的 URI 來存取 Blob：`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

例如，若要存取 *photos.contoso.com* 自訂子網域之 *myforms*容器中的 Web 表單，您可以使用以下 URI：`http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>映射停機時間為零的自訂域

> [!NOTE]
> 如果您不關心該域對使用者短暫不可用，請考慮按照本文的[自訂域部分的"映射](#map-a-domain)"中的步驟操作。 這是一種更簡單的方法，步驟更少。  

如果您的域當前支援具有服務等級協定 （SLA） 的應用程式，該協定需要零停機時間，請按照以下步驟操作，以確保使用者可以在 DNS 映射進行期間訪問您的域。 

：heavy_check_mark：步驟 1：獲取存儲終結點的主機名稱。

：heavy_check_mark：步驟 2：與域供應商創建中間正式名稱 （CNAME） 記錄。

：heavy_check_mark：步驟 3：向 Azure 預註冊自訂域。

：heavy_check_mark：步驟 4：使用域供應商創建 CNAME 記錄。

：heavy_check_mark：步驟 5：測試自訂域。

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>第 1 步：獲取存儲終結點的主機名稱 

主機名稱是存儲終結點 URL，沒有協定識別碼和尾隨斜杠。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

2. 在功能表窗格中，**在"設置"** 下，選擇 **"屬性**"。  

3. 將主 Blob**服務終結點**或**主靜態網站終結點**的值複製到文字檔。 

4. 從該字串中刪除協定識別碼（*例如*HTTPS）和尾隨斜杠。 下表包含示例。

   | 終結點的類型 |  端點 | 主機名稱 |
   |------------|-----------------|-------------------|
   |blob 服務  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |靜態網站  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   將此值放在一邊供以後使用。

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>第 2 步：與域供應商創建中間正式名稱 （CNAME） 記錄

創建臨時 CNAME 記錄以指向您的主機名稱。 CNAME 記錄是一種將來源網域名稱對應至目的地網域名稱的 DNS 記錄。

1. 登錄到域註冊商的網站，然後轉到該頁面以管理 DNS 設置。

   您可能會在名為 [Domain Name] \(網域名稱\)****、[DNS]**** 或 [Name Server Management] \(名稱伺服器管理\)**** 的區段中找到該頁面。

2. 查找用於管理 CNAME 記錄的部分。 

   您可能需要前往進階設定頁面，然後尋找 [CNAME]****、[Alias] \(別名\)**** 或 [Subdomains] \(子網域\)****。

3. 創建 CNAME 記錄。 作為該記錄的一部分，提供以下專案： 

   - 子域別名，如`www`或`photos`。 子域是必需的，根域不受支援。

     將`asverify`子域添加到別名。 例如：`asverify.www` 或 `asverify.photos`。
       
   - 本文前面在[獲取存儲終結點部分的主機名稱](#endpoint)中獲取的主機名稱。 

     將子域`asverify`添加到主機名稱。 例如：`asverify.mystorageaccount.blob.core.windows.net`。

4. 要註冊自訂域，請選擇"**保存**"按鈕。

   如果註冊成功，入口網站就會通知您已順利更新您的儲存體帳戶。 自訂網域已通過 Azure 的驗證，但傳送至網域的流量尚未路由傳送到儲存體帳戶。

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>步驟 3：在 Azure 中預註冊自訂域

在 Azure 中預註冊自訂域時，允許 Azure 識別自訂域，而無需修改域的 DNS 記錄。 這樣，當您修改域的 DNS 記錄時，它將映射到 Blob 終結點，沒有停機時間。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

2. 在功能表窗格中的 [Blob 服務]**** 底下，選取 [自訂網域]****。  

   ![自訂域選項](./media/storage-custom-domain-name/custom-domain-button.png "自訂域")

   [自訂網域]**** 窗格隨即開啟。

3. 在 **"功能變數名稱**文本"框中，輸入自訂域的名稱，包括子域  
   
   例如，如果您的域*是contoso.com，* 而您的子域別名是*www*，`www.contoso.com`請輸入 。 如果您的子域是*照片*，請輸入`photos.contoso.com`。

4. 選取 [使用間接 CNAME 驗證]**** 核取方塊。

5. 要註冊自訂域，請選擇"**保存**"按鈕。
  
   CNAME 記錄通過功能變數名稱伺服器 （DNS） 傳播後，如果使用者具有適當的許可權，則可以使用自訂域查看 blob 資料。

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>第 4 步：使用域供應商創建 CNAME 記錄

創建臨時 CNAME 記錄以指向您的主機名稱。

1. 登錄到域註冊商的網站，然後轉到該頁面以管理 DNS 設置。

   您可能會在名為 [Domain Name] \(網域名稱\)****、[DNS]**** 或 [Name Server Management] \(名稱伺服器管理\)**** 的區段中找到該頁面。

2. 查找用於管理 CNAME 記錄的部分。 

   您可能需要前往進階設定頁面，然後尋找 [CNAME]****、[Alias] \(別名\)**** 或 [Subdomains] \(子網域\)****。

3. 創建 CNAME 記錄。 作為該記錄的一部分，提供以下專案： 

   - 子域別名，如`www`或`photos`。 子域是必需的，根域不受支援。
      
   - 本文前面在[獲取存儲終結點部分的主機名稱](#endpoint-2)中獲取的主機名稱。 

#### <a name="step-5-test-your-custom-domain"></a>第 5 步：測試自訂域

若要確認自訂網域是否對應至 Blob 服務端點，請在儲存體帳戶內的公用容器中建立 Blob。 接著，在網頁瀏覽器中，使用以下格式的 URI 來存取 Blob：`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

例如，若要存取 *photos.contoso.com* 自訂子網域之 *myforms*容器中的 Web 表單，您可以使用以下 URI：`http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>刪除自訂域映射

要刪除自訂域映射，請登出自訂域。 請利用下列其中一項程序。

#### <a name="portal"></a>[入口網站](#tab/azure-portal)

若要移除自訂網域設定，請執行下列操作：

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

2. 在功能表窗格中的 [Blob 服務]**** 底下，選取 [自訂網域]****。  
   [自訂網域]**** 窗格隨即開啟。

3. 清除包含自訂網域名稱的文字方塊內容。

4. 選取 [儲存]**** 按鈕。

成功刪除自訂域後，您將看到一個門戶通知，通知您的存儲帳戶已成功更新

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要移除自訂網域註冊，請使用 [az storage account update](https://docs.microsoft.com/cli/azure/storage/account) CLI 命令，然後指定空字串 (`""`) 作為 `--custom-domain` 引數值。

* 命令格式︰

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* 命令範例︰

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

#### <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要移除自訂網域註冊，請使用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell Cmdlet，然後指定空字串 (`""`) 作為 `-CustomDomainName` 引數值。

* 命令格式︰

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* 命令範例︰

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```
---

<a id="enable-https" />

## <a name="map-a-custom-domain-with-https-enabled"></a>映射啟用了 HTTPS 的自訂域

此方法涉及更多步驟，但它支援 HTTPS 訪問。 

如果不需要使用者使用 HTTPS 訪問 Blob 或 Web 內容，請參閱[僅使用本文啟用 HTTP 的部分映射自訂域](#enable-http)。 

要映射自訂域並啟用 HTTPS 訪問，請執行以下操作：

1. 在 blob 或 Web 終結點上啟用[Azure CDN。](../../cdn/cdn-overview.md) 

   有關 Blob 存儲終結點，請參閱[將 Azure 存儲帳戶與 Azure CDN 集成](../../cdn/cdn-create-a-storage-account-with-cdn.md)。 

   有關靜態網站終結點，請參閱[將靜態網站與 Azure CDN 集成](static-website-content-delivery-network.md)。

2. [將 Azure CDN 內容對應至自訂網域](../../cdn/cdn-map-content-to-custom-domain.md)。

3. [在 Azure CDN 自訂域上啟用 HTTPS。](../../cdn/cdn-custom-ssl.md)

   > [!NOTE] 
   > 更新靜態網站時，請確保通過清除 CDN 終結點來清除 CDN 邊緣伺服器上的緩存內容。 如需詳細資訊，請參閱[清除 Azure CDN 端點](../../cdn/cdn-purge-endpoint.md)。

4. （可選）查看以下指南：

   * [使用 Azure CDN 的共用訪問簽名 （SAS） 權杖](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures)。

   * [HTTP 到 HTTPS 使用 Azure CDN 重定向](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)。

   * [將 Blob 存儲與 Azure CDN 一起使用時的定價和計費](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)。

## <a name="next-steps"></a>後續步驟

* [瞭解 Azure Blob 存儲中的靜態網站託管](storage-blob-static-website.md)
