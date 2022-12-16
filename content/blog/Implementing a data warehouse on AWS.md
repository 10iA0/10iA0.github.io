---
title: Implementing a data warehouse on AWS
date: 2019-05-20 22:13:00
tags: ["AWS"]
series: ["Data Warehouse"]
featured: false
---
<!--more-->

Implementing a data warehouse on AWS needs S3, and Redshif. As I have stored log files in S3, I only need create a Redshift and write ETL scripts to extract data from buckets of S3, transform them, and load them into databases on Redshift.

## Configuration file
`dwh.cfg` contains information of implementing a data warehouse on AWS. Every IAM user have a AWS Access Key and Secret Key while the user was created. Edit the `dwh.cfg` configuration file and fill in the AWS Access Key and Secret Key fields.

## Aws Toolkit

> Boto3 is a Python SDK for programmatically accessing AWS. It enables developers to create, configure, and manage AWS services.

It is nature to use `Boto3` to write a script `aws_toolkit.py` that create, configure, and manage AWS services we need on data warehouse automatically.

> To summarize, resources are higher-level abstractions of AWS services compared to clients. Resources are the recommended pattern to use boto3 as you don’t have to worry about a lot of the underlying details when interacting with AWS services. As a result, code written with Resources tends to be simpler. However, Resources aren’t available for all AWS services. In such cases, there is no other choice but to use a Client instead.

