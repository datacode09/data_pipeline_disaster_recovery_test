
# Disaster Recovery Exercise for Data Pipelines

This document outlines the steps for conducting a disaster recovery exercise for data pipelines maintained in Linux VMs, ingesting data from SQL Server, processing it on a Cloudera Hadoop Data Lake, and writing it into HDFS.

## Steps to Conduct Disaster Recovery Exercise

### 1. Backup SQL Server Data
Ensure regular backups of the SQL Server database.
```sql
BACKUP DATABASE [YourDatabaseName] 
TO DISK = 'C:\Backup\YourDatabaseName.bak'
WITH FORMAT, MEDIANAME = 'SQLServerBackups';
```

### 2. Export Metadata and Configuration
Export pipeline configurations and metadata.
```bash
scp user@yourserver:/path/to/pipeline/configs/* /local/backup/configs/
```

### 3. HDFS Data Backup
Create a backup of critical HDFS data.
```bash
hdfs dfs -cp /path/to/data /backup/path/to/data
```

### 4. Simulate Failure
Simulate a failure by stopping critical services.
```bash
sudo systemctl stop cloudera-scm-agent
sudo systemctl stop hadoop-hdfs-namenode
```

### 5. Restore SQL Server Database
Restore the SQL Server database from the backup.
```sql
RESTORE DATABASE [YourDatabaseName] 
FROM DISK = 'C:\Backup\YourDatabaseName.bak'
WITH REPLACE;
```

### 6. Restore HDFS Data
Restore HDFS data from the backup location.
```bash
hdfs dfs -cp /backup/path/to/data /path/to/data
```

### 7. Restore Metadata and Configuration
Copy the pipeline configuration files back to their original location.
```bash
scp /local/backup/configs/* user@yourserver:/path/to/pipeline/configs/
```

### 8. Restart Services
Restart the services that were stopped.
```bash
sudo systemctl start hadoop-hdfs-namenode
sudo systemctl start cloudera-scm-agent
```

### 9. Validate Data Integrity
Run scripts to validate the integrity of the restored data.
```python
import hashlib

def check_data_integrity(file_path, original_hash):
    with open(file_path, 'rb') as f:
        data = f.read()
        if hashlib.md5(data).hexdigest() == original_hash:
            print("Data integrity check passed.")
        else:
            print("Data integrity check failed.")
```

### 10. Monitor and Document
Monitor the system to ensure it’s functioning correctly and document the entire recovery process for future reference.
```bash
tail -f /var/log/hadoop-hdfs/hadoop-hdfs-namenode.log
```

## Notes
1. **Backups**: Regularly schedule backups for SQL Server, HDFS data, and pipeline configurations.
2. **Automate**: Automate the disaster recovery process using scripts and cron jobs.
3. **Testing**: Regularly test your disaster recovery plan to ensure its effectiveness.
4. **Documentation**: Keep detailed documentation of each step and update it as necessary.

By following these steps, you can ensure that your data pipelines can recover effectively from a disaster. If you have any questions or need further assistance, please feel free to open an issue in this repository.

---

**Maintainers**: [Your Team Name]

**Contact**: [Your Contact Information]
