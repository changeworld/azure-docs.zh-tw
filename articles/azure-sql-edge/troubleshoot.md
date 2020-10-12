---
title: 針對 Azure SQL Edge 部署進行疑難排解
description: 瞭解部署 Azure SQL Edge 時可能發生的錯誤
keywords: SQL Edge，疑難排解，部署錯誤
services: sql-edge
ms.service: sql-edge
ms.topic: troubleshooting
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 517fed0dd9eb1736344546bde9f79e52ee17182f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333098"
---
# <a name="troubleshooting-azure-sql-edge-deployments"></a>針對 Azure SQL Edge 部署進行疑難排解 

本文提供部署和使用 Azure SQL Edge 容器時可能發生之錯誤的相關資訊，並提供疑難排解技術來協助解決這些問題。 

Azure SQL Edge 支援兩種部署模型： 
- 透過 Azure IoT Edge 連線的部署： Azure SQL Edge 可在 Azure Marketplace 上取得，並且可部署為 [Azure IoT Edge](../iot-edge/about-iot-edge.md)的模組。 如需詳細資訊，請參閱[部署 Azure SQL Edge](deploy-portal.md)。<br>

- 已中斷連線的部署：可以從 docker hub 提取 Azure SQL Edge 容器映射，並將其部署為獨立的 docker 容器或 kubernetes 叢集。 如需詳細資訊，請參閱 [使用 Docker 部署 AZURE Sql edge](disconnected-deployment.md) 和 [在 Kubernetes 中部署 azure sql edge 容器](deploy-kubernetes.md)。

## <a name="troubleshooting-iot-edge-device-and-deployments"></a>針對 IoT Edge 裝置和部署進行疑難排解

如果您在透過 Azure IoT Edge 部署 SQL Edge 時收到錯誤，請確定 `iotedge` 已正確設定並執行服務。 當您針對與 Azure IoT Edge 相關的問題進行疑難排解時，下列檔可能很有説明：
- [Azure IoT Edge 的常見問題和解決](../iot-edge/troubleshoot-common-errors.md)方式。
- [針對您的 IoT Edge 裝置進行疑難排解](../iot-edge/troubleshoot.md)

## <a name="docker-command-errors"></a>Docker 命令錯誤

如果您收到有關任何 `docker` 命令的錯誤，請確定 Docker 服務正在執行，並嘗試以提升的權限執行。

例如，在 Linux 上，您可能會在執行 `docker` 命令時收到下列錯誤：

