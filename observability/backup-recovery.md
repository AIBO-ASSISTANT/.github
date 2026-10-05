# Backup and Disaster Recovery Strategy

## Overview

AIBO Assistant relies on a unified single-datastore architecture centered on MongoDB 7.0 Community replica set (`rs0`) alongside an ephemeral Redis 7 cache.

This document defines the backup schedules, disaster recovery procedures, and Recovery Objectives (RPO/RTO) for production deployments.

---

## Recovery Objectives

| Metric | Target | Description |
| --- | --- | --- |
| **Recovery Point Objective (RPO)** | **< 15 minutes** | Maximum allowable data loss during a catastrophic disaster. Achieved via continuous MongoDB oplog streaming and scheduled snapshot dumps. |
| **Recovery Time Objective (RTO)** | **< 30 minutes** | Target time elapsed between declaring disaster recovery and restoring full operational serving capacity. |

---

## Datastore Backup Policies

### 1. MongoDB (`rs0` Replica Set)
- **Authoritative Data**: Users, Tasks, Projects, Columns, Schedules, Chat History, Confirmation Tokens, Audit Logs.
- **Backup Mechanism**:
  - Daily full logical backup via `mongodump --oplog --gzip` against secondary replica set members to avoid impacting primary write throughput.
  - Continuous oplog archiving for Point-In-Time Recovery (PITR).
- **Encryption & Storage**: Backup archives must be encrypted at rest (AES-256) and synced to dedicated, immutable cloud object storage (e.g., AWS S3 with Object Lock or GCS Bucket Lock).
- **Retention**:
  - Daily backups: 30 days retention.
  - Weekly snapshots: 12 weeks retention.
  - Monthly archives: 12 months retention.

### 2. Redis 7 (Cache & Rate Limiting)
- **Data Class**: Ephemeral sessions, token-bucket counters, cached engine responses.
- **Backup Mechanism**: Periodic RDB snapshots (`BGSAVE`) for warm cache re-hydration during container restarts.
- **Disaster Tolerance**: Redis is designated non-authoritative. In the event of catastrophic Redis data loss, the application boots gracefully with empty caches; rate limits reset, and user sessions re-authenticate via valid JWT cookies.

> [!IMPORTANT]
> **Zero PostgreSQL Footprint**: All relational project management models have been unified into MongoDB. Operators do not need to configure, backup, or synchronize PostgreSQL instances.

---

## Disaster Recovery Drill & Verification

1. **Quarterly Restoration Drills**: Production backups must be restored to an isolated staging replica set quarterly to verify backup file integrity and measure actual RTO.
2. **Automated Verification**: Daily backup jobs must verify archive checksums and attempt test restoration of database indexes.
3. **Emergency Restore Command**:
   ```bash
   mongorestore --drop --oplogReplay --gzip --archive=/backups/aibo_latest.dump.gz
   ```
