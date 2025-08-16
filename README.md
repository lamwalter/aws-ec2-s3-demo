# EC2 → S3 Demo (Least-Privilege + Lifecycle)

**Bucket:** `walter-qa-bucket-001`  
**EC2 Role:** `EC2-S3Access-Role` (instance profile)  
**Region:** ca-central-1

## What I built
- IAM **bucket-scoped access** for an EC2 instance (list bucket; read/write/delete objects).
- **Strict policy** variant: access limited to `ec2-demo/*` and `lifecycle-demo/*`, plus **TLS-only** (deny if not using HTTPS).
- S3 **Lifecycle rule**: filter by tag `temp=true`, move to Intelligent-Tiering (Day 0), **expire on Day 1**.

## Policies
- Baseline: [`policies/03_bucket_policy.json`](policies/03_bucket_policy.json)
- Strict: [`policies/13_strict_policy.json`](policies/13_strict_policy.json)

## Screenshots
See `screenshots/2025-08/`:
- 01_s3_bucket_list.png
- 02_s3_bucket_properties.png
- 03_s3_access_policy.png
- 03b_s3_policy_entities.png
- 04_attach_policy_to_role.png
- 05_ec2_list_with_iam_role.png
- 06_ec2_s3_access_test.png
- 07_ec2_s3_upload_test.png
- 08_s3_bucket_after_upload.png
- 09_ec2_s3_demo_terminal.png
- 10_s3_bucket_objects.png
- 11_object_with_lifecycle_and_tag.png
- 12_lifecycle_rule_details.png
- 13_strict_policy_json.png
- 14_role_permissions_strict_attached.png

## How I tested (from EC2)
```bash
# baseline positive tests
aws s3 ls s3://walter-qa-bucket-001/
echo ok | aws s3 cp - s3://walter-qa-bucket-001/ec2-demo/ok.txt
aws s3 cp s3://walter-qa-bucket-001/ec2-demo/ok.txt .

# strict negative test (expected AccessDenied)
echo nope | aws s3 cp - s3://walter-qa-bucket-001/forbidden/deny.txt
