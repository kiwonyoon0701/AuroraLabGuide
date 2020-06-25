# DB 접속, Data Loading, Auto Scaling

1. Lab01에서 복사해둔 CloudFormation Output중에 [clusterEndpoint]를 확인합니다.

2. Session Manager Terminal에서 ubuntu user를 사용하여 다음 명령어로 DB에 접속 합니다.

`mysql -h[clusterEndpoint] -u$DBUSER -p"$DBPASS" mylab`

3. mysql session에서 다음의 명령어로 Procedure를 생성합니다. 이 Procedure를 사용하여 이후 Step에서 Database에서 부하를 줍니다.

```
DELIMITER $$
DROP PROCEDURE IF EXISTS minute_rollup$$
CREATE PROCEDURE minute_rollup(input_number INT)
BEGIN
 DECLARE counter int;
 DECLARE out_number float;
 set counter=0;
 WHILE counter <= input_number DO
 SET out_number=SQRT(rand());
 SET counter = counter + 1;
END WHILE;
END$$
DELIMITER ;
```

<kbd> ![GitHub Logo](images/3-create-procedure.png) </kbd>

4. Initial Table을 생성합니다. mysql session에서 다음의 명령어를 수행합니다.

```
DROP TABLE IF EXISTS `sbtest1`;
CREATE TABLE `sbtest1` (
 `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
 `k` int(10) unsigned NOT NULL DEFAULT '0',
 `c` char(120) NOT NULL DEFAULT '',
 `pad` char(60) NOT NULL DEFAULT '',
PRIMARY KEY (`id`),
KEY `k_1` (`k`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
```

<kbd> ![GitHub Logo](images/3-create-table.png) </kbd>

5. S3에 Data를 다음의 명령어로 Loading 합니다. mysql session에서 다음의 명령어를 수행합니다.

```
LOAD DATA FROM S3 MANIFEST
's3-us-east-1://awsauroralabsmy-us-east-1/samples/sbdata/sample.manifest'
REPLACE
INTO TABLE sbtest1
CHARACTER SET 'latin1'
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\r\n';
```

<kbd> ![GitHub Logo](images/3-data-load.png) </kbd>

6. 정상적으로 Loading이 끝났다는 message가 나오면 아래의 명령어로 mysql session에서 나옵니다.

`quit;`

7. Read-Only Workload를 생성하여 Aurora Auto Scaling을 확인합니다. 다음의 명령어를 Session Manager에서 ubuntu user로 실행합니다. Lab01에서 확인했던 [readerEndpoint]를 확인하고 아래 명령어 실행시 변경해줍니다.

`python3 reader_loadtest.py -e[readerEndpoint] -u$DBUSER -p"$DBPASS" -dmylab`

8. RDS => Databases => auroralab-mysql-cluster를 선택합니다. Reader Instance의 CPU부하가 증가하는 것을 확인합니다.

<kbd> ![GitHub Logo](images/3-read-load.png) </kbd>

9. 몇분 후 Auto Scaling Policy에 의해 새로운 Reader Node가 추가되는 것을 확인합니다.

<kbd> ![GitHub Logo](images/3-aas-create-reader.png) </kbd>

10. 신규 Read Replica가 Available이 되면 부하가 분산되어 CPU Usage가 낮아짐을 확인합니다.

<kbd> ![GitHub Logo](images/3-read-load-balanced.png) </kbd>

11. Session Manager terminal에서 부하를 주고 있는 Session을 `Ctrl+c`를 이용하여 중지합니다.
