---
title: "클라우드 서비스(클래식)에 대해 SSL 구성 | Microsoft Docs"
description: "웹 역할에 대해 HTTPS 끝점을 지정하는 방법 및 응용 프로그램 보안을 위해 SSL 인증서를 업로드하는 방법에 대해 알아봅니다."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 4cbb7f38-7994-454d-b4f0-7259b558c766
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: adegeo
ms.openlocfilehash: 7df2371f64f0d8a9fabc0df37c17d4dfbc47cd7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Azure에서 응용 프로그램에 대한 SSL 구성
> [!div class="op_single_selector"]
> * [Azure 포털](cloud-services-configure-ssl-certificate-portal.md)
> * [Azure 클래식 포털](cloud-services-configure-ssl-certificate.md)
> 
> 

SSL(Secure Socket Layer) 암호화는 인터넷을 통해 전송되는 데이터 보호에 가장 일반적으로 사용되는 방법입니다. 이 일반 작업에서는 웹 역할에 대해 HTTPS 끝점을 지정하는 방법 및 응용 프로그램 보안을 위해 SSL 인증서를 업로드하는 방법에 대해 설명합니다.

> [!NOTE]
> 이 작업의 절차는 Azure 클라우드 서비스에 적용됩니다. 앱 서비스에 대해서는 [이](../app-service/app-service-web-tutorial-custom-ssl.md) 문서를 참조하세요.
> 
> 

이 작업에서는 프로덕션 배포를 사용합니다. 스테이징 배포에 대한 정보는 이 항목의 끝에 제공됩니다.

클라우드 서비스를 아직 만들지 않은 경우 먼저 [이](cloud-services-how-to-create-deploy.md) 문서를 읽어보세요.

## <a name="step-1-get-an-ssl-certificate"></a>1단계: SSL 인증서 다운로드
응용 프로그램에 대해 SSL을 구성하려면 먼저 이 목적으로 인증서를 발급하는 신뢰할 수 있는 타사 CA(인증 기관)에서 서명한 SSL 인증서를 가져와야 합니다. 아직 없는 경우 SSL 인증서를 판매하는 회사에서 구입해야 합니다.

인증서는 Azure의 SSL 인증서에 대한 다음 요구 사항을 충족해야 합니다.

* 인증서에 개인 키가 포함되어 있어야 합니다.
* 개인 정보 교환(.pfx) 파일로 내보낼 수 있는 키 교환용 인증서를 만들어야 합니다.
* 인증서의 주체 이름은 클라우드 서비스 액세스에 사용되는 도메인과 일치해야 합니다. cloudapp.net 도메인에 사용되는 SSL 인증서는 CA(인증 기관)에서 얻을 수 없습니다. 서비스에 액세스할 때 사용할 사용자 지정 도메인 이름을 획득해야 합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 응용 프로그램 액세스에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다. 예를 들어 사용자 지정 도메인 이름이 **contoso.com**인 경우 CA에서 ***.contoso.com** 또는 **www.contoso.com**에 대한 인증서를 요청합니다.
* 인증서는 최소한 2048비트 암호화를 사용해야 합니다.

테스트용으로 자체 서명된 인증서를 [만들어](cloud-services-certs-create.md) 사용할 수 있습니다. 자체 서명된 인증서는 CA를 통해 인증되지 않으며 cloudapp.net 도메인을 웹 사이트 URL로 사용할 수 있습니다. 예를 들어 다음 작업에서는 인증서에서 사용되는 CN(일반 이름)이 **sslexample.cloudapp.net**인 자체 서명된 인증서를 사용합니다.

다음으로 인증서에 대한 정보를 서비스 정의 및 서비스 구성 파일에 포함해야 합니다.

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>2단계: 서비스 정의 및 구성 파일 수정
인증서를 사용하도록 응용 프로그램을 구성하고 HTTPS 끝점을 추가해야 합니다. 따라서 서비스 정의 및 서비스 구성 파일을 업데이트해야 합니다.

1. 개발 환경에서 서비스 정의 파일(CSDEF)을 열고 **WebRole** 섹션 내에 **Certificates** 섹션을 추가한 후 인증서(및 중간 인증서)에 대한 다음 정보를 포함합니다.
   
    ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate" 
                        storeLocation="LocalMachine" 
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```
   
   **Certificates** 섹션에서는 인증서의 이름, 위치 및 인증서가 위치한 저장소의 이름을 정의합니다.
   
   권한(`permisionLevel` 특성)은 다음 값 중 하나로 설정될 수 있습니다.
   
   | 권한 값 | 설명 |
   | --- | --- |
   | limitedOrElevated |**(기본값)** 모든 역할 프로세스는 개인 키에 액세스할 수 있습니다. |
   | elevated |승격된 프로세스만 개인 키에 액세스할 수 있습니다. |
2. 서비스 정의 파일에서 **끝점** 섹션 내에 **InputEndpoint** 요소를 추가하여 HTTPS를 사용하도록 설정합니다.
   
    ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. 서비스 정의 파일에서 **Sites** 섹션 내에 **Binding** 요소를 추가합니다. 이 섹션은 HTTPS 바인딩을 추가하여 끝점을 사이트에 매핑합니다.
   
    ```xml   
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```
   
   서비스 정의 파일에서 필요한 사항은 모두 변경했지만 인증서 정보를 서비스 구성 파일에 추가해야 합니다.
4. 서비스 구성 파일(CSCFG), ServiceConfiguration.Cloud.cscfg에서 **Role** 섹션 내에 **Certificates** 섹션을 추가하여 아래에 표시된 샘플 지문 값을 인증서의 값으로 바꿉니다.
   
    ```xml   
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate" 
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc" 
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

