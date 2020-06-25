# LAB 환경 구성

1. AWS Console Login - 자신의 AWS IAM 계정으로 Login 합니다. 
`https://console.aws.amazon.com/`

<kbd> ![GitHub Logo](images/2-login.png) </kbd>

2. Region에서 US West(Oregon) 을 선택

<kbd> ![GitHub Logo](images/2-region-select.png) </kbd>

3. 다음의 주소를 복사해서 Browser의 새 창에 Copy and Paste합니다.

`https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?stackName=auroralab&templateURL=https://s3.amazonaws.com/ams-stack-prod-content-us-east-1/templates/lab_template.yml&param_deployCluster=Yes`

4. "I acknowledge that AWS CloudFormation might create IAM resources with custom names"를 Check 하고 "Create Stack" Click

<kbd> ![GitHub Logo](images/2-create-stack-confirm.png) </kbd>

5. Stack 생성에 약 15-20분 정도 소요 됩니다.

6. "Event" Page에서 진행 상황을 확인 합니다.

7. 아래 화면처럼 Stack Resource가 "CREATE_COMPLETE"가 되면 환경 구성이 완료된 것입니다.

<kbd> ![GitHub Logo](images/2-stack-status.png) </kbd>

8. Stack 생성이 완료되면 Outputs Tab을 Click하고, KEY와 Value를 별도의 메모장에 복사해둡니다. (이후 Step에서 사용됨)

<kbd> ![GitHub Logo](images/2-stack-outputs.png) </kbd>

9. BastionInstance 항목에 `i-0123456789abcdef0` 형태의 Instance ID가 존재하는지 확인하세요.

수고하셨습니다. 다음 챕터로 이동하세요. [AuroraLab02.md](AuroraLab02.md)

kkk




2-stack-outputs.png

2-stack-status

**Bucket Name** : `oracle-to-s3-dms-kiwony`

<kbd> ![GitHub Logo](images2. Regionㅇ에엣에서서 OREGONUSUS WEST/1.png) </kbd>