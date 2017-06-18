Azure Backup 서비스에는 Backup 자격 증명 모음과 Recovery Services 자격 증명 모음의 두 가지 자격 증명 모음 유형이 있습니다. Backup 자격 증명 모음을 먼저 제공했습니다. 그런 다음 Recovery Services 자격 증명 모음에서 확장된 Resource Manager 배포가 지원됩니다. Microsoft에서는 클래식 배포가 특별히 필요한 경우가 아니면 Resource Manager 배포를 사용하도록 권장합니다.

| **배포웹사이트를** | **포털** | **자격 증명 모음** |
|-----------|------|-----|
|클래식|[클래식](https://manage.windowsazure.com)|백업|
|리소스 관리자|[Azure](https://portal.azure.com)|복구 서비스|

> [AZURE.NOTE] 백업 자격 증명 모음을 사용하여 Resource Manager에서 배포한 솔루션을 보호할 수는 없습니다. 그러나 복구 서비스 자격 증명 모음을 사용하면 클래식 방식으로 배포한 서버와 VM을 보호할 수는 있습니다.

<!---HONumber=AcomDC_0921_2016-->