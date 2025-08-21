abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ mkdir log_analysis_project
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ cd log_analysis_project
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~/log_analysis_project$ mkdir raw_logs processed_logs delivery_package
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~/log_analysis_project$ echo "[2025-06-17 15:10] INFO: User 'admin' logged in." > raw_logs/app_1.log
echo "[2025-06-17 15:11] ERROR: Failed to connect to database. Service unavailable." >> raw_logs/app_1.log
echo "[2025-06-17 15:12] INFO: Server startup complete." > raw_logs/server.log
echo "[2025-06-17 15:13] WARN: Disk space is running low." >> raw_logs/server.log
echo "[2025-06-17 15:14] FATAL_ERROR: Authentication service failed." > raw_logs/auth_service.txt
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~/log_analysis_project$ find raw_logs/ -type f
raw_logs/auth_service.txt
raw_logs/app_1.log
raw_logs/server.log
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~/log_analysis_project$ grep -r "ERROR" raw_logs/
raw_logs/auth_service.txt:[2025-06-17 15:14] FATAL_ERROR: Authentication service failed.
raw_logs/app_1.log:[2025-06-17 15:11] ERROR: Failed to connect to database. Service unavailable.
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~/log_analysis_project$ grep -r "ERROR" raw_logs/ > processed_logs/error_summary.txt
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~/log_analysis_project$ cat processed_logs/error_summary.txt
raw_logs/auth_service.txt:[2025-06-17 15:14] FATAL_ERROR: Authentication service failed.
raw_logs/app_1.log:[2025-06-17 15:11] ERROR: Failed to connect to database. Service unavailable.
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~/log_analysis_project$ tar -czvf delivery_package/full_analysis.tar.gz raw_logs/ processed_logs/
raw_logs/
raw_logs/auth_service.txt
raw_logs/app_1.log
raw_logs/server.log
processed_logs/
processed_logs/error_summary.txt
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~/log_analysis_project$ zip delivery_package/error_summary.zip processed_logs/error_summary.txt
  adding: processed_logs/error_summary.txt (deflated 25%)
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~/log_analysis_project$ ls delivery_package
error_summary.zip  full_analysis.tar.gz
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~/log_analysis_project$ 