이전 예제에서는 지문 알고리즘에 **sha1**을 사용합니다. 인증서의 지문 알고리즘에 적합한 값을 지정하세요.

서비스 정의 및 서비스 구성 파일이 업데이트되었으므로 Azure에 업로드할 배포를 패키지합니다. **cspack**를 사용하는 경우 **/generateConfigurationFile** 플래그를 사용하지 않도록 하세요. 이 플래그는 방금 삽입한 인증서 정보를 덮어씁니다.

## <a name="step-3-upload-a-certificate"></a>3단계: 인증서 업로드
배포 패키지가 인증서를 사용하도록 업데이트되었으며 HTTPS 끝점이 추가되었습니다. 이제 Azure 클래식 포털을 사용하여 패키지 및 인증서를 Azure에 업로드할 수 있습니다.

1. [Azure 클래식 포털][Azure classic portal]에 로그인합니다. 
2. 클릭할 왼쪽 탐색 모음 창의 **클라우드 서비스**를 클릭합니다.
3. 원하는 클라우드 서비스를 클릭합니다.
4. **인증서** 탭을 클릭합니다.
   
    ![인증서 탭을 클릭합니다.](./media/cloud-services-configure-ssl-certificate/click-cert.png)

5. **업로드** 단추를 클릭합니다.
   
    ![업로드](./media/cloud-services-configure-ssl-certificate/upload-button.png)
    
6. **파일**, **암호**를 제공한 후 **완료**를 클릭합니다(확인 표시).

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>4단계: HTTPS를 사용하여 역할 인스턴스에 연결
이제 Azure에서 배포가 실행되고 있으므로 HTTPS를 사용하여 연결할 수 있습니다.

1. Azure 클래식 포털에서 배포를 선택한 다음 **사이트 URL**아래의 링크를 클릭합니다.
   
   ![사이트 URL 확인][2]
2. 웹 브라우저에서 **http** 대신 **https**를 사용하도록 링크를 수정한 다음 페이지를 방문합니다.
   
   > [!NOTE]
   > 자체 서명된 인증서를 사용하는 경우 자체 서명된 인증서와 연결된 HTTPS 끝점으로 이동하면 브라우저에 인증서오류가 표시됩니다. 신뢰할 수 있는 인증 기관에서 서명한 인증서를 사용하면 이 문제가 해결되지만 이 오류는 무시할 수 있습니다. 또 다른 옵션으로 사용자의 신뢰할 수 있는 인증 기관 인증서 저장소에 자체 서명된 인증서를 추가할 수 있습니다.
   > 
   > 
   
   ![SSL 예제 웹 사이트][3]

프로덕션 배포가 아닌 스테이징 배포에 SSL을 사용하려면 먼저 스테이징 배포에 사용된 URL을 확인해야 합니다. 인증서나 인증서 정보를 포함하지 않고도 스테이징 환경에 클라우드 서비스를 배포할 수 있습니다. 배포된 다음에는 Azure 클래식 포털의 **사이트 URL** 필드에 나열되는 GUID 기반 URL을 확인할 수 있습니다. GUID 기반 URL과 같은 CN(일반 이름)으로 인증서를 만듭니다(예: **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**). 스테이징된 클라우드 서비스에 인증서를 추가하려면 Azure 클래식 포털을 사용합니다. 그런 다음 인증서 정보를 CSDEF 및 CSCFG 파일에 추가하고 응용 프로그램을 다시 패키지하고 스테이징된 배포를 업데이트하여 새 패키지를 사용합니다.

## <a name="next-steps"></a>다음 단계
* [클라우드 서비스의 일반 구성](cloud-services-how-to-configure.md)
* [클라우드 서비스를 배포](cloud-services-how-to-create-deploy.md)하는 방법을 알아봅니다.
* [사용자 지정 도메인 이름](cloud-services-custom-domain-name.md)을 구성합니다.
* [클라우드 서비스를 관리합니다](cloud-services-how-to-manage.md).

[Azure classic portal]: http://manage.windowsazure.com
[0]: ./media/cloud-services-configure-ssl-certificate/CreateCloudService.png
[1]: ./media/cloud-services-configure-ssl-certificate/AddCertificate.png
[2]: ./media/cloud-services-configure-ssl-certificate/CopyURL.png
[3]: ./media/cloud-services-configure-ssl-certificate/SSLCloudService.png
[4]: ./media/cloud-services-configure-ssl-certificate/AddCertificateComplete.png  
