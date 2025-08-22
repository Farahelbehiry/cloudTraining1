# 🌐 Real-Time Translation Lambda

This project is an **AWS Lambda function** that provides text translation using the **Amazon Translate** service.  
It’s designed to be used with **API Gateway**, so you can send HTTP requests with text and get back the translated text.

---

## 📌 Features
- Detects the **source language automatically** (`auto`).
- Translates text into a **target language** (default: Spanish `es`).
- Returns clean JSON responses.
- Handles errors gracefully with proper HTTP status codes.

---

## 📂 Project Structure
.
├── lambda_function.py # Main Lambda code

python
Copy
Edit

---

## 🛠️ Code Overview

```python
import json
import boto3

translate = boto3.client('translate')

def lambda_handler(event, context):
    try:
        print("Received event:", event)

        # Parse request body
        body = json.loads(event.get('body', '{}'))
        text = body.get('text', '')
        target_lang = body.get('target', 'es')  # default to Spanish

        # Handle missing text
        if not text:
            return {
                'statusCode': 400,
                'body': json.dumps({'error': 'Missing "text" in request'})
            }

        # Call AWS Translate
        result = translate.translate_text(
            Text=text,
            SourceLanguageCode='auto',
            TargetLanguageCode=target_lang
        )

        return {
            'statusCode': 200,
            'body': json.dumps({'translatedText': result['TranslatedText']})
        }

    except Exception as e:
        print("Error:", str(e))
        return {
            'statusCode': 500,
            'body': json.dumps({'error': str(e)})
        }
🚀 How It Works
Deploy the Lambda function in AWS.

Connect it to API Gateway (HTTP API or REST API).

Send a POST request with JSON input:

json
Copy
Edit
{
  "text": "Hello world",
  "target": "fr"
}
Get the response:

json
Copy
Edit
{
  "translatedText": "Bonjour le monde"
}
📡 Example cURL Command
bash
Copy
Edit
curl -X POST "https://<your-api-id>.execute-api.<region>.amazonaws.com/prod" \
-H "Content-Type: application/json" \
-d '{"text": "How are you?", "target": "de"}'
✅ Response:

json
Copy
Edit
{
  "translatedText": "Wie geht es dir?"
}
⚠️ Error Handling
If text is missing → 400 Bad Request

json
Copy
Edit
{ "error": "Missing \"text\" in request" }
If AWS Translate or Lambda fails → 500 Internal Server Error

json
Copy
Edit
{ "error": "Detailed error message" }
🔑 IAM Permissions
Your Lambda execution role must have the following policies:

AmazonTranslateFullAccess

AWSLambdaBasicExecutionRole (for CloudWatch logs)

📝 Notes
Default translation target is Spanish (es).

You can change the default language in the code by editing:

python
Copy
Edit
target_lang = body.get('target', 'es')



