# Language detection

Detects the language of the source text. The response is returned as the language code.

## Request {#request}

```
POST https://translate.api.cloud.yandex.net/translate/v1/detect
```

### Parameters in the request body

All parameters must be URL-encoded. The maximum size of the POST request body is 30 KB.

| Parameter | Description |
| ----- | ----- |
| `text` | Required parameter.<br/>UTF-8 encoded text to detect the language of. |
| `hint` | List of most probable languages (they will be given priority when detecting the language). The list items are comma-separated. |
| `folderId` | Required parameter.<br/>ID of your folder.<br/>For more information about how to find the folder ID, see the section [Authorization in the API](../concepts/auth.md). |

## Response {#response}

The response is returned in JSON format.

```json
{
    "language": <code of the language the text is in>
}
```

## Examples {#examples}

### Sample request

```httpget
export FOLDER_ID=<folder id>
export TOKEN=<IAM-token>
curl -X POST \
     -H "Authorization: Bearer ${TOKEN}" \
     -d "folderId=${FOLDER_ID}" \
     --data-urlencode "text=привет мир" \
     "https://translate.api.cloud.yandex.net/translate/v1/detect"
```

### Response example

The response is returned in JSON format.

```json
{
    "language": "ru"
}
```
