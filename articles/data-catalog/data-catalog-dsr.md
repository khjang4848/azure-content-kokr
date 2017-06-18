<properties
   pageTitle="Azure 데이터 카탈로그 지원되는 데이터 원본 | Microsoft Azure"
   description="현재 지원되는 데이터 원본의 사양."
   services="data-catalog"
   documentationCenter=""
   authors="trhabe"
   manager="jstrauss"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/15/2016"
   ms.author="trhabe"/>

# Azure 데이터 카탈로그 지원되는 데이터 원본

Azure 데이터 카탈로그의 사용자는 등록 도구 클릭 한 번으로 공용 API를 사용하거나 데이터 카탈로그 웹 포털에 정보를 직접 입력하여 메타데이터를 게시할 수 있습니다. 다음 표는 현재 카탈로그로 지원되는 모든 원본 및 각각에 대한 게시 기능을 요약합니다. 또한 포털의 "열기" 경험에서 시작할 수 있는 각 원본에 대한 외부 데이터 도구가 나열되어 있습니다. 문서의 두 번째 표에는 각 데이터 원본 연결 속성의 자세한 기술 사양이 나옵니다.


## 지원되는 데이터 원본 목록

<table>

    <tr>
       <td><b>데이터 원본 개체</b></td>
       <td><b>API</b></td>
       <td><b>수동 입력</b></td>
       <td><b>등록 도구</b></td>
       <td><b>도구에서 열기</b></td>
       <td><b>참고 사항</b></td>
    </tr>

    <tr>
      <td>Azure 데이터 레이크 저장소 디렉터리</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 데이터 레이크 저장소 파일</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 저장소 Blob</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 저장소 디렉터리</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 저장소 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>HDFS 디렉터리</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HDFS 파일</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive 보기</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle 데이터베이스 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle 데이터베이스 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>기타(일반 자산)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL 데이터 웨어하우스 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL 데이터 웨어하우스 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 차원</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 측정값</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services 보고서</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>브라우저</font></td>
      <td><font size=2>기본 모드 서버에만 해당. SharePoint 모드는 지원되지 않음.</font></td>
    </tr>

    <tr>
      <td>SQL Server 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata 테이블</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana 뷰</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2>계산 보기 및 분석 보기만 지원되며, 특성 보기는 지원되지 않습니다.</font></td>
    </tr>

    <tr>
      <td>Db2 테이블</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Db2 뷰</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>파일 시스템 파일</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ftp 디렉터리</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ftp 파일</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http 보고서</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http 끝점</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http 파일</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Odata 엔터티 집합</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Odata 함수</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql 테이블</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql 뷰</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana 뷰</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Salesforce 개체</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SharePoint 목록 </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

추가적인 원본에 대한 지원이 필요하면, [Azure Data Catalog 포럼](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)에 기능 요청을 제출합니다.


