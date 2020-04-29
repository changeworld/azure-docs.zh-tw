---
title: 將自訂網域對應至 Azure Blob 儲存體端點
titleSuffix: Azure Storage
description: 將自訂網域對應至 Azure 儲存體帳戶中的 Blob 儲存體或 web 端點。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 9d05677ec47851557594ef47499da653accad141
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79370469"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>將自訂網域對應至 Azure Blob 儲存體端點

您可以將自訂網域對應至 blob 服務端點或[靜態網站](storage-blob-static-website.md)端點。 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> 這個對應僅適用于子域（例如： `www.contoso.com`）。 如果您想要在根域（例如： `contoso.com`）上使用 web 端點，則必須使用 Azure CDN。 如需指導方針，請參閱本文的[對應已啟用 HTTPS 的自訂網域](#enable-https)一節。 因為您前往本文的那一節來啟用自訂網域的根域，所以啟用 HTTPS 的那一節內的步驟是選擇性的。 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>對應僅啟用 HTTP 的自訂網域

這種方法比較簡單，但只會啟用 HTTP 存取。 如果儲存體帳戶設定為需要透過 HTTPS 進行[安全傳輸](../common/storage-require-secure-transfer.md)，則您必須為您的自訂網域啟用 HTTPS 存取。 

若要啟用 HTTPS 存取，請參閱本文的[對應已啟用 HTTPs 的自訂網域](#enable-https)一節。 

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>對應自訂網域

> [!IMPORTANT]
> 當您完成設定時，使用者會短暫無法使用您的自訂網域。 如果您的網域目前支援的應用程式具有不需要停機的服務等級協定（SLA），請遵循本文的[對應自訂網域與零停機](#zero-down-time)一節中的步驟，以確保使用者可以在 DNS 對應發生時存取您的網域。

如果您要不在乎您的使用者暫時無法使用該網域，請遵循下列步驟。

： heavy_check_mark：步驟1：取得儲存體端點的主機名稱。

： heavy_check_mark：步驟2：使用您的網域提供者建立正式名稱（CNAME）記錄。

： heavy_check_mark：步驟3：向 Azure 註冊自訂網域。 

： heavy_check_mark：步驟4：測試您的自訂網域。

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>步驟1：取得儲存體端點的主機名稱 

主機名稱是沒有通訊協定識別碼和尾端斜線的儲存體端點 URL。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

2. 在功能表窗格的 [**設定**] 底下，選取 [**屬性**]。  

3. 將**主要 Blob 服務端點**或**主要靜態網站端點**的值複製到文字檔。 

4. 從該字串移除通訊協定識別碼（*例如*，HTTPS）和尾端斜線。 下表包含範例。

   | 端點的類型 |  端點 | 主機名稱 |
   |------------|-----------------|-------------------|
   |blob 服務  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |靜態網站  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   請稍後再設定此值。

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>步驟2：使用您的網域提供者建立正式名稱（CNAME）記錄

建立 CNAME 記錄以指向您的主機名稱。 CNAME 記錄是一種將來源網域名稱對應至目的地網域名稱的 DNS 記錄。

1. 登入您的網域註冊機構網站，然後移至 [管理 DNS] 設定頁面。

   您可能會在名為 [Domain Name] \(網域名稱\)****、[DNS]**** 或 [Name Server Management] \(名稱伺服器管理\)**** 的區段中找到該頁面。

2. 尋找管理 CNAME 記錄的區段。 

   您可能需要前往進階設定頁面，然後尋找 [CNAME]****、[Alias] \(別名\)**** 或 [Subdomains] \(子網域\)****。

3. 建立 CNAME 記錄。 作為該記錄的一部分，請提供下列專案： 

   - 子域別名，例如`www`或。 `photos` 子域是必要的，不支援根域。 
      
   - 您在本文稍早的[取得儲存體端點的主機名稱](#endpoint)一節中取得的主機名稱。 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>步驟3：向 Azure 註冊您的自訂網域

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

2. 在功能表窗格中的 [Blob 服務]**** 底下，選取 [自訂網域]****。  

   ![自訂網域選項](./media/storage-custom-domain-name/custom-domain-button.png "自訂網域")

   [自訂網域]**** 窗格隨即開啟。

3. 在 [**功能變數名稱**] 文字方塊中，輸入自訂網域的名稱，包括子域  
   
   例如，如果您的網域是*contoso.com* ，而子域別名是*www*，請`www.contoso.com`輸入。 如果您的子域是*相片*， `photos.contoso.com`請輸入。

4. 若要註冊自訂網域，請選擇 [**儲存**] 按鈕。

   當 CNAME 記錄透過功能變數名稱伺服器（DNS）傳播後，如果您的使用者擁有適當的許可權，他們就可以使用自訂網域來查看 blob 資料。

#### <a name="step-4-test-your-custom-domain"></a>步驟4：測試您的自訂網域

若要確認自訂網域是否對應至 Blob 服務端點，請在儲存體帳戶內的公用容器中建立 Blob。 接著，在網頁瀏覽器中，使用以下格式的 URI 來存取 Blob：`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

例如，若要存取 *photos.contoso.com* 自訂子網域之 *myforms*容器中的 Web 表單，您可以使用以下 URI：`http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>對應自訂網域零停機時間

> [!NOTE]
> 如果您要不在乎您的使用者暫時無法使用該網域，請考慮遵循本文的[對應自訂網域](#map-a-domain)一節中的步驟。 這是較簡單的方法，其中包含更少的步驟。  

如果您的網域目前支援的應用程式具有不需要停機的服務等級協定（SLA），則請遵循下列步驟，以確保使用者可以在 DNS 對應發生時存取您的網域。 

： heavy_check_mark：步驟1：取得儲存體端點的主機名稱。

： heavy_check_mark：步驟2：使用您的網域提供者建立中繼正式名稱（CNAME）記錄。

： heavy_check_mark：步驟3：使用 Azure 預先註冊自訂網域。

： heavy_check_mark：步驟4：使用您的網域提供者建立 CNAME 記錄。

： heavy_check_mark：步驟5：測試您的自訂網域。

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>步驟1：取得儲存體端點的主機名稱 

主機名稱是沒有通訊協定識別碼和尾端斜線的儲存體端點 URL。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

2. 在功能表窗格的 [**設定**] 底下，選取 [**屬性**]。  

3. 將**主要 Blob 服務端點**或**主要靜態網站端點**的值複製到文字檔。 

4. 從該字串移除通訊協定識別碼（*例如*，HTTPS）和尾端斜線。 下表包含範例。

   | 端點的類型 |  端點 | 主機名稱 |
   |------------|-----------------|-------------------|
   |blob 服務  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |靜態網站  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   請稍後再設定此值。

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>步驟2：使用您的網域提供者建立中繼正式名稱（CNAME）記錄

建立暫存 CNAME 記錄以指向您的主機名稱。 CNAME 記錄是一種將來源網域名稱對應至目的地網域名稱的 DNS 記錄。

1. 登入您的網域註冊機構網站，然後移至 [管理 DNS] 設定頁面。

   您可能會在名為 [Domain Name] \(網域名稱\)****、[DNS]**** 或 [Name Server Management] \(名稱伺服器管理\)**** 的區段中找到該頁面。

2. 尋找管理 CNAME 記錄的區段。 

   您可能需要前往進階設定頁面，然後尋找 [CNAME]****、[Alias] \(別名\)**** 或 [Subdomains] \(子網域\)****。

3. 建立 CNAME 記錄。 作為該記錄的一部分，請提供下列專案： 

   - 子域別名，例如`www`或。 `photos` 子域是必要的，不支援根域。

     將`asverify`子域新增至別名。 例如：`asverify.www` 或 `asverify.photos`。
       
   - 您在本文稍早的[取得儲存體端點的主機名稱](#endpoint)一節中取得的主機名稱。 

     將子域`asverify`新增至主機名稱。 例如： `asverify.mystorageaccount.blob.core.windows.net` 。

4. 若要註冊自訂網域，請選擇 [**儲存**] 按鈕。

   如果註冊成功，入口網站就會通知您已順利更新您的儲存體帳戶。 自訂網域已通過 Azure 的驗證，但傳送至網域的流量尚未路由傳送到儲存體帳戶。

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>步驟3：預先向 Azure 註冊您的自訂網域

當您向 Azure 預先註冊自訂網域時，您會允許 Azure 辨識您的自訂網域，而不需要修改網域的 DNS 記錄。 如此一來，當您修改網域的 DNS 記錄時，它將會對應至 blob 端點，而不會有停機時間。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

2. 在功能表窗格中的 [Blob 服務]**** 底下，選取 [自訂網域]****。  

   ![自訂網域選項](./media/storage-custom-domain-name/custom-domain-button.png "自訂網域")

   [自訂網域]**** 窗格隨即開啟。

3. 在 [**功能變數名稱**] 文字方塊中，輸入自訂網域的名稱，包括子域  
   
   例如，如果您的網域是*contoso.com* ，而子域別名是*www*，請`www.contoso.com`輸入。 如果您的子域是*相片*， `photos.contoso.com`請輸入。

4. 選取 [使用間接 CNAME 驗證]**** 核取方塊。

5. 若要註冊自訂網域，請選擇 [**儲存**] 按鈕。
  
   當 CNAME 記錄透過功能變數名稱伺服器（DNS）傳播後，如果您的使用者擁有適當的許可權，他們就可以使用自訂網域來查看 blob 資料。

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>步驟4：使用您的網域提供者建立 CNAME 記錄

建立暫存 CNAME 記錄以指向您的主機名稱。

1. 登入您的網域註冊機構網站，然後移至 [管理 DNS] 設定頁面。

   您可能會在名為 [Domain Name] \(網域名稱\)****、[DNS]**** 或 [Name Server Management] \(名稱伺服器管理\)**** 的區段中找到該頁面。

2. 尋找管理 CNAME 記錄的區段。 

   您可能需要前往進階設定頁面，然後尋找 [CNAME]****、[Alias] \(別名\)**** 或 [Subdomains] \(子網域\)****。

3. 建立 CNAME 記錄。 作為該記錄的一部分，請提供下列專案： 

   - 子域別名，例如`www`或。 `photos` 子域是必要的，不支援根域。
      
   - 您在本文稍早的[取得儲存體端點的主機名稱](#endpoint-2)一節中取得的主機名稱。 

#### <a name="step-5-test-your-custom-domain"></a>步驟5：測試您的自訂網域

若要確認自訂網域是否對應至 Blob 服務端點，請在儲存體帳戶內的公用容器中建立 Blob。 接著，在網頁瀏覽器中，使用以下格式的 URI 來存取 Blob：`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

例如，若要存取 *photos.contoso.com* 自訂子網域之 *myforms*容器中的 Web 表單，您可以使用以下 URI：`http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>移除自訂網域對應

若要移除自訂網域對應，請取消註冊自訂網域。 請利用下列其中一項程序。

#### <a name="portal"></a>[入口網站](#tab/azure-portal)

若要移除自訂網域設定，請執行下列操作：

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

2. 在功能表窗格中的 [Blob 服務]**** 底下，選取 [自訂網域]****。  
   [自訂網域]**** 窗格隨即開啟。

3. 清除包含自訂網域名稱的文字方塊內容。

4. 選取 [儲存]**** 按鈕。

成功移除自訂網域之後，您會看到已成功更新儲存體帳戶的入口網站通知

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

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

## <a name="map-a-custom-domain-with-https-enabled"></a>對應已啟用 HTTPS 的自訂網域

此方法牽涉到更多步驟，但它會啟用 HTTPS 存取。 

如果您不需要使用者使用 HTTPS 來存取您的 blob 或 web 內容，請參閱本文的[對應具有僅啟用 HTTP 的自訂網域](#enable-http)一節。 

若要對應自訂網域並啟用 HTTPS 存取，請執行下列動作：

1. 在您的 blob 或 web 端點上啟用[AZURE CDN](../../cdn/cdn-overview.md) 。 

   如需 Blob 儲存體端點，請參閱[整合 azure 儲存體帳戶與 AZURE CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md)。 

   如需靜態網站端點，請參閱[整合靜態網站與 AZURE CDN](static-website-content-delivery-network.md)。

2. [將 Azure CDN 內容對應至自訂網域](../../cdn/cdn-map-content-to-custom-domain.md)。

3. [在 AZURE CDN 自訂網域上啟用 HTTPS](../../cdn/cdn-custom-ssl.md)。

   > [!NOTE] 
   > 當您更新靜態網站時，請務必清除 CDN 端點，以清除 CDN edge server 上的快取內容。 如需詳細資訊，請參閱[清除 Azure CDN 端點](../../cdn/cdn-purge-endpoint.md)。

4. 選擇性請參閱下列指引：

   * [使用 AZURE CDN 的共用存取簽章（SAS）權杖](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures)。

   * [使用 AZURE CDN 的 HTTP 對 HTTPS](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)重新導向。

   * 搭配[使用 Blob 儲存體與 AZURE CDN 時的定價和計費](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)。

## <a name="next-steps"></a>後續步驟

* [瞭解 Azure Blob 儲存體中的靜態網站裝載](storage-blob-static-website.md)
