# How2 - Power Automate
1. [adobe bill auto send to specific mail](#adobe-bill-auto-send-to-specific-mail)
2. [create new task element](#create-new-task-element)
3. [ auto tasklist send to mail](#auto-tasklist-send-to-mail)

---

## adobe bill auto send to specific mail

***On new Email***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_office365",
            "operationId": "OnNewEmailV3",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_office365"
        },
        "parameters": {
            "folderPath": "Id::###########################Censored###########################",
            "from": "message@adobe.com",
            "includeAttachments": true,
            "subjectFilter": "Steuerfreie Transaktion mit Adobe",
            "importance": "Any",
            "fetchOnlyWithAttachment": true
        },
        "authentication": "@parameters('$authentication')"
    },
    "description": "incoming mails",
    "metadata": {
        "Id::###########################Censored###########################": "Posteingang",
        "operationMetadataId": "###########################Censored###########################"
    },
    "splitOn": "@triggerOutputs()?['body/value']"
}
```

***Apply to each Control - output from previous setps = Attachments***
***send mail***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_office365",
            "operationId": "SendEmailV2",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_office365"
        },
        "parameters": {
            "emailMessage/To": "censored.accounting@gmail.de",
            "emailMessage/Subject": "Adobe Rechnung",
            "emailMessage/Body": "<p>Adobe Rechnung im Anhang</p>",
            "emailMessage/From": "censored.user@gmail.de",
            "emailMessage/Attachments": [
                {
                    "Name": "@items('take_attachment_and_send_to_RNEG')?['name']",
                    "ContentBytes": "@items('take_attachment_and_send_to_RNEG')?['contentBytes']"
                }
            ]
        },
        "authentication": "@parameters('$authentication')"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***mark as read***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_office365",
            "operationId": "MarkAsRead_V3",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_office365"
        },
        "parameters": {
            "messageId": "@triggerOutputs()?['body/id']",
            "body/isRead": true
        },
        "authentication": "@parameters('$authentication')"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***Apply to each Control - output from previous setps = Attachments***
***create item in tasklist***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_sharepointonline",
            "operationId": "PostItem",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_sharepointonline"
        },
        "parameters": {
            "dataset": "https://companydomain-my.sharepoint.com/personal/user_name_companydomain",
            "table": "###########################Censored###########################",
            "item/field_0": "@items('create_item_in_tasklist')?['lastModifiedDateTime']",
            "item/Title": "User Name",
            "item/field_2": "@items('create_item_in_tasklist')?['lastModifiedDateTime']",
            "item/field_1": "Adobe Rechnung einholen",
            "item/field_4": "Betreff:\"Steuerfreie Transaktion mit Adobe\""
        },
        "authentication": "@parameters('$authentication')"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

**Where can I find items like "item/field_0"?**
List on SharePoint > Gear Symbol | Settings > Columns > choose column > look at url "...7D&Field=field_0"

---

## create new task element

***When a new response is submitted FROM NEW TASK LIST***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_microsoftforms",
            "operationId": "CreateFormWebhook",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_microsoftforms"
        },
        "parameters": {
            "form_id": "###########################Censored###########################"
        },
        "authentication": "@parameters('$authentication')"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***Apply to each Control - output from previous setps = List of response notifications Response Id***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_microsoftforms",
            "operationId": "GetFormResponseById",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_microsoftforms"
        },
        "parameters": {
            "form_id": "###########################Censored###########################",
            "response_id": "@items('Apply_to_each_')?['resourceData/responseId']"
        },
        "authentication": "@parameters('$authentication')"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}

{
    "kind": "ConvertTimeZone",
    "inputs": {
        "baseTime": "@outputs('Get_response_details')?['body/submitDate']",
        "formatString": "G",
        "sourceTimeZone": "UTC-08",
        "destinationTimeZone": "UTC-08"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***Condition - task done?***

***TRUE001L{***
***Condition - routine task?***

***TRUE002L{***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_sharepointonline",
            "operationId": "PostItem",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_sharepointonline"
        },
        "parameters": {
            "dataset": "https://companydomain-my.sharepoint.com/personal/user_name_companydomain",
            "table": "###########################Censored###########################",
            "item/field_0": "@body('Convert_time_zone')",
            "item/Title": "@outputs('Get_response_details')?['body/###########################Censored###########################']",
            "item/field_2": "@body('Convert_time_zone')",
            "item/field_1": "@outputs('Get_response_details')?['body/###########################Censored###########################']",
            "item/field_4": "@outputs('Get_response_details')?['body/###########################Censored###########################']",
            "item/field_5": "@outputs('Get_response_details')?['body/###########################Censored###########################']"
        },
        "authentication": "@parameters('$authentication')"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```
***}TRUE002L***

