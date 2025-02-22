---
title: 'Migrate to cloud: Adapt the file system-based features'
description: To migrate to SCCOS, one of the steps, is restoring Elasticsearch and Redis.
template: howto-guide-template
redirect_from:
- /docs/scos/dev/migration-concepts/migrate-to-sccos/step-8-adapt-the-filesystem-based-features.html
last_updated: Dec 6, 2023

---

Due to the specifics of the Spryker architecture, all containers, such as Yves, Gateway, Backoffice, Jenkins, or Glue are isolated from each other and don't share any volume. For more details, refer to [Docker environment infrastructure](/docs/scos/dev/the-docker-sdk/{{site.version}}/docker-environment-infrastructure.html).

For a shared file storage solution, we recommend using S3 buckets. For an illustrative example, see the [Data Import](/docs/ca/dev/configure-data-import-from-an-s3-bucket.html#configure-a-csvreader-based-on-flysystem) feature based on an S3 bucket for file storage.
For more details on the suggested file system, see [Flysystem feature](/docs/dg/dev/backend-development/data-manipulation/data-ingestion/structural-preparations/flysystem.html).

When migrating to SCCOS, make sure to consider these architectural peculiarities.

## Next step

[Implement Security and Performance guidelines](/docs/dg/dev/upgrade-and-migrate/migrate-to-cloud/migrate-to-cloud-implement-security-and-performance-guidelines.html)
