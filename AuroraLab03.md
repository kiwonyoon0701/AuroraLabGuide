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

1. "Start Session"을 Click

<kbd> ![GitHub Logo](images/1-start-session.png) </kbd>