***FALSE002L{***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_sharepointonline",
            "operationId": "PostItem",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_sharepointonline"
        },
        "parameters": {
            "dataset": "https://companydomain-my.sharepoint.com/personal/user_name_companydomain",
            "table": "###########################Censored###########################",
            "item/field_0": "@body('Convert_time_zone')",
            "item/Title": "@outputs('Get_response_details')?['body/###########################Censored###########################']",
            "item/field_2": "@body('Convert_time_zone')",
            "item/field_1": "@outputs('Get_response_details')?['body/###########################Censored###########################']",
            "item/field_4": "@outputs('Get_response_details')?['body/###########################Censored###########################']",
            "item/field_5": "@outputs('Get_response_details')?['body/###########################Censored###########################']"
        },
        "authentication": "@parameters('$authentication')"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```
***}FALSE002L***
***}TRUE001L***

***FALSE001R{***
***Condition - routine task?***

***TRUE002R{***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_sharepointonline",
            "operationId": "PostItem",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_sharepointonline"
        },
        "parameters": {
            "dataset": "https://companydomain-my.sharepoint.com/personal/user_name_companydomain",
            "table": "###########################Censored###########################",
            "item/field_0": "@body('Convert_time_zone')",
            "item/Title": "@outputs('Get_response_details')?['body/###########################Censored###########################']",
            "item/field_1": "@outputs('Get_response_details')?['body/###########################Censored###########################']",
            "item/field_4": "@outputs('Get_response_details')?['body/###########################Censored###########################']",
            "item/field_5": "@outputs('Get_response_details')?['body/###########################Censored###########################']"
        },
        "authentication": "@parameters('$authentication')"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```
***}TRUE002R***

