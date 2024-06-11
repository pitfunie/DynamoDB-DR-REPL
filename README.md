# DynamoDB-DR-REPL
# Replicate Amazon DynamoDB tables in other AWS Regions
# It is spossible to verify how replicated Amazon DynamoDB tables can be used in a 
# strategy of the type. Active-Active, both for reading and for data persist.

# Create an Amazon DynamoDB table
# Create a new table Music in the region US East (N. Virginia)
aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=Song,AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \
        AttributeName=Song,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST \
    --stream-specification StreamEnabled=true,StreamViewType=NEW_AND_OLD_IMAGES \
    --region us-east-1

# Create a replica of the table in another AWS Region
# Create a table of Music identical in the US West (N. California).
aws dynamodb update-table --table-name Music --cli-input-json --region us-east-1 \
    '{
        "ReplicaUpdates":
        [
            {
            "Create": {
                "RegionName": "us-west-1"
                }
            }
        ]
    }' 

# view the list of replicas created using describe-table
aws dynamodb describe-table --table-name Music --region us-east-1

# Enter sample data to verify that replication is working, add a new item to the Music table 
# in the region US East (N. Virginia).
aws dynamodb put-item \
    --table-name Music \
    --item '{"Artist": {"S":"The Beatles"},"Song": {"S":"Hey Jude"}}' \
    --region us-east-1

# To verify that replication is bidirectional, add a new item to the Music table in the region
#  US East (N. California).
aws dynamodb put-item \
    --table-name Music \
    --item '{"Artist": {"S":"The Clash"},"Song": {"S":"London Calling"}}' \
    --region us-west-1

# Make sure the items have been successfully replicated to the regions
aws dynamodb scan --table-name Music --region us-east-1
aws dynamodb scan --table-name Music --region us-west-1


