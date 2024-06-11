# Delete the replica table in the region US West (N. California).
aws dynamodb update-table --table-name Music --cli-input-json --region us-east-1 \
'{
    "ReplicaUpdates":
    [
        {
        "Delete": {
            "RegionName": "us-west-1"
            }
        }
    ]
}'

# Wait a few minutes until the secondary table be deleted. The primary table status will change from 
# UPDATING to ACTIVE. You can check the deletion already finished using the command below:
aws dynamodb describe-table --table-name Music --region us-east-1 | jq -r '.Table.TableStatus'


# After the deletion of secondary table be completed, delete the primary table Music in the region US East (N. Virginia).
aws dynamodb delete-table --table-name Music --region us-east-1

