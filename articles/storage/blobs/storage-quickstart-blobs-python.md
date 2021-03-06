---
title: "Azure 빠른 시작 - Python을 사용하여 Azure Blob Storage에서 개체 전송 | Microsoft Docs"
description: "Python을 사용하여 Azure Blob Storage에서 개체를 전송하는 방법을 간단히 알아봅니다."
services: storage
documentationcenter: storage
author: ruthogunnnaike
manager: cwatson
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/12/2017
ms.author: v-ruogun
ms.openlocfilehash: 44ec416a814ff6a5fef79ef21e2f54ce4ce4da17
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-python"></a>Python을 사용하여 Azure Blob Storage에서 개체 전송
이 빠른 시작에서 Python을 사용하여 Azure Blob Storage에서 컨테이너에 블록 blob을 업로드, 다운로드 및 나열하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다. 
* [Python 설치](https://www.python.org/downloads/)
* [Azure Storage SDK for Python](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python) 다운로드 및 설치 

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="create-a-storage-account-using-the-azure-portal"></a>Azure Portal을 사용하여 저장소 계정 만들기

먼저 이 빠른 시작 가이드에서 사용할 새 범용 저장소 계정을 만듭니다. 

1. [Azure Portal](https://portal.azure.com)로 이동한 후 Azure 계정을 사용하여 로그인합니다. 
2. 허브 메뉴에서 **새로 만들기** > **저장소** > **저장소 계정 - blob, 파일, 테이블, 큐**를 선택합니다. 
3. 저장소 계정의 이름을 입력합니다. 이 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 또한 고유해야 합니다.
4. `Deployment model`을 **Resource Manager**로 설정합니다.
5. `Account kind`를 **일반적인 용도**로 설정합니다.
6. `Performance`를 **표준**으로 설정합니다. 
7. `Replication`을 **LRS(로컬 중복 저장소)**로 설정합니다.
8. `Storage service encryption`을 **사용 안 함**으로 설정합니다.
9. `Secure transfer required`를 **사용 안 함**으로 설정합니다.
10. 사용 중인 구독을 선택합니다. 
11. `resource group`에 대해 새 리소스 그룹을 만들고 고유한 이름을 지정합니다. 
12. 저장소 계정에 사용할 `Location`을 선택합니다.
13. **대시보드에 고정**을 선택하고 **만들기**를 클릭하여 저장소 계정을 만듭니다. 

저장소 계정을 만들면 대시보드에 고정됩니다. 클릭하여 엽니다. **설정**에서 **액세스 키**를 클릭합니다. 키를 선택하고 나중에 사용할 수 있게 저장소 계정 이름을 클립보드에 복사한 후 메모장에 붙여 넣습니다.

## <a name="download-the-sample-application"></a>샘플 응용 프로그램 다운로드
이 빠른 시작 가이드에서 사용되는 [샘플 응용 프로그램](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git)은 기본 Python 응용 프로그램입니다.  

[git](https://git-scm.com/)을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드합니다. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. Python 프로그램을 열려면 storage-blobs-python-quickstart 폴더와 example.py 파일을 찾습니다.  

## <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성
응용 프로그램에서 `BlockBlobService` 개체를 만들려면 저장소 계정 이름과 계정 키를 제공해야 합니다. IDE의 솔루션 탐색기에서 `example.py` 파일을 엽니다. **accountname** 및 **accountkey** 값을 해당하는 계정 이름과 키로 바꿉니다. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>샘플 실행
이 샘플에서는 'Documents' 폴더에 테스트 파일을 만듭니다. 샘플 프로그램은 Blob 저장소에 테스트 파일을 업로드하고, 컨테이너에 Blob를 나열하며, 새 이름으로 파일을 다운로드합니다. 

샘플을 실행합니다. 다음 출력은 응용 프로그램 실행 시 반환되는 출력의 예제입니다.
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
아무 키나 눌러 계속하면 샘플 프로그램이 저장소 컨테이너 및 파일을 삭제합니다. 계속하기 전에 'Documents' 폴더에서 두 파일을 확인합니다. 이 파일을 열어 동일한지 확인할 수 있습니다.

[Azure Storage 탐색기](http://storageexplorer.com)와 같은 도구를 사용하여 Blob Storage의 파일을 볼 수도 있습니다. Azure Storage 탐색기는 저장소 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다. 

파일을 확인한 후에 아무 키나 눌러 데모를 완료하고 테스트 파일을 삭제합니다. 이 샘플의 용도 파악했으므로 example.py 파일을 열고 코드를 확인합니다. 

## <a name="get-references-to-the-storage-objects"></a>저장소 개체에 대한 참조 가져오기
가장 먼저 할 일은 Blob Storage의 액세스 및 관리에 사용되는 개체에 대한 참조를 만드는 것입니다. 이러한 개체는 서로를 기준으로 작성됩니다. 즉, 각 개체가 목록의 다음 개체에 사용됩니다.

* 저장소 계정의 Blob 서비스를 가리키는 **CloudBlobClient** 개체를 인스턴스화합니다. 

* 액세스하는 컨테이너를 나타내는 **CloudBlobContainer** 개체를 인스턴스화합니다. 컨테이너는 컴퓨터에서 폴더를 사용하여 파일을 구성하는 것과 같이 blob을 구성하는 데 사용됩니다.

Cloud Blob 컨테이너가 있으면 관심 있는 특정 blob을 가리키는 **CloudBlockBlob** 개체를 인스턴스화하고, 업로드, 다운로드, 복사 등의 작업을 수행할 수 있습니다.

이 섹션에서는 개체를 인스턴스화하고, 새 컨테이너를 만든 다음, 컨테이너에 대해 사용 권한을 설정하여 Blob를 공용 Blob로 유지합니다. 컨테이너를 **quickstartblobs**로 지칭합니다. 


```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
## <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 블록 Blob는 가장 일반적으로 사용되므로 이 빠른 시작 가이드에서도 사용합니다.  

Blob에 파일을 업로드하려면 로컬 드라이브에서 디렉터리 이름과 파일 이름을 조인하여 파일의 전체 경로를 가져옵니다. 그런 다음 **create\_blob\_from\_path** 메서드를 사용하여 지정된 경로에 파일을 업로드할 수 있습니다. 

샘플 코드는 업로드 및 다운로드에 사용할 로컬 파일을 만들고 해당 파일이 **file\_path\_to\_file** 및 Blob 이름 **local\_file\_name**으로 업로드되게 저장합니다. 다음 예제에서는 **quickstartblobs**라는 저장소에 이 파일을 업로드합니다.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Blob 저장소에서 사용할 수 있는 몇 가지 업로드 메서드가 있습니다. 예를 들어 메모리 스트림이 있는 경우 **create\_blob\_from\_path** 대신 **create\_blob\_from\_stream** 메서드를 사용할 수 있습니다. 

블록 blob 크기는 4.7TB까지 가능하며, Excel 스프레드시트에서 큰 비디오 파일까지 다양할 수 있습니다. 페이지 blob은 IaaS VM을 백업하는 데 사용되는 VHD 파일에 주로 사용됩니다. 추가 Blob은 파일에 쓰고 더 많은 정보를 계속해서 추가하려는 경우처럼 로깅에 사용됩니다. Blob Storage에 저장된 대부분의 개체는 블록 Blob입니다.

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

**list_blobs** 메서드를 사용하여 컨테이너의 파일 목록을 가져옵니다. 이 메서드는 생성기를 반환합니다. 다음 코드는 Blob 목록을 검색하고, 이 과정을 반복하여 컨테이너에서 찾은 Blob의 이름을 표시합니다.  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

## <a name="download-the-blobs"></a>Blob 다운로드

**get\_blob\_to\_path** 메서드를 사용하여 Blob를 로컬 디스크에 다운로드합니다. 다음 코드는 이전 섹션에서 업로드된 Blob를 다운로드합니다. 로컬 디스크에서 두 파일을 확인할 수 있게 "_DOWNLOADED"가 접미사로 Blob 이름에 추가됩니다. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

## <a name="clean-up-resources"></a>리소스 정리
이 빠른 시작 가이드에서는 업로드된 blob이 더 이상 필요하지 않으므로 **delete\_container**를 사용하여 전체 컨테이너를 삭제해도 됩니다. 만든 파일이 더 이상 필요하지 않으면 **delete\_blob** 메서드를 사용하여 파일을 삭제합니다.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="next-steps"></a>다음 단계
 
이 빠른 시작 가이드에서는 Python을 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 알아보았습니다. Blob Storage를 사용하는 방법을 자세히 알아보려면 계속해서 Blob Storage 방법을 진행하세요.

> [!div class="nextstepaction"]
> [Blob Storage 작업 방법](./storage-python-how-to-use-blob-storage.md)
 

Storage 탐색기 및 Blob에 대한 자세한 내용은 [Storage 탐색기를 사용하여 Azure Blob Storage 리소스 관리](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.
