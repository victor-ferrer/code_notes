# SQL Server on Docker

General Instructions:
https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker

How to solve "2017-GA: The transaction log for database 'master' is full due to 'NOTHING'. #180" error:
https://github.com/Microsoft/mssql-docker/issues/180#issuecomment-371965952

```
sudo docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' \
				-p 1401:1433 --name sql1 -v /data/ms-sql-docker-data:/var/opt/mssql \
				 -d microsoft/mssql-server-linux:2017-latest
```
 