```output
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

如果您在 Linux 上收到此錯誤，請嘗試執行前面已加上 `sudo` 的相同命令。 如果該動作失敗，確認 Docker 服務正在執行，並視需要啟動它。

```bash
sudo systemctl status docker
sudo systemctl start docker
```

在 Windows 上，確認您是以系統管理員身分啟動 PowerShell 或命令提示字元。

## <a name="azure-sql-edge-container-startup-errors"></a>Azure SQL Edge 容器啟動錯誤

如果 SQL Edge 容器無法執行，請嘗試下列測試：

- 如果您使用 Azure IoT Edge，請確定已成功下載模組映射，並在模組資訊清單中正確指定環境變數和容器建立選項。

- 如果您使用 docker 或 kubernetes 為基礎的部署，請確定 `docker run` 命令的格式正確。 如需詳細資訊，請參閱 [使用 Docker 部署 AZURE Sql edge](disconnected-deployment.md) 和 [在 Kubernetes 中部署 azure sql edge 容器](deploy-kubernetes.md)。

- 若收到如 `failed to create endpoint CONTAINER_NAME on network bridge. Error starting proxy: listen tcp 0.0.0.0:1433 bind: address already in use.` 的錯誤，即表示正在嘗試將容器連接埠 1433 對應到已在使用中的連接埠。 如果您是在主機電腦本機上執行 SQL Edge，就會發生這種情況。 如果您啟動兩個 SQL Edge 容器，並嘗試將它們對應到相同的主機埠，也可能會發生這種情況。 如果發生這種情況，請使用 `-p` 參數，將容器連接埠 1433 對應到不同的主機連接埠。 例如： 

    ```bash
    sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge-developer.
    ```

- 若在嘗試啟動容器時收到如 `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.30tdout=1&tail=all: dial unix /var/run/docker.sock: connect: permission denied` 的錯誤，請在 Ubuntu 中將使用者新增到 Docker 群組。 接著登出並重新登入，因為此變更將影響新的工作階段。 

   ```bash
    usermod -aG docker $USER
   ```

- 檢查以查看是否有任何來自容器的錯誤訊息。

   ```bash
   docker logs e69e056c702d
   ```

- 如果您使用任何容器管理軟體，請確定它支援以 root 身分執行容器程序。 容器中的 sqlservr 程序會以 root 身分執行。

- 根據預設，Azure SQL Edge 容器會以非根使用者的形式執行，名為 `mssql` 。 如果您使用掛接點或資料磁片區來保存資料，請確定 `mssql` 使用者具有磁片區的適當許可權。 如需詳細資訊，請參閱以 [非根使用者的身份執行](configure.md#run-azure-sql-edge-as-non-root-user) 並 [保存資料](configure.md#persist-your-data)。

- 如果您的 SQL Edge Docker 容器在啟動後立即結束，請檢查您的 Docker 記錄。 若正在 Windows 上的 PowerShell 使用 `docker run` 命令，請使用雙引號，而非單引號。 若是 PowerShell Core，請使用單引號。

- 檢查 [SQL Edge 錯誤記錄](#errorlogs)檔。

## <a name="sql-edge-connection-failures"></a>SQL Edge 連接失敗

如果您無法連接到在容器中執行的 SQL Edge 實例，請嘗試下列測試：

- 查看輸出的 [ **狀態** ] 資料行，以確定您的 SQL Edge 容器正在執行 `docker ps -a` 。 如果沒有，則使用 `docker start <Container ID>` 來啟動它。

- 如果您已對應到非預設的主機連接埠 (不是 1433)，請確定您會在連接字串中指定該連接埠。 您可以在 `docker ps -a` 輸出的 [連接埠] 欄中看到連接埠對應。 如需有關連接到 Azure SQL Edge 的詳細資訊，請參閱 [Connect 和查詢 AZURE Sql edge](connect.md)

- 如果您先前已使用對應的資料磁片區或資料磁片區容器來部署 SQL Edge，且現在使用現有的對應資料磁片區或資料磁片區容器，則 SQL Edge 會忽略 `MSSQL_SA_PASSWORD` 環境變數的值。 相反地，會使用先前設定的 SA 使用者密碼。 這是因為 SQL Edge 會重複使用對應磁片區或資料磁片區容器中的現有 master 資料庫檔案。 如果您遇到此問題，您可以使用下列選項：

    - 使用先前使用過的密碼（如果仍然可用）進行連接。
    - 將 SQL Edge 設定為使用不同的對應磁片區或資料磁片區容器。
    - 從對應的磁片區或資料磁片區容器中，移除現有的 master 資料庫檔案 (主要 .mdf 和 mastlog.ldf .mdf) 。

- 檢查 [SQL Edge 錯誤記錄](#errorlogs)檔。

## <a name="sql-edge-setup-and-error-logs"></a><a id="errorlogs"></a> SQL Edge 設定和錯誤記錄檔

根據預設，SQL Edge 錯誤記錄檔會出現在容器內的 **/var/opt/mssql/log** 目錄中，而且可以使用下列任何一種方式來存取：

- 如果您已在建立容器時將主機目錄裝載至 **/var/opt/mssql**，則可改為查看主機中對應路徑上的**記錄**子目錄。
- 使用互動式命令提示字元來連接至容器。 如果容器並未執行，請先啟動容器。 接著，使用互動式命令提示字元來檢查記錄。 您可透過執行 `docker ps` 命令來取得容器識別碼。

    ```bash
    docker start <ContainerID>
    docker exec -it <ContainerID> "/bin/bash"
    ```

    從您容器內部的 Bash 工作階段，執行下列命令：

    ```bash
    cd /var/opt/mssql/log
    cat errorlog
    ```
- 如果 SQL Edge 容器已啟動且正在執行，而且您可以使用用戶端工具連接到實例，則您可以使用預存程式 `sp_readerrorlog` 來讀取 SQL Edge 錯誤記錄檔的內容。

## <a name="execute-commands-in-a-container"></a>在容器中執行命令

如果您有執行中的容器，就可以在該容器內，從主機終端機執行命令。

若要取得容器識別碼，請執行：

```bash
docker ps -a
```

若要在容器中啟動 Bash 終端機，請執行：

```bash
docker exec -it <Container ID> /bin/bash
```

現在您可以執行命令，就像是在容器內部的終端機中執行它們一樣。 完成後，鍵入 `exit`。 這會在互動式命令工作階段中結束，但您的容器會繼續執行。

### <a name="enabling-verbose-logging"></a>啟用詳細資訊記錄

如果串流引擎的預設記錄層級未提供足夠的資訊，可以在 SQL Edge 中啟用串流引擎的 debug 記錄。 若要啟用 debug 記錄，請將 `RuntimeLogLevel=debug` 環境變數新增至您的 SQL Edge 部署。 啟用偵錯工具記錄之後，請嘗試重現問題，並檢查記錄中是否有任何相關的訊息或例外狀況。 

> [!NOTE]
> 詳細資訊記錄選項應該只用于疑難排解，而不應該用於一般生產工作負載。 


## <a name="next-steps"></a>後續步驟

- [在 SQL Edge 中使用 ONNX 的 Machine Learning 和人工智慧](onnx-overview.md)
- [Azure SQL Edge 中的資料串流](stream-data.md)
- [資料保留和清除](data-retention-overview.md)
- [填滿時間間距並輸入遺漏值](imputing-missing-values.md)