<br> <br>
## 데이터 원본 참조 사양
> [AZURE.NOTE] 다음 테이블의 "DSL 구조" 열에는 Azure Data Catalog에 사용되는 "address" 속성 모음에 대한 연결 속성만 나열됩니다.("address" 속성 모음은 Azure Data Catalog에서 유지하는 데이터 원본의 다른 연결 속성을 포함할 수 있지만, 사용하지는 않습니다.)
<table>
    <tr>
       <td><b>원본 유형</b></td>
       <td><b>자산 형식</b></td>
       <td><b>개체 유형</b></td>
       <td><b>DSL 구조<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>컨테이너</td>
      <td>데이터 레이크</td>
      <td>
        <font size=2> 프로토콜: webhdfs
            <br>authentication: {basic, oauth}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>테이블</td>
      <td>디렉터리, 파일</td>
      <td>
        <font size=2> 프로토콜: webhdfs
            <br>authentication: {basic, oauth}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure 저장소</td>
      <td>컨테이너</td>
      <td>컨테이너</td>
      <td>
        <font size=2> protocol: azure-blobs
            <br>인증: {azure-access-key}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; 도메인
            <br>&#160;&#160;&#160;&#160;&#160; 계정
            <br>&#160;&#160;&#160;&#160;&#160; 컨테이너 </font>
      </td>
    </tr>
    <tr>
      <td>Azure 저장소</td>
      <td>테이블</td>
      <td>Blob, 디렉터리</td>
      <td>
        <font size=2> protocol: azure-blobs
            <br>인증: {azure-access-key}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; 도메인
            <br>&#160;&#160;&#160;&#160;&#160; 계정
            <br>&#160;&#160;&#160;&#160;&#160; 컨테이너
            <br>&#160;&#160;&#160;&#160;&#160; 이름 </font>
      </td>
    </tr>
    <tr>
      <td>Azure 저장소</td>
      <td>컨테이너</td>
      <td>컨테이너</td>
      <td>
        <font size=2> protocol: azure-tables
            <br>인증: {azure-access-key}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; 도메인
            <br>&#160;&#160;&#160;&#160;&#160; 계정 </font>
      </td>
    </tr>
    <tr>
      <td>Azure 저장소</td>
      <td>테이블</td>
      <td>테이블</td>
      <td>
        <font size=2> protocol: azure-tables
            <br>인증: {azure-access-key}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; 도메인
            <br>&#160;&#160;&#160;&#160;&#160; 계정
            <br>&#160;&#160;&#160;&#160;&#160; 이름 </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>컨테이너</td>
      <td>가상 클러스터</td>
      <td>
        <font size=2> 프로토콜: cosmos
            <br>인증: {기본, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>테이블</td>
      <td>스트림, 스트림 집합, 뷰</td>
      <td>
        <font size=2> 프로토콜: cosmos
            <br>인증: {기본, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>컨테이너</td>
      <td>사이트</td>
      <td>
        <font size=2> 프로토콜: http
            <br>authentication: {none, basic, windows, oauth}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>보고서</td>
      <td>보고서, 대시보드</td>
      <td>
        <font size=2> 프로토콜: http
            <br>authentication: {none, basic, windows, oauth}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: db2
            <br>인증: {기본, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: db2
            <br>인증: {기본, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; 스키마 </font>
      </td>
    </tr>
    <tr>
      <td>파일 시스템</td>
      <td>테이블</td>
      <td>파일</td>
      <td>
        <font size=2> 프로토콜: 파일
            <br>인증: {없음, 기본, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; 경로 </font>
      </td>
    </tr>
    <tr>
      <td>Ftp</td>
      <td>테이블</td>
      <td>디렉터리, 파일</td>
      <td>
        <font size=2> 프로토콜: ftp
            <br>인증: {없음, 기본, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>컨테이너</td>
      <td>프로비전</td>
      <td>
        <font size=2> 프로토콜: webhdfs
            <br>authentication: {basic, oauth}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>테이블</td>
      <td>디렉터리, 파일</td>
      <td>
        <font size=2> 프로토콜: webhdfs
            <br>authentication: {basic, oauth}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> protocol: hive
            <br>인증: {hdinsight, 기본, 사용자 이름, 없음}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>connectionProperties:
            <br>&#160;&#160;&#160;&#160;&#160; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> protocol: hive
            <br>인증: {hdinsight, 기본, 사용자 이름, 없음}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>connectionProperties:
            <br>&#160;&#160;&#160;&#160;&#160; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>컨테이너</td>
      <td>사이트</td>
      <td>
        <font size=2> 프로토콜: http
            <br>authentication: {none, basic, windows, oauth}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>보고서</td>
      <td>보고서, 대시보드</td>
      <td>
        <font size=2> 프로토콜: http
            <br>authentication: {none, basic, windows, oauth}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>테이블</td>
      <td>끝점, 파일</td>
      <td>
        <font size=2> 프로토콜: http
            <br>authentication: {none, basic, windows, oauth}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: mysql
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: mysql
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; Object </font>
      </td>
    </tr>
    <tr>
      <td>Odata</td>
      <td>컨테이너</td>
      <td>엔터티 컨테이너</td>
      <td>
        <font size=2> 프로토콜: odata
            <br>인증: {없음, 기본, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Odata</td>
      <td>테이블</td>
      <td>엔터티 집합, 함수</td>
      <td>
        <font size=2> 프로토콜: odata
            <br>인증: {없음, 기본, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; 리소스 </font>
      </td>
    </tr>
    <tr>
      <td>Oracle 데이터베이스</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: oracle
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>Oracle 데이터베이스</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: oracle
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; Object </font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: postgresql
            <br>인증: {기본, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: postgresql
            <br>인증: {기본, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; Object </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>컨테이너</td>
      <td>사이트</td>
      <td>
        <font size=2> 프로토콜: http
            <br>authentication: {none, basic, windows, oauth}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>보고서</td>
      <td>보고서, 대시보드</td>
      <td>
        <font size=2> 프로토콜: http
            <br>authentication: {none, basic, windows, oauth}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>파워 쿼리</td>
      <td>테이블</td>
      <td>데이터 매시업</td>
      <td>
        <font size=2> 프로토콜: power-query
            <br>인증: {oauth}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>테이블</td>
      <td>Object</td>
      <td>
        <font size=2> 프로토콜: salesforce-com
            <br>인증: {기본, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; loginServer
            <br>&#160;&#160;&#160;&#160;&#160; class
            <br>&#160;&#160;&#160;&#160;&#160; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>컨테이너</td>
      <td>서버</td>
      <td>
        <font size=2> 프로토콜: sap-hana-sql
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; 서버 </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>테이블</td>
      <td>보기</td>
      <td>
        <font size=2> 프로토콜: sap-hana-sql
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; Object </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>테이블</td>
      <td>나열</td>
      <td>
        <font size=2> 프로토콜: sharepoint-list
            <br>인증: {기본, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>SQL 데이터 웨어하우스</td>
      <td>명령</td>
      <td>저장 프로시저</td>
      <td>
        <font size=2> 프로토콜: tds
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; Object </font>
      </td>
    </tr>
    <tr>
      <td>SQL 데이터 웨어하우스</td>
      <td>TableValuedFunction</td>
      <td>테이블 값 함수</td>
      <td>
        <font size=2> 프로토콜: tds
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; Object </font>
      </td>
    </tr>
    <tr>
      <td>SQL 데이터 웨어하우스</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: tds
          <br>인증: {프로토콜, windows}
          <br>주소:
          <br>&#160;&#160;&#160;&#160;&#160; server
          <br>&#160;&#160;&#160;&#160;&#160; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>SQL 데이터 웨어하우스</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: tds
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; Object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>명령</td>
      <td>저장 프로시저</td>
      <td>
        <font size=2> 프로토콜: tds
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; Object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>테이블 값 함수</td>
      <td>
        <font size=2> 프로토콜: tds
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; Object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: tds
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: tds
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; Object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 다차원</td>
      <td>컨테이너</td>
      <td>모델</td>
      <td>
        <font size=2> 프로토콜: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; 모델 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 다차원</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> 프로토콜: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; 모델
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 다차원</td>
      <td>측정값</td>
      <td>측정값</td>
      <td>
        <font size=2> 프로토콜: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; 모델
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {측정값} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 다차원</td>
      <td>테이블</td>
      <td>차원</td>
      <td>
        <font size=2> 프로토콜: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; 모델
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {차원} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 테이블 형식</td>
      <td>컨테이너</td>
      <td>모델</td>
      <td>
        <font size=2> 프로토콜: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; 모델 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 테이블 형식</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> 프로토콜: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; 모델
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 테이블 형식</td>
      <td>측정값</td>
      <td>측정값</td>
      <td>
        <font size=2> 프로토콜: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; 모델
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {측정값} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 테이블 형식</td>
      <td>테이블</td>
      <td>테이블</td>
      <td>
        <font size=2> 프로토콜: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; 모델
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Table} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>컨테이너</td>
      <td>서버</td>
      <td>
        <font size=2> 프로토콜: reporting-services
            <br>인증: {windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>보고서</td>
      <td>보고서</td>
      <td>
        <font size=2> 프로토콜: reporting-services
            <br>인증: {windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; path
            <br>&#160;&#160;&#160;&#160;&#160; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>컨테이너</td>
      <td>데이터베이스</td>
      <td>
        <font size=2> 프로토콜: teradata
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; 데이터베이스 </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>테이블</td>
      <td>테이블, 뷰</td>
      <td>
        <font size=2> 프로토콜: teradata
            <br>인증: {프로토콜, windows}
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; Object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>컨테이너</td>
      <td>모델</td>
      <td>
        <font size="2"> 프로토콜: mssql-mds
          <br>인증: {windows}
          <br>주소:
          <br>&#160;&#160;&#160;&#160;&#160; url
          <br>&#160;&#160;&#160;&#160;&#160; 모델
          <br>&#160;&#160;&#160;&#160;&#160; 버전 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>테이블</td>
      <td>엔터티</td>
      <td>
        <font size="2"> 프로토콜: mssql-mds
          <br>인증: {windows}
          <br>주소:
          <br>&#160;&#160;&#160;&#160;&#160; url
          <br>&#160;&#160;&#160;&#160;&#160; 모델
          <br>&#160;&#160;&#160;&#160;&#160; 버전
          <br>&#160;&#160;&#160;&#160;&#160; 엔터티 </font>
      </td>
    </tr>
    <tr>
      <td>기타(위에 없는 항목)</td>
      <td>*</td>
      <td>*</td>
      <td>
        <font size=2> 프로토콜: generic-asset
            <br>주소:
            <br>&#160;&#160;&#160;&#160;&#160; assetId </font>
      </td>
    </tr>
</table>

<!---HONumber=AcomDC_0921_2016-->