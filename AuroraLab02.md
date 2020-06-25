# Session Manager를 이용해서 EC2 인스턴스에 접속

1. 다음의 주소를 복사해서 Browser의 새 창에 Copy and Paste합니다.

`https://us-west-2.console.aws.amazon.com/systems-manager/session-manager?region=us-west-2`

2. "Start Session"을 Click

<kbd> ![GitHub Logo](images/1-start-session.png) </kbd>

3. `auroralab-mysql-workstation`을 선택한 후 "Start Session"을 Click

<kbd> ![GitHub Logo](images/1-connect-session.png) </kbd>

4. Terminal 이 뜨면 `sudo su -l ubuntu`를 통해 ssm-user에서 ubuntu User로 Switching.(항상 ubuntu User로 작업을 해야 합니다.)

<kbd> ![GitHub Logo](images/1-terminal-sudo.png) </kbd>

5. `tail -n1 /debug.log`를 확인하여 다음의 Log가 있는지 확인 

`* bootstrap complete, rebooting`

6. mysql client를 사용해서 aurora instance로 접속을 확인합니다.

`mysql -h[clusterEndpoint] -u$DBUSER -p"$DBPASS" -e"SELECT @@aurora_version;"`

**COMMAND & Output Example**

<kbd> ![GitHub Logo](images/1-connection-check2.png) </kbd>

**앞으로 있을 Terminal 작업은 모두 위의 Session Manager 접속을 통해서 이뤄집니다.**
**Session Manager가 Timeout되서 Close될 경우 위의 순서로 다시 여시면 됩니다.**

1. 수고하셨습니다. 다음 챕터로 이동하세요. [AuroraLab03.md](AuroraLab03.md)