Instalación de Python:
En Amazon Linux: sudo yum -y update
Para Ubuntu Server: sudo apt update
En Amazon Linux: sudo yum -y install python3
Para Ubuntu Server: sudo apt-get install python3

Ejecute el código:

Instalación y configuración de AWS SDK for Python (Boto3):
Instalar pip: python -m pip --version 
curl -O https://bootstrap.pypa.io/get-pip.py # Get the install script.
sudo python36 get-pip.py                     # Install pip for Python 3.6.
python -m pip --version                      # Verify pip is installed.
rm get-pip.py                                # Delete the install script.
Instalar la AWS SDK for Python (Boto3):
sudo python36 -m pip install boto3  # Install boto3 for Python 3.6.
python -m pip show boto3            # Verify boto3 is installed for the current version of Python.

AddAWSCódigo del SDK:
import sys
import boto3
from botocore.exceptions import ClientError


def get_s3(region=None):
    """
    Get a Boto 3 Amazon S3 resource with a specific AWS Region or with your
    default AWS Region.
    """
    return boto3.resource('s3', region_name=region) if region else boto3.resource('s3')


def list_my_buckets(s3):
    print('Buckets:\n\t', *[b.name for b in s3.buckets.all()], sep="\n\t")


def create_and_delete_my_bucket(bucket_name, region, keep_bucket):
    s3 = get_s3(region)

    list_my_buckets(s3)

    try:
        print('\nCreating new bucket:', bucket_name)
        bucket = s3.create_bucket(
            Bucket=bucket_name,
            CreateBucketConfiguration={
                'LocationConstraint': region
            }
        )
    except ClientError as e:
        print(e)
        sys.exit('Exiting the script because bucket creation failed.')

    bucket.wait_until_exists()
    list_my_buckets(s3)

    if not keep_bucket:
        print('\nDeleting bucket:', bucket.name)
        bucket.delete()

        bucket.wait_until_not_exists()
        list_my_buckets(s3)
    else:
        print('\nKeeping bucket:', bucket.name)


def main():
    import argparse

    parser = argparse.ArgumentParser()
    parser.add_argument('bucket_name', help='The name of the bucket to create.')
    parser.add_argument('region', help='The region in which to create your bucket.')
    parser.add_argument('--keep_bucket', help='Keeps the created bucket. When not '
                                              'specified, the bucket is deleted '
                                              'at the end of the demo.',
                        action='store_true')

    args = parser.parse_args()

    create_and_delete_my_bucket(args.bucket_name, args.region, args.keep_bucket)


if __name__ == '__main__':
    main()

Ejecute laAWSCódigo del SDK:
introduzca s3.py my-test-bucket us-west-2, donde my-test-bucket es el nombre del bucket que va a crear y us-west-2 es el ID de la región de AWS en la que se ha 
creado el bucket. De forma predeterminada, el bucket se elimina antes de que el script salga. Para conservar el bucket, añada --keep_bucket al comando.

Elija Run (Ejecutar) y compare los resultados:

Buckets:

        a-pre-existing-bucket

Creating new bucket: my-test-bucket
Buckets:

        a-pre-existing-bucket
        my-test-bucket

Deleting bucket: my-test-bucket
Buckets:

        a-pre-existing-bucket
  
Eliminar recursos:
Para evitar cargos continuos a tuAWSUna vez que finalice este tutorial, elimine elAWS Cloud9Medio ambiente. Para obtener instrucciones, 
