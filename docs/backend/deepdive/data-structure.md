## Nova's Data Structure

---

Nova's data structure is problematic in its current state.

Consider this example of an actual event profile, plucked from the development server:

```json
{
    "logoutRedirectLink": "",
    "metadata":
    {
        "streamingLocations":
        [
            "non-china",
            "china"
        ]
    },
    "endShow":
    {
        "redirectUrl": null,
        "_state": "Ready",
        "state": "Ready",
        "templateId": "bf1f9e65-f806-4c8f-b3d4-9694b039b329",
        "enabled": true,
        "startAt": 1627315200000,
        "status": "Active",
        "autoStart": false
    },
    "preShow":
    {
        "state": "Ready",
        "templateId": "8671c8e7-48ec-44a6-9daf-31bcd520623b",
        "endAt": 1630339200000,
        "autoEnd": false,
        "enabled": true,
        "status": "Active"
    },
    "status": "Active",
    "name": "20210727_self_reg_genric",
    "state": "Started",
    "passwordRecovery":
    {
        "reset":
        {},
        "state": "Ready",
        "trigger":
        {
            "templateId": "5ca16027-4af7-4426-85b2-93ee43840e56"
        },
        "status": "Active"
    },
    "enabled": true,
    "login":
    {
        "templateId": "91b5ab2f-9e21-4063-8d54-d6f3a77617f9",
        "status": "Active",
        "state": "Ready"
    },
    "startAt": 1627315200000,
    "error":
    {
        "templateId": "e085da8a-d1fe-4ede-a52e-938948dc63ae",
        "status": "Active",
        "state": "Ready"
    },
    "eventId": "ce34ce5a-9f65-49f0-bff8-b669c248f11d",
    "authPreset": "SELF_EMAIL_GENERIC",
    "location": "",
    "emailFrom": null,
    "eventCode": "20210702_self_reg_genric",
    "createdAt": 1627366465942,
    "locales":
    [
        "en-US",
        "zh-HK"
    ],
    "channelId": "fdfe9f3d-1e04-415b-8136-e6b4e2020f65",
    "updatedAt": 1627366490633,
    "endAt": 1630339200000,
    "eventType": "LiveStreaming",
    "main":
    {
        "templateId": "c78005ed-8d5a-4721-8fee-73d3e97ad3da",
        "status": "Active",
        "state": "Ready"
    },
    "description": "",
    "registration":
    {
        "endWithShow": false,
        "accessTokenTtl": 86400000,
        "formFields":
        [
            {
                "preset": true,
                "type": "email",
                "value": "E-mail",
                "key": "email",
                "required": true
            }
        ],
        "templateId": "a66ada2d-e2c6-465c-9185-a7f3df2ed8c0",
        "endAt": 1630339200000,
        "passcode": "000000"
    },
    "remarks": ""
}
```