***FALSE002R{***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_sharepointonline",
            "operationId": "PostItem",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_sharepointonline"
        },
        "parameters": {
            "dataset": "https://companydomain-my.sharepoint.com/personal/user_name_companydomain",
            "table": "###########################Censored###########################",
            "item/field_0": "@body('Convert_time_zone')",
            "item/Title": "@outputs('Get_response_details')?['body/###########################Censored###########################']",
            "item/field_1": "@outputs('Get_response_details')?['body/###########################Censored###########################']",
            "item/field_4": "@outputs('Get_response_details')?['body/###########################Censored###########################']",
            "item/field_5": "@outputs('Get_response_details')?['body/###########################Censored###########################']"
        },
        "authentication": "@parameters('$authentication')"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```
***}FALSE002R***
***}FALSE001R***

---

## auto tasklist send to mail

***Recurrence***
```json
{
    "recurrence": {
        "frequency": "Month",
        "interval": 1,
        "timeZone": "W. Europe Standard Time",
        "startTime": "2022-02-01T00:00:00Z"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***VAR differenceToCurrentDate***
```json
{
    "inputs": {
        "variables": [
            {
                "name": "differenceToCurrentDate",
                "type": "integer",
                "value": -15
            }
        ]
    },
    "description": "Abhängig vom Start des Flows",
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***VAR lastMonth***
```json
{
    "inputs": {
        "variables": [
            {
                "name": "lastMonth",
                "type": "string",
                "value": "@{addDays(utcNow(), variables('differenceToCurrentDate'))}"
            }
        ]
    },
    "description": "addDays(utcNow(), variables('differenceToCurrentDate'))",
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***VAR startOfLastMonthInUTC***
```json
{
    "inputs": {
        "variables": [
            {
                "name": "startOfLastMonthInUTC",
                "type": "string",
                "value": "@{startOfMonth(formatDateTime(variables('lastMonth')))}"
            }
        ]
    },
    "description": "startOfMonth(formatDateTime(variables('lastMonth')))",
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***VAR endOfLastMonthInUTC***
```json
{
    "inputs": {
        "variables": [
            {
                "name": "endOfLastMonthInUTC",
                "type": "string",
                "value": "@{subtractFromTime(addToTime(variables('StartOfLastMonthInUTC'),1,'month'),1,'second')}"
            }
        ]
    },
    "description": "subtractFromTime(addToTime(variables('StartOfLastMonthInUTC'),1,'month'),1,'second')",
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***VAR numberOfLastMonth***
```json
{
    "inputs": {
        "variables": [
            {
                "name": "numberOfLastMonth",
                "type": "string",
                "value": "@{formatDateTime(variables('lastMonth'),'MM')}"
            }
        ]
    },
    "description": "formatDateTime(variables('lastMonth'),'MM')",
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***VAR nameOfLastMonth in german***
```json
{
    "inputs": {
        "variables": [
            {
                "name": "nameOfLastMonth",
                "type": "string",
                "value": "@{if(equals(int(variables('numberOfLastMonth')),12),'Dezember',if(equals(int(variables('numberOfLastMonth')),11),'Nomvember',if(equals(int(variables('numberOfLastMonth')),10),'Oktober',if(equals(int(variables('numberOfLastMonth')),9),'September',if(equals(int(variables('numberOfLastMonth')),8),'August',if(equals(int(variables('numberOfLastMonth')),7),'Juli',if(equals(int(variables('numberOfLastMonth')),6),'Juni',if(equals(int(variables('numberOfLastMonth')),5),'Mai',if(equals(int(variables('numberOfLastMonth')),4),'April',if(equals(int(variables('numberOfLastMonth')),3),'März',if(equals(int(variables('numberOfLastMonth')),2),'Februar',if(equals(int(variables('numberOfLastMonth')),1),'Januar',null))))))))))))}"
            }
        ]
    },
    "description": "if(equals(int(variables('numberOfLastMonth')),12),'Dezember',if(equals(int(variables('numberOfLastMonth')),11),'Nomvember',if(equals(int(variables('numberOfLastMonth')),10),'Oktober',if(equals(int(variables('numberOfLastMonth')),9),'September',if(equ",
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***VAR yearOfLastMonth***
```json
{
    "inputs": {
        "variables": [
            {
                "name": "yearOfLastMonth",
                "type": "string",
                "value": "@{formatDateTime(utcNow(),'yyyy')}"
            }
        ]
    },
    "description": "formatDateTime(utcNow(),'yyyy')",
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***get items from SharePoint list***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_sharepointonline",
            "operationId": "GetItems",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_sharepointonline"
        },
        "parameters": {
            "dataset": "https://companydomain-my.sharepoint.com/personal/user_name_companydomain",
            "table": "###########################Censored###########################",
            "$filter": "(field_2 gt '@{formatDateTime(variables('startOfLastMonthInUTC'),'yyyy-MM-dd')}') and (field_2 lt '@{formatDateTime(variables('endOfLastMonthInUTC'),'yyyy-MM-dd')}')"
        },
        "authentication": "@parameters('$authentication')"
    },
    "description": "formatDateTime(variables('startOfLastMonthInUTC'),'yyyy-MM-dd')",
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***create HTML table***
```json
{
    "inputs": {
        "from": "@outputs('getSpTasklist')?['body/value']",
        "format": "HTML",
        "columns": [
            {
                "header": "Auftraggeber",
                "value": "@item()?['Title']"
            },
            {
                "header": "Begonnen_am",
                "value": "@convertFromUtc(item()?['field_0'], 'W. Europe Standard Time', 'dd.MM.yyyy hh:mm')"
            },
            {
                "header": "Abgeschlossen",
                "value": "@convertFromUtc(item()?['field_2'], 'W. Europe Standard Time', 'dd.MM.yyyy hh:mm')"
            },
            {
                "header": "Aufgabe",
                "value": "@item()?['field_1']"
            }
        ]
    },
    "description": "convertFromUtc(item()?['field_0'], 'W. Europe Standard Time', 'dd.MM.yyyy hh:mm')",
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***change CSS style***
```json
{
    "inputs": "<style>\ntable {\n  border: 1px solid #1C6EA4;\n  background-color: #EEEEEE;\n  width: 100%;\n  text-align: left;\n  border-collapse: collapse;\n}\ntable td, table th {\n  border: 1px solid #AAAAAA;\n  padding: 3px 2px;\n}\ntable tbody td {\n  font-size: 13px;\n}\ntable thead {\n  background: #1C6EA4;\n  border-bottom: 2px solid #444444;\n}\ntable thead th {\n  font-size: 15px;\n  font-weight: bold;\n  color: #FFFFFF;\n  border-left: 2px solid #D0E4F5;\n}\ntable thead th:first-child {\n  border-left: none;\n}\n</style>",
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***send mail to***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_office365",
            "operationId": "SendEmailV2",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_office365"
        },
        "parameters": {
            "emailMessage/To": "censored.userreceiver@gmail.de",
            "emailMessage/Subject": "Übersicht aktueller Arbeitsaufgaben / Output @{variables('nameOfLastMonth')} @{variables('yearOfLastMonth')} / Aufgabennachweis",
            "emailMessage/Body": "<p>Hallo MAILRECEIVERNAME hier meine Tasklist.<br>\n<br>\n@{outputs('changeCssStyle')}@{body('createHtmlTable')}<br>\n<br>\nMit freundlichen Grüßen<br>\nMAILSENDERNAME</p>"
        },
        "authentication": "@parameters('$authentication')"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```

***new task in tasklist***
```json
{
    "inputs": {
        "host": {
            "connectionName": "shared_sharepointonline",
            "operationId": "PostItem",
            "apiId": "/providers/Microsoft.PowerApps/apis/shared_sharepointonline"
        },
        "parameters": {
            "dataset": "https://companydomain-my.sharepoint.com/personal/user_name_companydomain",
            "table": "###########################Censored###########################",
            "view": "###########################Censored###########################",
            "item/field_0": "@utcNow()",
            "item/Title": "MAILRECEIVERNAME",
            "item/field_2": "@utcNow()",
            "item/field_1": "Übersicht aktueller Arbeitsaufgaben / Output @{variables('nameOfLastMonth')} @{variables('yearOfLastMonth')} / Aufgabennachweis"
        },
        "authentication": "@parameters('$authentication')"
    },
    "metadata": {
        "operationMetadataId": "###########################Censored###########################"
    }
}
```