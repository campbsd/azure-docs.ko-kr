---
title: "Azure CLI 스택을에 연결 | Microsoft Docs"
description: "크로스 플랫폼 명령줄 인터페이스 (CLI)를 사용 하 여 관리 하 고 스택에서 Azure 리소스를 배포 하는 방법을 알아봅니다"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: sngun
ms.openlocfilehash: 5ef64e727615d17ae550efbc7ea427936d7d4c3b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="install-and-configure-cli-for-use-with-azure-stack"></a>설치 하 고 Azure 스택과 함께 사용 하기 위해 CLI를 구성

이 문서에서는 우리 안내해 Azure CLI (명령줄 인터페이스) 리소스를 관리할 Azure 스택 개발 키트에서 Linux 및 Mac 클라이언트 플랫폼을 사용 하는 과정입니다. 

## <a name="export-the-azure-stack-ca-root-certificate"></a>Azure 스택 CA 루트 인증서 내보내기

Azure 스택 개발 키트 환경 내에서 실행 중인 가상 컴퓨터에서 CLI를 사용 하는 경우 직접 검색할 수 있도록 Azure 스택 루트 인증서가 이미 가상 컴퓨터에 설치 합니다. 개발 키트 외부 워크스테이션에서 CLI를 사용 하는 경우에 개발 키트에서 Azure 스택 CA 루트 인증서를 내보낸을 개발 워크스테이션 (외부 Linux 또는 Mac 플랫폼)의 Python 인증서 저장소에 추가 해야 합니다. 

PEM 형식으로 Azure 스택 루트 인증서를 내보내려면 개발 키트에 로그인 하 고 다음 스크립트를 실행 합니다.

```powershell
   $label = "AzureStackSelfSignedRootCert"
   Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
   $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
   if (-not $root)
   {
       Log-Error "Certificate with subject CN=$label not found"
       return
   }

   Write-Host "Exporting certificate"
   Export-Certificate -Type CERT -FilePath root.cer -Cert $root

   Write-Host "Converting certificate to PEM format"
   certutil -encode root.cer root.pem
```

## <a name="install-cli"></a>CLI 설치

다음으로 개발 워크스테이션에 로그인 하 고 CLI를 설치 합니다. Azure 스택 2.0 버전의 Azure CLI가 필요 합니다. 에 설명 된 단계를 사용 하 여를 설치 하는 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli) 문서. 설치가 성공 했는지 여부를 확인 하려면 터미널 또는 명령 프롬프트 창을 열고 다음 명령을 실행 합니다.

```azurecli
az --version
```

버전의 Azure CLI 및 컴퓨터에 설치 된 다른 종속 라이브러리 표시 되어야 합니다.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Azure 스택 CA 루트 인증서를 신뢰 합니다.

Azure 스택 CA 루트 인증서를 신뢰 하도록 기존 Python 인증서를 추가 합니다. Azure 스택 환경 내에서 만든 Linux 컴퓨터에서 CLI를 실행 하는 경우에 다음 bash 명령을 실행 합니다.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

Azure Sack 환경 외부 컴퓨터에서 CLI를 실행 하는 경우 먼저 설정 해야 [Azure 스택에 VPN 연결](azure-stack-connect-azure-stack.md)합니다. 이제 개발 워크스테이션에 이전에 내보낸 PEM 인증서를 복사 하 고 개발 워크스테이션의 운영 체제에 따라 다음 명령을 실행 합니다.

### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path root.pem -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>가상 컴퓨터 별칭 끝점을 설정 합니다.

사용자는 CLI를 사용 하 여 가상 컴퓨터를 만들 수 있습니다, 전에 클라우드 관리자는 가상 컴퓨터 이미지 별칭을 포함 하는 공개적으로 액세스할 수 있는 끝점을 설정 하 고 클라우드로이 끝점을 등록 해야 합니다. `endpoint-vm-image-alias-doc` 에서 매개 변수는 `az cloud register` 명령은이 목적을 위해 사용 됩니다. 클라우드 관리자 더 이미지 별칭 끝점에 추가 하기 전에 Azure 스택 마켓플레이스 이미지를 다운로드 해야 합니다.
   
Azure는 다음 URI를 사용 하는 예를 들어: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json 합니다. 클라우드 관리자 Azure 스택 시장에서 사용할 수 있는 이미지와 Azure 스택에 대 한 유사한 끝점을 설정 해야 합니다.

## <a name="connect-to-azure-stack"></a>Azure Stack에 연결

다음 단계를 사용 하 여 Azure 스택 연결할:

1. Azure 스택 환경을 실행 하 여 등록 된 `az cloud register` 명령입니다.
   
   a. 등록 하는 *클라우드 관리* 환경에서 사용 하 여:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   b. 등록 하는 *사용자* 환경에서 사용 하 여:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

2. 다음 명령을 사용 하 여 활성 환경을 설정 합니다.

   a. 에 대 한는 *클라우드 관리* 환경에서 사용 하 여:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. 에 대 한는 *사용자* 환경에서 사용 하 여:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

3. Azure 스택 특정 API 버전 프로필을 사용 하도록 환경 구성을 업데이트 합니다. 구성을 업데이트 하려면 다음 명령을 실행 합니다.

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. 사용 하 여 Azure 스택 환경에 로그인 된 `az login` 명령입니다. 로그인 할 수 있습니다 Azure 스택 환경에 사용자 또는으로 [서비스 사용자](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects)합니다. 

   * 로 로그인 한 *사용자*: 사용자 이름과 암호를 직접 내에서 지정 하거나는 `az login` 명령을 선택 하거나 브라우저를 사용 하 여 인증 합니다. 사용자 계정에 다단계 인증을 사용 하는 경우 두 번째 작업을 수행 해야 합니다.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > 사용자 계정에 사용 하도록 설정 하는 다단계 인증을 하는 경우 사용할 수 있습니다는 `az login command` 제공 하지 않고는 `-u` 매개 변수입니다. URL 및 인증을 사용 해야 하는 코드를 제공 명령을 실행 합니다.
   
   * 로 로그인 한 *서비스 사용자*: 로그인 하기 전에 [Azure 포털을 통해 서비스 사용자를 만들](azure-stack-create-service-principals.md) 또는 CLI 고 역할을 할당 합니다. 이제, 다음 명령을 사용 하 여 로그인 합니다.

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>연결 테스트

모든 했으므로 설치 프로그램을 Azure 스택 내에서 리소스를 만들 CLI를 사용 하겠습니다. 예를 들어 응용 프로그램에 대 한 리소스 그룹을 만들고 가상 컴퓨터를 추가할 수 있습니다. 다음 명령을 사용 하 여 "MyResourceGroup" 라는 리소스 그룹을 만듭니다.

```azurecli
az group create \
  -n MyResourceGroup -l local
```

리소스 그룹이 성공적으로 생성 되는 경우이 명령은 새로 만든된 리소스의 다음 속성을 출력 합니다.

![리소스 그룹에 대 한 출력 생성](media/azure-stack-connect-cli/image1.png)

## <a name="next-steps"></a>다음 단계

[Azure CLI을 사용하여 템플릿 배포](azure-stack-deploy-template-command-line.md)

[사용자 권한 관리](azure-stack-manage-permissions.md)

