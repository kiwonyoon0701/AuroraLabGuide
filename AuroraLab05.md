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

2. 다음의 sysbench command를 이용하여 새로운 `sbtest1` table을 생성하고 data를 넣습니다. [clusterEndpoint]를 변경한 후 실행합니다.

```
sysbench oltp_write_only \
--threads=1 \
--mysql-host=[clusterEndpoint] \
--mysql-user=$DBUSER \
--mysql-password="$DBPASS" \
--mysql-port=3306 \
--tables=1 \
--mysql-db=mylab \
--table-size=1000000 prepare
```

<kbd> ![GitHub Logo](images/5-sysbench.png) </kbd>

3. DB에 다시 접속하여 `sbtest1` table의 checksum을 확인합니다. 기존 Table의 Drop되고 새로운 Table에 Sysbench를 이용하여 신규 Data를 넣었기 때문에 기존 Checksum과 값이 달라짐을 확인합니다.

```
mysql -h[clusterEndpoint] -u$DBUSER -p"$DBPASS" mylab

checksum table sbtest1;

quit;
```

4. BackTrack을 이용하여 실수로 삭제했던 `sbtest1` table을 복원합니다. 우선 drop 직후의 시점으로 Database를 BackTrack합니다. (drop command 수행 후 확인했던 Timestamp를 이용합니다.)

```
aws rds backtrack-db-cluster \
--db-cluster-identifier auroralab-mysql-cluster \
--backtrack-to "2020-06-25T03:51:01Z"
```

<kbd> ![GitHub Logo](images/5-backtrack1.png) </kbd>

5. Backtrack 상태를 아래의 command로 조회합니다. available 상태가 될때까지 기다립니다. (BackTrack을 위해 몇 분 정도 소요됩니다.)

```
aws rds describe-db-clusters \
--db-cluster-identifier auroralab-mysql-cluster \
| jq -r '.DBClusters[0].Status'
```

<kbd> ![GitHub Logo](images/5-backtrack2.png) </kbd>

<kbd> ![GitHub Logo](images/5-backtrack3.png) </kbd>

6. Aurora DB에 접속하여 Table들을 확인합니다. 우리는 `sbtest1` table이 Drop된 직후로 backtrack했기 때문에 `sbtest1`은 존재하지 않아야 합니다.

<kbd> ![GitHub Logo](images/5-show-tables.png) </kbd>

**Drop table 직후 상태로 backtrack했기 때문에 table이 존재하지 않습니다.**

7. BackTrack을 이용하여 실수로 삭제했던 `sbtest1' table을 복원합니다. 이번에는 Drop Table 이전 시점으로 backtrack하겠습니다. (drop command 수행 전 확인했던 Timestamp를 이용합니다.) available 상태까지 기다립니다.

```
aws rds backtrack-db-cluster \
--db-cluster-identifier auroralab-mysql-cluster \
--backtrack-to "yyyy-mm-ddThh:mm:ssZ"
```

<kbd> ![GitHub Logo](images/5-backtrack4.png) </kbd>

```
aws rds describe-db-clusters \
--db-cluster-identifier auroralab-mysql-cluster \
| jq -r '.DBClusters[0].Status'
```

<kbd> ![GitHub Logo](images/5-backtrack5.png) </kbd>

8. Aurora DB에 접속하여 Table들을 확인합니다. `sbtest1` table drop 이전 시점으로 backtrack했기 때문에 `sbtest1` table이 존재하여 하며, 기존의 Checksum과 동일한 값을 Return 해야 합니다.

```
mysql -h[clusterEndpoint] -u$DBUSER -p"$DBPASS" mylab

show tables;

checksum table sbtest1;

quit;
```

<kbd> ![GitHub Logo](images/5-backtrack4.png) </kbd>
