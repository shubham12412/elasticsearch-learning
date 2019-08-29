# elasticsearch-learning

1) https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html

2) https://www.elastic.co/guide/en/elasticsearch/reference/6.7/disk-allocator.html

***cluster.routing.allocation.disk.watermark.flood_stage***
Controls the flood stage watermark. It defaults to 95%, meaning that Elasticsearch enforces a read-only index block (***index.blocks.read_only_allow_delete***) on every index that has one or more shards allocated on the node that has at least one disk exceeding the flood stage. This is a last resort to prevent nodes from running out of disk space. The index block must be released manually once there is enough disk space available to allow indexing operations to continue.


