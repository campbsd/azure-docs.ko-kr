---
title: "자습서: IQNavigator VMS와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 IQNavigator VMS 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 1164723a171843541098f6adbda0e65f7e82a0cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>자습서: IQNavigator VMS와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 IQNavigator VMS를 통합하는 방법에 대해 알아봅니다.

IQNavigator VMS를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

- IQNavigator VMS에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 IQNavigator VMS에 자동으로 로그온(Single Sign-On) 되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](active-directory-appssoaccess-whatis.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

IQNavigator VMS와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- IQNavigator VMS Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [평가판 제품](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 IQNavigator VMS 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>갤러리에서 IQNavigator VMS 추가
IQNavigator VMS가 Azure AD에 통합되도록 구성하려면 갤러리에서 IQNavigator VMS를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 IQNavigator VMS를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에서 **IQNavigator VMS**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_search.png)

5. 결과 창에서 **IQNavigator VMS**를 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 IQNavigator VMS에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 IQNavigator VMS 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 IQNavigator VMS의 관련 사용자 간에 링크 관계가 설정되어야 합니다.

IQNavigator VMS에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

IQNavigator VMS에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[IQNavigator VMS 테스트 사용자 만들기](#creating-a-iqnavigator-vms-test-user)** - Britta Simon의 Azure AD 표현과 연결된 사용자를 IQNavigator VMS에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 IQNavigator VMS 응용 프로그램에서 Single Sign-On을 구성합니다.

**IQNavigator VMS에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **IQNavigator VMS** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-on 구성][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-on 구성](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_samlbase.png)

3. **IQNavigator VMS 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url.png)

    a. **식별자** 텍스트 상자에 URL `iqn.com`을 입력합니다.

    b. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

4. **고급 URL 설정 표시**를 확인하고, 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url1.png)

    **릴레이 상태** 텍스트 상자에서 `https://<subdomain>.iqnavigator.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 회신 URL 및 릴레이 상태로 값을 업데이트합니다. 이러한 값을 얻으려면 [IQNavigator VMS 클라이언트 지원 팀](https://www.beeline.com/iqn-product-support/)에 문의하세요. 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_400.png)

6. **메타데이터** URL을 생성하려면 다음 단계를 수행합니다.

    a. **앱 등록**을 클릭합니다.
    
    ![Single Sign-on 구성](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_appregistrations.png)
   
    b. **끝점**을 클릭하여 **끝점** 대화 상자를 엽니다.  
    
    ![Single Sign-on 구성](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_endpointicon.png)

    c. 복사 단추를 클릭하여 **페더레이션 메타데이터 문서** URL을 복사하여 메모장에 붙여 넣습니다.
    
    ![Single Sign-on 구성](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_endpoint.png)
     
    d. 이제 **IQNavigator VMS**의 속성 페이지로 이동하고 **복사** 단추를 사용하여 **응용 프로그램 ID**를 복사하여 메모장에 붙여넣습니다.
 
    ![Single Sign-on 구성](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_appid.png)

    e. `<FEDERATION METADATA DOCUMENT url>?appid=<application id>` 패턴을 사용하여 **메타데이터 URL**을 생성합니다.

7. IQNavigator 응용 프로그램은 이름 식별자 클레임에 고유한 사용자 ID 값을 필요로 합니다. 고객은 이름 식별자 클레임에 대한 올바른 값을 매핑할 수 있습니다. 이 경우 데모 목적으로 user.UserPrincipalName을 매핑했습니다. 그러나 조직 설정에 따라 올바른 값을 매핑해야 합니다.   

    ![Single Sign-on 구성](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_attribute.png)

8. **IQNavigator VMS 구성** 섹션에서 **IQNavigator VMS 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_configure.png) 

9. **IQNavigator VMS** 쪽에서 Single Sign-On을 구성하려면 **메타데이터 URL**, **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 [IQNavigator VMS 지원 팀](https://www.beeline.com/iqn-product-support/)에 보내야 합니다. 그러면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-iqnavigatorvms-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-iqnavigatorvms-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-iqnavigatorvms-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-iqnavigatorvms-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    c. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-iqnavigator-vms-test-user"></a>IQNavigator VMS 테스트 사용자 만들기

이 섹션의 목적은 IQNavigator VMS에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다. IQNavigator VMS 계정에서 사용자를 추가하려면 [IQNavigator VMS 지원 팀](https://www.beeline.com/iqn-product-support/)에 문의하세요.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 IQNavigator VMS에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 IQNavigator VMS에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **IQNavigator VMS**를 선택합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 IQNavigator VMS 타일을 클릭하면 IQNavigator VMS 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_203.png

