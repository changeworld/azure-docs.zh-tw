---
title: 教學課程：設定 IoT Edge 裝置 - Azure IoT Edge 上的 Machine Learning
description: 在本教學課程中，您會將執行 Linux 的 Azure 虛擬機器設定為 Azure IoT Edge 裝置，以作為透明閘道。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: d7ff9efcedc1a6a5f92555a62e429be0431f2098
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448509"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>教學課程：設定 IoT Edge 裝置

> [!NOTE]
> 此文章是關於在 IoT Edge 上使用 Azure Machine Learning 的系列文章之一。 如果您是被直接引導至此文章，我們建議您先從本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)開始，以取得最佳成效。

在此文章中，我們將執行 Linux 的 Azure 虛擬機器設定為作為透明閘道的 IoT Edge 裝置。 透明閘道設定可讓裝置在不知道閘道存在的情況下，透過閘道連線到 Azure IoT 中樞。 同時，與 Azure IoT 中樞中的裝置互動的使用者，不會察覺中繼閘道裝置。 最後，我們會藉由向透明閘道新增 IoT Edge 模組來為系統新增邊緣分析。

此文章中的步驟通常是由雲端開發人員執行的。

## <a name="create-certificates"></a>建立憑證

針對要當作閘道運作的裝置，它必須能夠安全地連線到下游裝置。 Azure IoT Edge 可讓您使用公開金鑰基礎結構 (PKI)，設定這些裝置之間的安全連線。 在此案例中，我們將允許下游 IoT 裝置連線至作為透明閘道的 IoT Edge 裝置。 為了維持合理的安全性，下游裝置應該確認 IoT Edge 裝置的身分識別。 如需 IoT Edge 裝置如何使用憑證的詳細資訊，請參閱 [Azure IoT Edge 憑證使用方式詳細資料](iot-edge-certs.md)。

在此節中，我們會使用 Docker 映像建立自我簽署憑證，然後建置並執行它們。 我們選擇使用 Docker 映像來完成此步驟，因為其能大幅降低在 Windows 開發電腦上建立憑證所需的步驟數目。 請參閱[建立示範憑證以測試 IoT Edge 裝置功能](how-to-create-test-certificates.md)，以了解我們使用 Docker 映像自動化的項目。

1. 登入開發 VM。

2. 使用路徑和名稱 `c:\edgeCertificates` 建立新的資料夾。

3. 如果尚未執行，請從 Windows 的 [開始] 功能表開始**適用於 Windows 的 Docker**。

4. 開啟 Visual Studio Code。

5. 選取 [檔案]   > [開啟資料夾]  ，然後選擇 [C:\\來源\\IoTEdgeAndMlSample\\CreateCertificates]  。

6. 在 [總管] 窗格中，以滑鼠右鍵按一下 **dockerfile**，然後選擇 [建置映像]  。

