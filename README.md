# Elasticsearch Operations

## Copy ES snapshot from Opensearch to local

Scenario:
We have an Elasticsearch cluster in AWS Opensearch.  And we use ElasticVue to interact with the Elasticsearch.  We can take snapshots and see the snapshot taken via ElasticVue.  How do we download its snapshots to our local disk storage without corrupting the data integrity of them?

Solution #1
To download snapshots from an AWS OpenSearch (Elasticsearch) cluster to your local disk storage without corrupting the data integrity, you can follow these steps:

### Prerequisites:
1. **AWS CLI Installed**: Ensure you have the AWS Command Line Interface (CLI) installed and configured on your local machine.
2. **S3 Bucket**: Your snapshots should be stored in an Amazon S3 bucket.

### Steps:

1. **Identify the Snapshot Repository**:
   - In ElasticVue, navigate to the "Snapshot Repositories" section and identify the S3 repository where your snapshots are stored.

2. **Check Snapshot Details**:
   - List the snapshots using ElasticVue or the `_snapshot` API to confirm the snapshot name you want to download:
     ```bash
     GET _snapshot/your_s3_repository/_all
     ```

3. **Download Snapshots using AWS CLI**:
   - Use the AWS CLI to copy the snapshots from the S3 bucket to your local disk.

   **Step-by-Step Commands**:

   - **Configure AWS CLI** (if not already configured):
     ```bash
     aws configure
     ```
     Enter your AWS Access Key, Secret Key, region, and output format.

   - **Create a Local Directory**:
     Create a directory on your local machine where you want to store the snapshots:
     ```bash
     mkdir -p ~/elasticsearch-snapshots
     ```

   - **Copy the Snapshot from S3 to Local Storage**:
     Use the `aws s3 cp` command to recursively copy the snapshots from the S3 bucket to your local directory:
     ```bash
     aws s3 cp s3://your-s3-bucket/path/to/snapshots ~/elasticsearch-snapshots --recursive
     ```
     Replace `s3://your-s3-bucket/path/to/snapshots` with the actual S3 bucket path and `~/elasticsearch-snapshots` with your desired local path.

### Verifying Data Integrity:

1. **Compare Checksums**:
   - After downloading, ensure the integrity of the snapshots by comparing checksums. AWS S3 provides `etag` values that can be used for this purpose.
   - Use a tool like `md5sum` to generate checksums of the downloaded files and compare them with the `etag` values from S3:
     ```bash
     aws s3api list-objects --bucket your-s3-bucket --prefix path/to/snapshots --query "Contents[].{Key: Key, ETag: ETag}"
     ```
   - Use the `md5sum` command on your local files and compare the results.

2. **Check Snapshot Integrity with Elasticsearch**:
   - Optionally, you can re-register the local snapshot repository in a local Elasticsearch instance to verify that the snapshots can be read correctly.

### Example Commands to Verify:

```bash
# List objects in the S3 bucket and get their ETag values
aws s3api list-objects --bucket your-s3-bucket --prefix path/to/snapshots --query "Contents[].{Key: Key, ETag: ETag}"

# Generate local checksums and compare with the above ETag values
md5sum ~/elasticsearch-snapshots/path/to/file
```

### Important Tips:

- Ensure your local storage has enough space to accommodate the snapshots.
- Perform the downloads during low-traffic periods to minimize impact on your Elasticsearch cluster.
- Regularly verify the integrity of your downloaded snapshots to ensure data consistency.

By following these steps, you can download your Elasticsearch snapshots from AWS OpenSearch to your local disk storage while maintaining data integrity.

## Copy ES snapshot from local to GCP Elasticsearch
TBA
