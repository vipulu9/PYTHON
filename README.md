aws ecs describe-tasks \
    --cluster charter-ecs-cluster \
    --tasks <YOUR_TASK_ARN> \
    --query "tasks[0].attachments[0].details[?name=='privateIPv4Address' || name=='networkInterfaceId']"
    
