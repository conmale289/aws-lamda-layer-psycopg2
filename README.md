# AWS Lambda Layer - psycopg2 for Python 3.12

A pre-built AWS Lambda Layer containing psycopg2-binary compiled for Python 3.12 runtime. This layer allows you to use PostgreSQL database connections in your AWS Lambda functions without needing to compile or package psycopg2 yourself.

## 📋 Overview

psycopg2 is a PostgreSQL database adapter for Python.  Due to its C dependencies, it requires compilation for the specific Lambda runtime environment. This layer provides a ready-to-use version that's compatible with AWS Lambda's Python 3.12 runtime.

## 🚀 Quick Start

### 1. Download the Layer

Download the pre-built layer package: 
- [psycopg2-26613a87-e6c7-4af6-8b60-6da4c77aa65f.zip](https://github.com/conmale289/aws-lamda-layer-psycopg2/blob/main/psycopg2-26613a87-e6c7-4af6-8b60-6da4c77aa65f.zip)

### 2. Create Lambda Layer

#### Using AWS Console
1. Go to AWS Lambda Console
2. Navigate to **Layers** in the left sidebar
3. Click **Create layer**
4. Provide a name (e.g., `psycopg2-python312`)
5. Upload the `.zip` file
6. Select compatible runtime:  **Python 3.12**
7. Click **Create**

#### Using AWS CLI
```bash
aws lambda publish-layer-version \
    --layer-name psycopg2-python312 \
    --description "psycopg2-binary for Python 3.12" \
    --zip-file fileb://psycopg2-26613a87-e6c7-4af6-8b60-6da4c77aa65f.zip \
    --compatible-runtimes python3.12
```

### 3. Attach Layer to Your Lambda Function

#### Using AWS Console
1. Open your Lambda function
2. Scroll to **Layers** section
3. Click **Add a layer**
4. Select **Custom layers**
5. Choose your psycopg2 layer and version
6. Click **Add**

#### Using AWS CLI
```bash
aws lambda update-function-configuration \
    --function-name your-function-name \
    --layers arn:aws:lambda:region:account-id:layer:psycopg2-python312:1
```

## 💻 Usage Example

```python
import psycopg2
import os

def lambda_handler(event, context):
    # Database connection parameters
    conn = psycopg2.connect(
        host=os.environ['DB_HOST'],
        database=os.environ['DB_NAME'],
        user=os.environ['DB_USER'],
        password=os.environ['DB_PASSWORD'],
        port=os.environ. get('DB_PORT', 5432)
    )
    
    try:
        cursor = conn.cursor()
        cursor.execute("SELECT version();")
        db_version = cursor.fetchone()
        
        return {
            'statusCode': 200,
            'body': f'PostgreSQL version: {db_version[0]}'
        }
    except Exception as e:
        return {
            'statusCode': 500,
            'body': f'Error: {str(e)}'
        }
    finally: 
        cursor.close()
        conn.close()
```

## 🔧 Building Your Own Layer (Optional)

If you want to rebuild the layer yourself:

```bash
# Create a directory structure
mkdir -p python/lib/python3.12/site-packages

# Install psycopg2-binary using Docker (matching Lambda environment)
docker run -v "$PWD":/var/task "public.ecr.aws/lambda/python:3.12" \
    /bin/sh -c "pip install -t /var/task/python/lib/python3.12/site-packages psycopg2-binary"

# Create the zip file
zip -r psycopg2-layer.zip python
```

## 📦 What's Included

- **psycopg2-binary**: Full PostgreSQL adapter with pre-compiled C extensions
- Compatible with AWS Lambda Python 3.12 runtime
- All necessary dependencies included

## ⚙️ Requirements

- AWS Lambda runtime:  Python 3.12
- PostgreSQL database (any version supported by psycopg2)

## 🔒 Security Considerations

- Store database credentials in AWS Secrets Manager or Systems Manager Parameter Store
- Use environment variables for configuration
- Enable VPC configuration for Lambda if your database is in a VPC
- Use IAM roles for Lambda execution permissions

## 📝 Environment Variables

Set these environment variables in your Lambda function configuration:

- `DB_HOST`: PostgreSQL host endpoint
- `DB_NAME`: Database name
- `DB_USER`: Database username
- `DB_PASSWORD`: Database password (consider using Secrets Manager)
- `DB_PORT`: Database port (default: 5432)

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

## 🐛 Issues

If you encounter any problems or have suggestions, please [open an issue](https://github.com/conmale289/aws-lamda-layer-psycopg2/issues).

## 📚 Additional Resources

- [psycopg2 Documentation](https://www.psycopg.org/docs/)
- [AWS Lambda Layers Documentation](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html)
- [AWS Lambda Python Runtime](https://docs.aws.amazon.com/lambda/latest/dg/lambda-python. html)
