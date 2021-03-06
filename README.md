# Databricks ODBC Connection to Azure SQL Database with Azure AD User Access Token

This code repository is showing how to make ODBC connection from [Azure Databricks](https://docs.microsoft.com/en-us/azure/databricks/scenarios/what-is-azure-databricks) to [Azure SQL Database](https://docs.microsoft.com/en-in/azure/azure-sql/database/sql-database-paas-overview) with Azure AD user access token. Databricks doesn’t supported for Azure AD user passthrough authentication to Azure SQL Database and [only supported for Azure Data Lake Storage (ADLS)](https://docs.microsoft.com/en-us/azure/databricks/security/credential-passthrough/adls-passthrough).  Due to this limitation, we’re only able to use Azure AD Service Principle (or primitive SQL ID) to make authentication with Azure SQL Database. And we’re also required to setup (Databricks Secret Scope [maybe also using Azure Key Vault-backed scope in advanced scenario)](https://docs.microsoft.com/en-us/azure/databricks/security/secrets/secret-scopes) with corresponding [access control](https://docs.microsoft.com/en-us/azure/databricks/security/access-control/secret-acl) to save the Azure AD Service Principle secrets. This make lots of administration and operation overhead on Databricks Secret Scope. And Azure AD Service Principle based authentication is also hard to trace/audit database access as Secret Scope can be shared.

To make our life easier, I’m trying to make interactive authentication with Azure AD from Databricks notebook and obtain the access token. And then use pyodbc to make token-based authentication with Azure SQL Database.

1.	Analyst use browser to interact Azure Databricks with Notebook.
2.	Initiate [interactive authentication (with device code)](https://docs.microsoft.com/en-us/samples/azure-samples/data-lake-analytics-python-auth-options/authenticating-your-python-application-against-azure-active-directory/) from Notebook. Open up additional tab from browser ([https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)) to perform interactive authentication with Azure AD.
3.	After login successful, Notebook user session will get the user access token from Azure AD.
4.	Leverage collected access token to perform token-based authentication with Azure SQL Database via [pyodbc library](https://pypi.org/project/pyodbc/) and [MS-SQL driver](https://www.microsoft.com/en-us/download/details.aspx?id=56567), load SQL query into Panda dataframe, then covert Panda dataframe into Spark dataframe.

![diagram1](https://github.com/easonlai/Databricks_ODBC_Connection_to_Azure_SQL_DB_with_Azure_AD_User_Access_Token/blob/main/git-images/diagram1.PNG)

All data in this code repository is coming from [classic dataset of diabetes classification](https://raw.githubusercontent.com/jbrownlee/Datasets/master/pima-indians-diabetes.data.csv).

Enjoy!