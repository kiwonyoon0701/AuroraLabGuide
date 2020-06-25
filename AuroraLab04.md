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

4. 아래 화면처럼 Available이라고 나오면 Cloning이 완료 된 것입니다. 2번째 줄에서 Cloning으로 새롭게 생성된 Instance의 Endpoint를 확인 할 수 있습니다.

<kbd> ![GitHub Logo](images/4-describe-cluster.png) </kbd>

5. 생성된 Cluster에 DB Instance를 추가합니다.

```
aws rds create-db-instance \
--db-instance-class db.r5.large \
--engine aurora-mysql \
--db-cluster-identifier auroralab-mysql-clone \
--db-instance-identifier auroralab-mysql-clone-instance
```

6. 신규 Instance 생성 상태를 확인하고 available 상태가 되는지 확인합니다.

```
aws rds describe-db-instances \
--db-instance-identifier auroralab-mysql-clone-instance \
| jq -r '.DBInstances[0].DBInstanceStatus'
```

7. 기존 DB Instance와 Cloning된 Instance가 같은지 확인합니다. 다음의 명령어로 Clone된 신규 Instance에 접속합니다. [cluster endpoint of clone]은 3번 Step의 `describe-db-clusters`를 이용하여 확인 가능합니다.

`mysql -h[cluster endpoint of clone] -u$DBUSER -p"$DBPASS" mylab`
