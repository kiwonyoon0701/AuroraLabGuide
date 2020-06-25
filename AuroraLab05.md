# DB Backtrack

1. 사용자가 실수로 Table을 drop하는 상황을 만듭니다. mysql에 접속을 하고 아래의 명령어들을 수행합니다. 아래 실행 한 결과들을 메모장에 복사합니다. Timestamp를 이용하여 실수로 Drop한 Table을 BackTrack으로 복구 할 것 입니다.

`mysql -h[clusterEndpoint] -u$DBUSER -p"$DBPASS" mylab`

```
SELECT current_timestamp();

**10초 정도 대기**

DROP TABLE sbtest1;

SELECT current_timestamp();

quit;

```

<kbd> ![GitHub Logo](images/5-drop-table.png) </kbd>
