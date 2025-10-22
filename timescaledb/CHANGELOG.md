# Changelog since v5.4.1
- Update backup_exclude path to avoid wildcard usage for better clarity 
- Refactor pre-backup script to ensure file creation occurs before SQL dump and maintain error handling consistency 
- Change return statements to exit in backup scripts for consistent error handling 