```python
import boto3
import json
import configparser

class aws():
    KEY = None
    SECRET = None

    DWH_CLUSTER_TYPE = None
    DWH_NUM_NODES = None
    DWH_NODE_TYPE = None

    DWH_IAM_ROLE_NAME = None
    DWH_CLUSTER_IDENTIFIER = None

    DWH_DB = None
    DWH_DB_USER = None
    DWH_DB_PASSWORD = None
    DWH_PORT = None

    S3_LOG_DATA = None
    S3_LOG_JSONPATH = None
            
    DWH_IAM_ROLE_ARN = None

    def __init__(self, cfg_file='dwh.cfg'):
        """
        Set the AWS Config parameters with value from dwh.cfg config file
        :param cfg_file: the path of configuration file
        """

        print("Parsing the configuration file...\n")

        config = configparser.ConfigParser()
        with open(cfg_file) as cfg_file:
            config.read_file(cfg_file)

            self.KEY = config.get('AWS', 'KEY')
            self.SECRET = config.get('AWS', 'SECRET')

            self.DWH_CLUSTER_TYPE = config.get("DWH", "DWH_CLUSTER_TYPE")
            self.DWH_NUM_NODES = config.get("DWH", "DWH_NUM_NODES")
            self.DWH_NODE_TYPE = config.get("DWH", "DWH_NODE_TYPE")

            self.DWH_IAM_ROLE_NAME = config.get("DWH", "DWH_IAM_ROLE_NAME")
            self.DWH_CLUSTER_IDENTIFIER = config.get("DWH", "DWH_CLUSTER_IDENTIFIER")

            self.DWH_DB = config.get("CLUSTER", "DB_NAME")
            self.DWH_DB_USER = config.get("CLUSTER", "DB_USER")
            self.DWH_DB_PASSWORD = config.get("CLUSTER", "DB_PASSWORD")
            self.DWH_PORT = config.get("CLUSTER", "DB_PORT")
            
            self.S3_LOG_DATA = config.get('S3', 'LOG_DATA')
            self.S3_LOG_JSONPATH = config.get('S3', 'LOG_JSONPATH')
            
            self.DWH_IAM_ROLE_ARN = config.get("IAM_ROLE", "ARN")

    def client(self, client_name, region):
        """
        Creates an AWS client (specified by the argument) in region (specified by argument)
        :param client_name: The client to be created
        :param region: The region where service has to be created
        :return client: The client for AWS service
        """

        client = boto3.client(client_name, region_name=region, aws_access_key_id=self.KEY, aws_secret_access_key=self.SECRET)

        return client

    def resource(self, resource_name, region):
        """
        Creates an AWS resource (specified by the argument) in region (specified by argument)
        :param resource_name: The resource to be created
        :param region: The region where resource has to be created
        :return resource: The resource for AWS service
        """

        resource = boto3.resource(resource_name, region_name=region, aws_access_key_id=self.KEY, aws_secret_access_key=self.SECRET)

        return resource

    def create_iam_role(self, iam):
        """
        Create the AWS Identity and Access Management (IAM) role. Attach AmazonS3ReadOnlyAccess role policy to the IAM
        specified in argument. Return the IAM role ARN string
        :param iam: Boto3 client for IAM
        :return roleArn: IAM role ARN string
        """

        dwhRole = None
        try:
            print('1.1 Creating a new IAM Role')
            dwhRole = iam.create_role(
                Path='/',
                RoleName=self.DWH_IAM_ROLE_NAME,
                Description="Allows Redshift clusters to call AWS services on your behalf.",
                AssumeRolePolicyDocument=json.dumps(
                    {'Statement': [{'Action': 'sts:AssumeRole',
                                    'Effect': 'Allow',
                                    'Principal': {'Service': 'redshift.amazonaws.com'}}],
                    'Version': '2012-10-17'})
            )
        except Exception as exep:
            print(exep)
            dwhRole = iam.get_role(RoleName=self.DWH_IAM_ROLE_NAME)

        print('1.2 Attaching Policy')
        dwhRole_policy = iam.attach_role_policy(RoleName=self.DWH_IAM_ROLE_NAME, PolicyArn="arn:aws:iam::aws:policy"
                                                                                    "/AmazonS3ReadOnlyAccess")[
                                                'ResponseMetadata']['HTTPStatusCode']

        roleArn = iam.get_role(RoleName=self.DWH_IAM_ROLE_NAME)['Role']['Arn']

        return roleArn

    def create_redshift_cluster(self, redshift, roleArn):
        """
        Create the AWS Redshift cluster
        :param redshift: Boto3 client for the Redshift
        :param roleArn: The ARN string for IAM role
        :return boolean:
        """

        print("2. Starting Redshift cluster creation")
        try:
            response = redshift.create_cluster(
                ClusterType=self.DWH_CLUSTER_TYPE,
                NodeType=self.DWH_NODE_TYPE,
                NumberOfNodes=int(self.DWH_NUM_NODES),

                DBName=self.DWH_DB,
                ClusterIdentifier=self.DWH_CLUSTER_IDENTIFIER,
                MasterUsername=self.DWH_DB_USER,
                MasterUserPassword=self.DWH_DB_PASSWORD,

                IamRoles=[roleArn]
            )

            print("Redshift cluster creation http response status code: ")
            print(response['ResponseMetadata']['HTTPStatusCode'])
            return response['ResponseMetadata']['HTTPStatusCode'] == 200

        except Exception as exep:
            print(exep)
            return False

    def delete_redshift_cluster(self, redshift):
        """
        Delete the AWS Redshift cluster
        :param redshift: Boto3 client for Redshift
        """

        redshift.delete_cluster(ClusterIdentifier=self.DWH_CLUSTER_IDENTIFIER, SkipFinalClusterSnapshot=True)

    def redshift_cluster_status(self, redshift):
        """
        Retrieves the Redshift cluster status
        :param redshift: Boto3 client for Redshift
        :return cluster_status: The cluster status
        """

        cluster_props = redshift.describe_clusters(ClusterIdentifier=self.DWH_CLUSTER_IDENTIFIER)['Clusters'][0]
        cluster_status = cluster_props['ClusterStatus'].lower()

        return cluster_status

    def config_update(self, redshift, cfg_file='dwh.cfg'):
        """
        Write the cluster endpoint and IAM ARN string to the dwh.cfg configuration file
        :param redshift: Boto3 client for Redshift
        """

        print("Writing the cluster endpoint address and IAM Role ARN to the config file...\n")
        cluster_props = redshift.describe_clusters(ClusterIdentifier=self.DWH_CLUSTER_IDENTIFIER)['Clusters'][0]

        config = configparser.ConfigParser()
        with open(cfg_file) as cfg_file:
            config.read_file(cfg_file)

        config.set("CLUSTER", "HOST", cluster_props['Endpoint']['Address'])
        config.set("IAM_ROLE", "ARN", cluster_props['IamRoles'][0]['IamRoleArn'])

        with open(cfg_file, 'w+') as cfg_file:
            config.write(cfg_file)

    def open_redshift_port(ec2, redshift):
        """
        Opens an incoming TCP port to access Redshift cluster endpoint on VPC security group
        :param ec2: Boto3 client for EC2 instance
        :param Redshift: Boto3 client for Redshift
        """

        global DWH_CLUSTER_IDENTIFIER, DWH_PORT
        cluster_props = redshift.describe_clusters(ClusterIdentifier=DWH_CLUSTER_IDENTIFIER)['Clusters'][0]

        try:
            vpc = ec2.Vpc(id=cluster_props['VpcId'])
            all_security_groups = list(vpc.security_groups.all())
            print(all_security_groups)
            defaultSg = all_security_groups[0]
            print(defaultSg)

            defaultSg.authorize_ingress(
                GroupName=defaultSg.group_name,
                CidrIp='0.0.0.0/0',
                IpProtocol='TCP',
                FromPort=int(DWH_PORT),
                ToPort=int(DWH_PORT)
            )
        except Exception as error:
            print(error)
```

