# DB Cloning

1. Session Manager를 이용하여 ubuntu user로 접속합니다.

2. Lab01에서 확인 했던 `dbSecurityGroup`, `dbSubnetGroup`을 확인하고 아래 command에서 수정 후 실행합니다.

```
aws rds restore-db-cluster-to-point-in-time \
--restore-type copy-on-write \
--use-latest-restorable-time \
--source-db-cluster-identifier auroralab-mysql-cluster \
--db-cluster-identifier auroralab-mysql-clone \
--vpc-security-group-ids [dbSecurityGroup] \
--db-subnet-group-name [dbSubnetGroup] \
--backtrack-window 86400
```

<kbd> ![GitHub Logo](images/4-clone.png) </kbd>

3. Cloning 상태를 확인하기 위해 다음의 command를 실행합니다.

```
aws rds describe-db-clusters \
--db-cluster-identifier auroralab-mysql-clone \
| jq -r '.DBClusters[0].Status, .DBClusters[0].Endpoint'
```

<kbd> ![GitHub Logo](images/3-create-procedure.png) </kbd>
dbSecurityGroupdbSubnetGroup
