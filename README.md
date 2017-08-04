# MediaHaven Update Service
This service updates fragments in MediaHaven based on their fragment id. Updates are sequentially and they wait for the object to actually be updated before responding. The service will check whether the fragment was updated by checking the `lastModifiedDate` field.

## Usage
Run this project with the Mule Runtime and fill in the properties.

On RabbitMQ, publish a message in the following format. You can use both cp_name or cp_id:
### Request
```
{
    "correlation_id": "abc123",
    "fragment_id": "9a3cb699a3834160bde85d74fc18b02a60db8cef86c14219b94440fe6c7e82c6126ea398274e44b0be193d76aacec50",
    "cp_name": "someorganisation",
    "data": "<MediaHAVEN_external_metadata><MDProperties><CP_id>OR-rf5kf25</CP_id></MDProperties></MediaHAVEN_external_metadata>"
}
```

```
 {
     "correlation_id": "abc123",
     "fragment_id": "9a3cb699a3834160bde85d74fc18b02a60db8cef86c14219b94440fe6c7e82c6126ea398274e44b0be193d76aacec50",
     "cp_id": "OR-ABCDEFG",
     "data": "<MediaHAVEN_external_metadata><MDProperties><CP_id>OR-ABCDEFG</CP_id></MDProperties></MediaHAVEN_external_metadata>"
 }
 ```

The service will respond on the response queue with a message that looks like the following:
### Response: success
```
{
	"correlation_id": "abc123",
	"fragment_id": "9a3cb699a3834160bde85d74fc18b02a60db8cef86c14219b94440fe6c7e82c6126ea398274e44b0be193d76aacec50",
	"outcome": "success",
	"message": ""
}
```

### Response: failed
```
{
	"correlation_id": "abc123",
	"fragment_id": "9a3cb699a3834160bde85d74fc18b02a60db8cef86c14219b94440fe6c7e82c6126ea398274e44b0be193d76aacec50",
	"outcome": "failed",
	"message": "Failed to update fragment"
}
```

## Notes
Since the Mule AMQP connector refuses to create queues, this service uses the RabbitMQ REST API to declare the queue before publishing to it.
