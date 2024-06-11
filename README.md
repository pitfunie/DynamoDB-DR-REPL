# DynamoDB-DR-REPL
# Replicate Amazon DynamoDB tables in other AWS Regions

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

