### VM에 대한 네트워크 보안 그룹 인바운드 규칙 구성

인터넷을 통해 SQL Server에 연결할 수 있도록 하려는 경우 SQL Server 인스턴스가 수신 중인 포트에 대한 네트워크 보안 그룹에 인바운드 규칙을 구성해야 합니다. 기본적으로 TCP 포트 1433입니다.

1. 포털에서 **가상 컴퓨터**를 선택한 다음 SQL Server VM을 선택합니다.

3. 그런 후 **네트워크 인터페이스**를 선택합니다.

	![네트워크 인터페이스](./media/virtual-machines-sql-server-connection-steps/rm-network-interface.png)

4. VM에 대한 네트워크 인터페이스를 선택합니다.

4. **네트워크 보안 그룹** 링크를 클릭합니다.

	![네트워크 인터페이스](./media/virtual-machines-sql-server-connection-steps/rm-network-security-group.png)

6. 네트워크 보안 그룹의 속성에서 **인바운드 보안 규칙**을 확장합니다.

5. **추가** 단추를 클릭합니다.

6. **이름**으로 "SQLServerPublicTraffic"을 제공합니다.

7. **프로토콜**을 **TCP**로 변경합니다.

8. **대상 포트 범위**인 1433(또는 SQL Server 인스턴스가 수신 중인 포트)을 지정합니다.

9. **작업**이 **허용**으로 설정되었는지 확인합니다. 보안 규칙 대화 상자는 다음 스크린샷과 유사해야 합니다.

	![네트워크 보안 규칙](./media/virtual-machines-sql-server-connection-steps/rm-network-security-rule.png)

9. **확인**을 클릭하여 VM에 대한 규칙을 저장합니다.

>[AZURE.NOTE] 두 번째 네트워크 보안 그룹이 서브넷과 연결될 수 있습니다(VM의 네트워크 보안 그룹과는 별개임). 기본적으로 이 작업은 수행되지 않습니다. 그렇지만 서브넷에서 네트워크 보안 그룹을 만든 경우 서브넷 및 VM의 네트워크 보안 그룹 둘 다에서 포트 1433을 열어야 합니다.

<!---HONumber=AcomDC_0921_2016-->