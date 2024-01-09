# 下面是上傳圖片的程式，如果需要更進階的，可以問問ChatGPT
``` python
import boto3
import json
import base64
from botocore.exceptions import ClientError

def lambda_handler(event, context):
    print('event:', event)
    s3 = boto3.client('s3')
    bucket_name = '填你的s3 bucket_name'

    try:
        # 從事件中提取文件名和 MIME 類型
        file_name = event['headers']['file-name']
        mime_type = event['headers']['content-type']

        # 根據 MIME 類型決定如何處理文件內容
        if mime_type == 'text/plain':
            # 直接處理文本內容
            file_content = event['body']
        elif event.get('isBase64Encoded', False):
            # 處理 Base64 編碼的內容
            file_content = base64.b64decode(event['body'])
        else:
            # 其他類型，暫不處理
            return {
                'statusCode': 400,
                'body': json.dumps('Unsupported file type')
            }

        # 上傳文件到 S3
        s3.put_object(Bucket=bucket_name, Key=file_name, Body=file_content, ContentType=mime_type)

        return {
            'statusCode': 200,
            'body': json.dumps('File uploaded successfully')
        }
    except ClientError as e:
        print(e)
        return {
            'statusCode': 500,
            'body': json.dumps('Error uploading file')
        }


```
