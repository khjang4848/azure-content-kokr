<properties
   pageTitle="일반 SQL 커넥터 단계별 가이드 | Microsoft Azure"
   description="이 문서에서는 일반 SQL 커넥터를 사용하여 간단한 HR 시스템을 만드는 단계별 지침을 안내합니다."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="andkjell"/>

# 일반 SQL 커넥터 단계별 가이드
이 항목은 단계별 가이드입니다. 이 항목에서는 간단한 샘플 HR 데이터베이스를 만들고 이를 사용하여 일부 사용자와 해당 그룹 멤버 자격을 가져옵니다.

## 샘플 데이터베이스 준비
SQL Server를 실행하는 서버에서 [부록 A](#appendix-a)에 있는 SQL 스크립트를 실행합니다. 이 스크립트는 GSQLDEMO라는 샘플 데이터베이스를 만듭니다. 만들어진 데이터베이스의 개체 모델은 다음 그림과 같습니다. ![개체 모델](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\objectmodel.png)

또한 데이터베이스에 연결하는 데 사용할 사용자를 만듭니다. 이 연습에서 사용자는 FABRIKAM\\SQLUser이며 도메인에 있습니다.

## ODBC 연결 파일 만들기
일반 SQL 커넥터는 ODBC를 사용하여 원격 서버에 연결합니다. 먼저 ODBC 연결 정보를 사용하여 파일을 만들어야 합니다.

1. 서버에서 ODBC 관리 유틸리티를 시작합니다. ![ODBC](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc.png)
2. **파일 DSN** 탭을 선택합니다. **추가...**를 클릭합니다. ![ODBC1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc1.png)
3. Windows에서 제공하지 않는 드라이버가 원활하게 작동하므로 이를 선택하고 **다음>**을 클릭합니다. ![ODBC2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc2.png)
4. **GenericSQL**과 같은 파일 이름을 지정합니다. ![ODBC3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc3.png)
5. **마침**을 클릭합니다. ![ODBC4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc4.png)
6. 이제 연결을 구성해야 합니다. 데이터 원본에 대한 적절한 설명을 제공하고 SQL Server를 실행하는 서버의 이름을 입력합니다. ![ODBC5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc5.png)
7. SQL을 사용하여 인증할 방법을 선택합니다. 이 예제에서는 Windows 인증을 사용합니다. ![ODBC6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc6.png)
8. 샘플 데이터베이스의 이름 **GSQLDEMO**를 입력합니다. ![ODBC7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc7.png)
9. 이 화면에서는 모든 항목을 기본값으로 유지합니다. **마침**을 클릭합니다. ![ODBC8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc8.png)
10. 모든 항목이 예상대로 작동하는지 확인하기 위해 **데이터 원본 테스트**를 클릭합니다. ![ODBC9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc9.png)
11. 테스트에 성공했는지 확인합니다. ![ODBC10](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc10.png)
12. 이제 파일 DSN에 ODBC 구성 파일이 표시됩니다. ![ODBC11](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc11.png)

필요한 파일이 있으므로 커넥터 만들기를 시작할 수 있습니다.

## 일반 SQL 커넥터 만들기

1. Synchronization Service Manager UI에서 **커넥터** 및 **만들기**를 선택합니다. **일반 SQL(Microsoft)**을 선택하고 설명이 포함된 이름을 입력합니다. ![Connector1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector1.png)
2. 이전 섹션에서 만든 DSN 파일을 찾아서 서버에 업로드합니다. 데이터베이스에 연결할 자격 증명을 제공합니다. ![Connector2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector2.png)
3. 이 연습에서는 작업을 쉽게 하기 위해 **User**와 **Group**이라는 두 개의 개체가 있다고 가정합니다. ![Connector3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector3.png)
4. 특성을 찾기 위해 커넥터에서 테이블 자체를 확인하여 해당 특성을 검색하도록 합니다. **Users**는 SQL의 예약된 단어이므로 대괄호([ ]) 안에 이 단어를 입력해야 합니다. ![Connector4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector4.png)
5. 이제 앵커 특성 및 DN 특성을 정의해야 합니다. **User**의 경우 username과 EmployeeID라는 두 가지 특성의 조합을 사용합니다. **Group**의 경우 GroupName(실제로는 현실적이지 않지만 이 연습에서는 작동함)을 사용합니다. ![Connector5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector5.png)
6. 일부 특성 유형은 SQL 데이터베이스에서 검색할 수 없습니다. 특히 참조 특성 유형은 검색할 수 없습니다. Group 개체 유형의 경우 OwnerID와 MemberID를 참조로 변경해야 합니다. ![Connector6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector6.png)
7. 이전 단계에서 참조 특성으로 선택한 특성에는 이러한 값을 참조하는 개체 유형이 필요합니다. 이 경우에 User 개체 유형입니다. ![Connector7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector7.png)
8. 글로벌 매개 변수 페이지에서 **워터마크**를 델타 전략으로 선택합니다. 또한 날짜/시간 형식 **yyyy-MM-dd HH:mm:ss**를 입력합니다. ![Connector8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector8.png)
9. **파티션 및 계층 구조 구성** 페이지에서 두 개체 유형을 모두 선택합니다. ![Connector9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector9.png)
10. **개체 유형 선택** 및 **특성 선택**에서 두 개체 유형과 모든 특성을 선택합니다. **앵커 구성** 페이지에서 **마침**을 클릭합니다.

## 실행 프로필 만들기

1. Synchronization Service Manager UI에서 **커넥터** 및 **실행 프로필 구성**을 선택합니다. **새 프로필**을 클릭합니다. **전체 가져오기**를 시작합니다. ![Runprofile1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile1.png)
2. **전체 가져오기(스테이지 전용)** 유형을 선택합니다. ![Runprofile2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile2.png)
3. **OBJECT=User** 파티션을 선택합니다. ![Runprofile3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile3.png)
4. **테이블**을 선택하고 **[USERS]**를 입력합니다. 다중값 개체 유형 섹션까지 아래로 스크롤하여 다음 그림과 같이 데이터를 입력합니다. **마침**을 선택하여 단계를 저장합니다. ![Runprofile4a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4a.png) ![Runprofile4b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4b.png)
5. **새 단계**를 선택합니다. 이번에는 **OBJECT=Group**을 선택합니다. 마지막 페이지에서 다음 그림과 같이 구성을 사용합니다. **마침**을 클릭합니다. ![Runprofile5a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5a.png) ![Runprofile5b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5b.png)
6. 선택 사항: 원하는 경우 추가 실행 프로필을 구성할 수 있습니다. 이 연습에서는 전체 가져오기만 사용합니다.
7. **확인**을 클릭하여 실행 프로필 변경을 완료합니다.

## 일부 테스트 데이터를 추가하고 가져오기 테스트
샘플 데이터베이스에서 일부 테스트 데이터를 입력합니다. 준비되었으면 **실행** 및 **전체 가져오기**를 선택합니다.

다음은 두 개의 전화 번호가 있는 사용자와 몇 명의 구성원이 있는 그룹입니다. ![cs1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs1.png) ![cs2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs2.png)

## 부록 A
**샘플 데이터베이스를 만드는 SQL 스크립트**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
	[MemberID] [int] NOT NULL,
	[Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
	[GroupID] [int] NOT NULL,
	[GROUPNAME] [nvarchar](200) NOT NULL,
	[DESCRIPTION] [nvarchar](200) NULL,
	[WATERMARK] [datetime] NULL,
	[OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
	[GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
	[USER_ID] [int] NULL,
	[Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
	[USERID] [int] NOT NULL,
	[USERNAME] [nvarchar](200) NOT NULL,
	[FirstName] [nvarchar](100) NULL,
	[LastName] [nvarchar](100) NULL,
	[DisplayName] [nvarchar](100) NULL,
	[ACCOUNTDISABLED] [bit] NULL,
	[EMPLOYEEID] [int] NOT NULL,
	[WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
	[USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```

<!---HONumber=AcomDC_0914_2016-->