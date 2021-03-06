<properties
	pageTitle="Log Analytics에서 사용자 지정 대시보드 만들기 | Microsoft Azure"
	description="이 가이드는 Log Analytics 대시보드가 저장된 모든 로그 검색을 시각화하여 환경을 보는 단일 렌즈를 제공하는 방법을 이해하는 데 도움이 됩니다."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Log Analytics에서 사용자 지정 대시보드 만들기

이 가이드는 Log Analytics 대시보드가 저장된 모든 로그 검색을 시각화하여 환경을 보는 단일 렌즈를 제공하는 방법을 이해하는 데 도움이 됩니다.

![예제 대시보드](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

## 내 대시보드를 만드는 방법

먼저 왼쪽 탐색 모음에서 개요 단추를 클릭하여 OMS 개요로 이동합니다. 왼쪽에 "내 대시보드" 타일이 표시됩니다. 타일을 클릭하여 대시보드로 드릴다운합니다.

![개요](./media/log-analytics-dashboards/oms-dashboards-overview.png)



## 타일 추가

대시보드에서 타일은 저장된 로그 검색을 기반으로 합니다. OMS에는 여러 저장된 로그 검색이 미리 만들어져 있으므로 바로 시작할 수 있습니다. 시작하는 방법을 간략하게 설명하는 다음 그림이 표시됩니다.

![그림](./media/log-analytics-dashboards/oms-dashboards-pictorial.png)

내 대시보드 뷰에서 페이지 맨 아래에 있는 '사용자 지정' 기어를 클릭하여 사용자 지정 모드로 전환합니다. 페이지 오른쪽에 열리는 패널에는 작업 영역의 저장된 로그 검색이 모두 표시됩니다.

![타일 추가 1](./media/log-analytics-dashboards/oms-dashboards-add-tile1.png)

저장된 로그 검색을 타일로 시각화하려면 왼쪽의 빈 공간으로 끌어다 놓습니다. 저장된 검색을 끌면 타일로 바뀝니다.

![타일 추가 2](./media/log-analytics-dashboards/oms-dashboards-add-tile2.png)

![타일 추가 3](./media/log-analytics-dashboards/oms-dashboards-add-tile3.png)


## 타일 편집

내 대시보드 뷰에서 페이지 맨 아래에 있는 '사용자 지정' 기어를 클릭하여 사용자 지정 모드로 전환합니다. 편집하려는 타일을 클릭합니다. 오른쪽 패널이 편집할 수 있도록 바뀌고 다양한 옵션을 제공합니다.

![타일 편집](./media/log-analytics-dashboards/oms-dashboards-edit-tile.png)

### 타일 시각화#
다음 두 종류의 타일 시각화 중에서 선택할 수 있습니다.

|차트 종류|수행하는 작업|
|---|---|
|![가로 막대형 차트](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png)|로그 검색 결과가 필드에 따라 집계되는지 여부에 따라 저장된 로그 검색 결과의 타임라인 또는 필드별 결과 목록을 표시합니다.
|![메트릭](./media/log-analytics-dashboards/oms-dashboards-metric.png)|총 로그 검색 결과 적중 횟수를 타일에 숫자로 표시합니다. 메트릭 타일을 사용하면 임계값에 도달할 때 타일이 강조 표시되도록 임계값을 설정할 수 있습니다.|

### 임계값
메트릭 시각화를 사용하여 타일에 임계값을 만들 수 있습니다. 타일에 임계값을 만들려면 선택합니다. 값이 선택된 임계값을 초과하거나 미만일 때 타일을 강조 표시할지 여부를 선택하고 아래에서 임계값을 설정합니다.

## 대시보드 구성
대시보드를 구성하려면 내 대시보드 뷰로 이동한 다음 페이지 맨 아래에 있는 '사용자 지정' 기어를 클릭하여 사용자 지정 모드로 전환합니다. 이동할 타일을 클릭하고 끌어서 원하는 위치로 타일을 이동합니다.

![대시보드 구성](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## 타일 제거
타일을 제거하려면 내 대시보드 뷰로 이동한 다음 페이지 맨 아래에 있는 **사용자 지정** 기어를 클릭하여 사용자 지정 모드로 전환합니다. 제거할 타일을 선택한 다음 오른쪽 패널에서 **타일 제거**를 선택합니다.

![타일 제거](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## 다음 단계

- 알림을 생성하고 문제를 해결하기 위해 Log Analytics에서 경고를 만듭니다.

<!---HONumber=AcomDC_0504_2016-->