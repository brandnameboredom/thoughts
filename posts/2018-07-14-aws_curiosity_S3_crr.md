---
title: AWS Curiosities - S3 Cross Region Replication
description: AWS Curiosities - S3 Cross Region Replication
date: 2018-07-14
tags:
  - AWS
  - S3
---

# S3

## Cross Region Replication

When two s3 buckets are configured for cross region replication and a deletion marker is created for a file in one of the s3 buckets it is replicated over to the linked s3 bucket.  However, if the deletion marker is itself deleted, that action is not replicated. Also, if a specific version of the file is deleted that will also not be replicated.  

Hmm.  

My assumption is that these items are not replicated because these are subtractive operations.  The original deletion marker is considered a new object so s3 replicates it to the destination bucket while the removal of the deletion marker (or specific file version) does not add any new objects and therefore is not replicated.  

Still a bit odd though...  

For more information you can check out [what is and what is not replicated].

[what is and what is not replicated]: https://docs.aws.amazon.com/AmazonS3/latest/dev/crr-what-is-isnot-replicated.html