## SQL Toolkit
While implementing a data warehouse on AWS, a lots of SQL are needed, which are used to create tables, insert data for ETL.

## Create a Cluster
The script `create_aws_cluster.py` is used to create a cluster on AWS. Run this file on console:

```shell
python create_aws_cluster.py
```

```python
import time
from aws_toolkit import *

def main():
    a = aws()

    ec2 = a.resource('ec2', "us-west-2")
    s3 = a.resource('s3', "us-west-2")
    iam = a.client('iam', "us-west-2")
    redshift = a.client('redshift', "us-west-2")

    roleArn = a.create_iam_role(iam)
    clusterCreationStarted = a.create_redshift_cluster(redshift, roleArn)

    if clusterCreationStarted:
        print("The cluster is being created.")

        while True:
            print("Checking if the cluster is created...")

            if a.redshift_cluster_status(redshift) == 'available':
                a.config_update(redshift)
                a.open_redshift_port(ec2, redshift)
                break
            else:
                print("The cluster is still being created. Please wait.")

            time.sleep(30)
        print("The cluster is created successfully.\n")
        

if __name__ == '__main__':
    main()

```

## Create tables
The script `create_tables.py` is used to create tables on Redshift. Run this file on console:

```shell
python create_table.py
```

```python
import sys
import configparser
import psycopg2
from sql_tookit import create_table_queries, drop_table_queries


def drop_tables(cur, conn):
    """
    Drop all the table in the Redshift cluster
    :param cur: cursor object to database connection
    :param conn: connection object to database
    """
    
    for query in drop_table_queries:
        try:
            cur.execute(query)
            conn.commit()
        except psycopg2.Error as e:
            print(e)
            conn.close()
            sys.exit(0)


def create_tables(cur, conn):
    """
    Create all the tables in the Redshift cluster
    :param cur: cursor object to database connection
    :param conn: connection object to database
    """
    
    for query in create_table_queries:
        try:
            cur.execute(query)
            conn.commit()
        except psycopg2.Error as e:
            print(e)
            conn.close()
            sys.exit(0)


def main():
    """
    Connects to the Redshift cluster and resets the tables.
    """
    
    config = configparser.ConfigParser()
    config.read('dwh.cfg')

    try:
        conn = psycopg2.connect("host={} dbname={} user={} password={} port={}".format(*config['CLUSTER'].values()))
        cur = conn.cursor()
    except Exception as e:
        print(e)

    drop_tables(cur, conn)
    create_tables(cur, conn)

    conn.close()


if __name__ == "__main__":
    main()
```

## Execute ETL
The script `etl.py` is used to execute ETL processing. Run this file on console:

```shell
python etl.py
```

```python
import configparser
import psycopg2
from sql_toolkit import copy_table_queries, insert_table_queries


def load_staging_tables(cur, conn):
    """
    Load data from files stored in S3 to the staging tables.
    """
    
    print("Loading data from JSON files stored in S3 buckets into staging tables")
    
    for query in copy_table_queries:
        cur.execute(query)
        conn.commit()
    
    print("Complete.\n")


def insert_tables(cur, conn):
    """
    Insert data from staging tables into the tables.
    """
    
    print("Inserting data from staging tables into Redshift tables")
    
    for query in insert_table_queries:
        cur.execute(query)
        conn.commit()

    print("Complete.\n")
    

def main():
    """
    Extract song metadata and user activity data from S3, transform it using a staging table, and load it into fact and dimensional tables for analysis
    """
    
    config = configparser.ConfigParser()
    config.read('dwh.cfg')

    conn = psycopg2.connect("host={} dbname={} user={} password={} port={}".format(*config['CLUSTER'].values()))
    cur = conn.cursor()
    
    load_staging_tables(cur, conn)
    insert_tables(cur, conn)

    conn.close()


if __name__ == "__main__":
    main()
```