7. 在對話方塊中，接受映像名稱和標籤的預設值：**createcertificates: latest**。

    ![在 Visual Studio Code 中建立憑證](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. 等待建置完成。

    > [!NOTE]
    > 您可能會看到缺少的公開金鑰的相關警告。 您可以放心地忽略這些警告。 同樣地，您將看到一個建議您檢查/重設您的映像的安全性警告，針對此映像您可以放心地忽略此警告。

9. 在 Visual Studio Code 終端機視窗中，執行 createcertificates 容器。

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker 將提示存取 **c:\\** 磁碟機。 選取 [共用]  。

11. 出現提示時，請提供您的認證。

12. 容器完成執行時，請檢查 **c:\\edgeCertificates** 中的下列檔案：

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>將憑證上傳至 Azure Key Vault

為安全地儲存我們的憑證，並使其可從多個裝置存取，我們會將憑證上傳至 Azure Key Vault。 從上述清單中可以看出，我們有兩種類型的憑證檔案：PFX 和 PEM。 我們會將 PFX 視為要上傳至 Key Vault 的 Key Vault 憑證。 PEM 檔案是純文字，我們會將其視為 Key Vault 祕密。 我們將使用與我們透過執行 [Azure Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks) 建立之 Azure Machine Learning 工作區相關聯的 Key Vault。

1. 從 [Azure 入口網站](https://portal.azure.com)，瀏覽至您的 Azure Machine Learning 工作區。

2. 從 Azure Machine Learning 工作區 [概觀] 頁面，尋找 **Key Vault** 的名稱。

    ![複製金鑰保存庫名稱](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. 在您的開發電腦上，將憑證上傳至 Key Vault。 以您的資源資訊取代 **\<subscriptionId\>** 和 **\<keyvaultname\>** 。

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. 出現提示時，請登入 Azure。

5. 該指令碼將執行幾分鐘，輸出會列出新的 Key Vault 項目。

    ![Key Vault 指令碼輸出](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>建立 IoT Edge 裝置

若要將 Azure IoT Edge 裝置連線到 IoT 中樞，首先我們要在中樞中建立裝置的身分識別。 我們會從雲端中的裝置身分識別取得連接字串，並使用它來設定 IoT Edge 裝置上的執行階段。 一旦已設定好的裝置連線到中樞，我們就可以部署模組並傳送訊息。 我們還可以透過變更實體 IoT Edge 裝置在 IoT 中樞中的對應裝置身分識別，來變更其設定。

在此教學課程中，我們會使用 Visual Studio Code 建立新的裝置身分識別。 您也可以使用 [Azure 入口網站](how-to-register-device.md#register-in-the-azure-portal)或 [Azure CLI](how-to-register-device.md#register-with-the-azure-cli) 來完成這些步驟。

1. 在您的開發電腦上，開啟 Visual Studio Code。

2. 從 Visual Studio Code 總管檢視展開 [Azure IoT 中樞]  框架。

3. 按一下省略符號，然後選取 [建立 IoT Edge 裝置]  。

4. 指定裝置的名稱。 為求方便，我們使用名稱 **aaTurbofanEdgeDevice**，以便將其排序到所列出裝置的頂端。

5. 新的裝置會出現在裝置清單中。

    ![在 VS Code 總管中檢視新的 aaTurbofanEdgeDevice](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>部署 Azure 虛擬機器

我們使用來自 Azure Marketplace 的 [Azure IoT Edge on Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) 映像為此教學課程建立 IoT Edge 裝置。 Azure IoT Edge on Ubuntu 映像會在啟動時安裝最新版的 Azure IoT Edge 執行階段與其相依性。 我們使用 PowerShell 指令碼 `Create-EdgeVM.ps1`、Resource Manager 範本 `IoTEdgeVMTemplate.json` 和一個殼層指令碼 `install packages.sh` 部署 VM。

### <a name="enable-programmatic-deployment"></a>啟用 [以程式設計方式部署]

若要在指令碼部署中使用來自 Marketplace 的映像，我們需要為映像啟用 [以程式設計方式部署]。

1. 登入 Azure 入口網站。

1. 選取 [所有服務]  。

1. 在搜尋列中，輸入並選取 [Marketplace]  。

1. 在 Marketplace 搜尋列中，輸入並選取 [Azure IoT Edge on Ubuntu]  。

1. 選取 [開始使用]  超連結以便透過程式設計方式進行部署。

1. 選取 [啟用]  按鈕，然後 [儲存]  。

    ![針對 VM 啟用 [以程式設計方式部署]](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. 您會看到成功通知。

### <a name="create-virtual-machine"></a>建立虛擬機器

接下來，執行指令碼為您的 IoT Edge 裝置建立虛擬機器。

1. 開啟 PowerShell 視窗並瀏覽至 [EdgeVM]  目錄。

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. 執行指令碼來建立虛擬機器。

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. 出現提示時，請針對每個參數提供值。 針對訂用帳戶、資源群組與位置，我們建議您使用與此教學課程中所有資源相同的內容。

    * **Azure 訂用帳戶識別碼**：可在 Azure 入口網站中找到
    * **資源群組名稱**：此教學課程中群組資源的易記名稱
    * **位置**：要建立虛擬機器的 Azure 地點。 例如，westus2 或 northeurope。 如需詳細資訊，請參閱所有 [Azure 地點](https://azure.microsoft.com/global-infrastructure/locations/)。
    * **AdminUsername**：您將用來登入虛擬機器的系統管理員帳戶名稱
    * **AdminPassword**：為虛擬機器上的 AdminUsername 設定的密碼

4. 若要使指令碼能夠設定 VM，您需要使用與您正在使用之 Azure 訂用帳戶相關聯的認證登入 Azure。

5. 指令碼將會確認建立 VM 的資訊。 選取 **y** 或 **Enter** 以繼續。

6. 指令碼會執行下列步驟，這需要花費數分鐘的時間：

    * 建立資源群組 (若它尚不存在)
    * 建立虛擬機器
    * 針對連接埠 22 (SSH)、5671 (AMQP)、5672 (AMPQ) 和 443 (TLS) 新增 VM 的 NSG 例外
    * 安裝 [Azure CLI](/cli/azure/install-azure-cli-apt)

7. 該指令碼會輸出 SSH 連接字串以連接到 VM。 複製連接字串以進行下一步。

    ![複製 VM 的 SSH 連接字串](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>連線到 IoT Edge 裝置

接下來的幾節會設定我們所建立的 Azure 虛擬機器。 第一步是連接至虛擬機器。

1. 開啟命令提示字元，並貼上從指令碼輸出中複製的 SSH 連接字串。 根據您在上一節中提供給 PowerShell 指令碼的值，輸入您自己的使用者名稱、後置詞和區域資訊。

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. 當系統提示您驗證主機的真實性時，請輸入 [是]  ，然後選取 **Enter**。

3. 出現提示時，請提供您的密碼。

4. Ubuntu 會顯示歡迎訊息，然後您應該會看到類似 `<username>@<machinename>:~$` 的提示。

## <a name="download-key-vault-certificates"></a>下載 Key Vault 憑證

我們在本文稍早將憑證上傳至 Key Vault，使其可供 IoT Edge 裝置和分葉裝置使用。 分葉裝置是一種下游裝置，會使用 IoT Edge 裝置來作為與 IoT 中樞通訊的閘道。

稍後在此教學課程中，我們將會討論分葉裝置。 在此節中，將憑證下載到 IoT Edge 裝置。

1. 在 Linux 虛擬機器上的 SSH 工作階段中，使用 Azure CLI 登入 Azure。

    ```azurecli
    az login
    ```

1. 系統會提示您開啟瀏覽器至 <https://microsoft.com/devicelogin> 並提供唯一的代碼。 您可以在本機電腦上執行這些步驟。 完成驗證時，請關閉瀏覽器視窗。

1. 成功進行驗證後，Linux VM 將登入並列出您的 Azure 訂用帳戶。

1. 設定您要用於 Azure CLI 命令的 Azure 訂用帳戶。

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. 在 VM 上為憑證建立目錄。

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. 下載您儲存在金鑰保存庫中的憑證：new-edge-device-full-chain.cert.pem、new-edge-device.key.pem 和 azure-iot-test-only.root.ca.cert.pem

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>更新 IoT Edge 裝置設定

IoT Edge 執行階段會使用檔案 `/etc/iotedge/config.yaml` 來保存其設定。 我們需要更新此檔案中的三個資訊：

* **裝置連接字串**：IoT 中樞中來自此裝置身分識別的連接字串
* **憑證**：用於與下游裝置進行連線的憑證
* **主機名稱**：VM IoT Edge 裝置的完整網域名稱 (FQDN)。

我們用來建立 IoT Edge VM 的 *Azure IoT Edge on Ubuntu* 映像隨附一個殼層指令碼，該指令碼會使用連接字串更新 config.yaml。

1. 在 Visual Studio Code 中以滑鼠右鍵按一下 IoT Edge 裝置，然後選取 [複製裝置連接字串]  。

    ![從 Visual Studio Code 複製連接字串](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. 在您的 SSH 工作階段中，執行命令以使用裝置連接字串更新 config.yaml 檔案。

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

接下來，我們將透過直接編輯 config.yaml 來更新憑證和主機名稱。

1. 開啟 config.yaml 檔案。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. 透過移除前置 `#` 並設定路徑來更新 config.yaml 的 certificates 區段，以便檔案看起來如下列範例所示：

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    請確定 **certificates:** 這一行前面沒有空格，且每個巢狀憑證都會內縮兩個空格。

    以滑鼠右鍵按一下 nano，會將剪貼簿的內容貼上您到目前的游標位置。 若要取代字串，請使用鍵盤箭號瀏覽至您想要取代的字串、刪除字串，然後按一下滑鼠右鍵從緩衝區貼上。

3. 在 Azure 入口網站中，瀏覽至您的虛擬機器。 從 [概觀]  區段複製 DNS 名稱 (機器的 FQDN)。

4. 將 FQDN 貼入 config.yml 的主機名稱區段。 請確定名稱全部為小寫。

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. 儲存並關閉檔案 (`Ctrl + X`、`Y`、`Enter`)。

6. 重新啟動 IoT Edge 精靈。

    ```bash
    sudo systemctl restart iotedge
    ```

7. 檢查 IoT Edge 精靈的狀態 (在命令之後，輸入 ":q" 退出)。

    ```bash
    systemctl status iotedge
    ```

8. 如果您在狀態中看到錯誤 (前面加上 "\[ERROR\]" 的彩色文字)，請檢查精靈記錄檔以取得詳細的錯誤資訊。

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>後續步驟

我們剛剛將 Azure VM 設定為 Azure IoT Edge 透明閘道。 我們首先產生測試憑證，然後將其上傳至 Azure Key Vault。 接下來，我們使用指令碼和 Resource Manager 範本從 Azure Marketplace 部署具有 "Ubuntu Server 16.04 LTS + Azure IoT Edge 執行階段" 映像的 VM。 當我們透過 SSH 所連線的 VM 啟動並執行時，我們便登入到 Azure，並從 Key Vault 下載了憑證。 我們已藉由更新 config.yaml 檔案，對 IoT Edge 執行階段的設定進行了數項更新。

如需詳細資訊，請參閱[如何使用 IoT Edge 裝置作為閘道](iot-edge-as-gateway.md)和[設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)。

請前往下一篇文章以建置 IoT Edge 模組。

> [!div class="nextstepaction"]
> [建立和部署自訂 IoT Edge 模組](tutorial-machine-learning-edge-06-custom-modules.md)
