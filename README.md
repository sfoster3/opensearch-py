
[![Release](https://github.com/opensearch-project/opensearch-py/actions/workflows/unified-release.yml/badge.svg)](https://github.com/opensearch-project/opensearch-py/actions/workflows/unified-release.yml)
[![CI](https://github.com/opensearch-project/opensearch-py/actions/workflows/ci.yml/badge.svg)](https://github.com/opensearch-project/opensearch-py/actions/workflows/ci.yml)
[![Integration](https://github.com/opensearch-project/opensearch-py/actions/workflows/integration.yml/badge.svg)](https://github.com/opensearch-project/opensearch-py/actions/workflows/integration.yml)
[![Chat](https://img.shields.io/badge/chat-on%20forums-blue)](https://discuss.opendistrocommunity.dev/c/clients/)
![PRs welcome!](https://img.shields.io/badge/PRs-welcome!-success)

![OpenSearch logo](https://github.com/opensearch-project/opensearch-py/raw/main/OpenSearch.svg)

OpenSearch Python Client

- [Welcome!](#welcome)
- [Project Resources](#project-resources)
- [Code of Conduct](#code-of-conduct)
- [License](#license)
- [Copyright](#copyright)

## Welcome!

**opensearch-py** is [a community-driven, open source fork](https://aws.amazon.com/blogs/opensource/introducing-opensearch/)
of elasticsearch-py licensed under the [Apache v2.0 License](https://github.com/opensearch-project/opensearch-py/blob/main/LICENSE.txt). 
For more information, see [opensearch.org](https://opensearch.org/).

This is the low-level client. A high-level Python client is in the works, and will be available soon.

## Setup

To add the client to your project, install it using [pip](https://pip.pypa.io/):

```bash
pip install opensearch-py
```

Then import it like any other module:

```python
from opensearchpy import OpenSearch
```

If you prefer to add the client manually or just want to examine the source code, see [opensearch-py on GitHub](https://github.com/opensearch-project/opensearch-py).


## Sample code

```python
from opensearchpy import OpenSearch

host = 'localhost'
port = 9200
auth = ('admin', 'admin') # For testing only. Don't store credentials in code.
ca_certs_path = '/full/path/to/root-ca.pem' # Provide a CA bundle if you use intermediate CAs with your root CA.

# Optional client certificates if you don't want to use HTTP basic authentication.
# client_cert_path = '/full/path/to/client.pem'
# client_key_path = '/full/path/to/client-key.pem'

# Create the client with SSL/TLS enabled, but hostname verification disabled.
client = OpenSearch(
    hosts = [{'host': host, 'port': port}],
    http_compress = True, # enables gzip compression for request bodies
    http_auth = auth,
    # client_cert = client_cert_path,
    # client_key = client_key_path,
    use_ssl = True,
    verify_certs = True,
    ssl_assert_hostname = False,
    ssl_show_warn = False,
    ca_certs = ca_certs_path
)

# Create an index with non-default settings.
index_name = 'python-test-index3'
index_body = {
  'settings': {
    'index': {
      'number_of_shards': 4
    }
  }
}

response = client.indices.create(index_name, body=index_body)
print('\nCreating index:')
print(response)

# Add a document to the index.
document = {
  'title': 'Moneyball',
  'director': 'Bennett Miller',
  'year': '2011'
}
id = '1'

response = client.index(
    index = index_name,
    body = document,
    id = id,
    refresh = True
)

print('\nAdding document:')
print(response)

# Search for the document.
q = 'miller'
query = {
  'size': 5,
  'query': {
    'multi_match': {
      'query': q,
      'fields': ['title^2', 'director']
    }
  }
}

response = client.search(
    body = query,
    index = index_name
)
print('\nSearch results:')
print(response)

# Delete the document.
response = client.delete(
    index = index_name,
    id = id
)

print('\nDeleting document:')
print(response)

# Delete the index.
response = client.indices.delete(
    index = index_name
)

print('\nDeleting index:')
print(response)
```

## Using IAM credentials for authentication

Refer the AWS documentation regarding usage of IAM credentials to sign requests to OpenSearch APIs - [Signing HTTP requests to Amazon OpenSearch Service.](https://docs.aws.amazon.com/opensearch-service/latest/developerguide/request-signing.html#request-signing-python)

Opensearch-py client library also provides an in-house IAM based authentication feature, `AWSV4SignerAuth` that will help users to connect to their opensearch clusters by making use of IAM roles.

#### Pre-requisites to use `AWSV4SignerAuth`
 - Python version 3.6 or above,
 - Install [botocore](https://pypi.org/project/botocore/) using pip

   `pip install botocore`

Here is the sample code that uses `AWSV4SignerAuth` -

```python
from opensearchpy import OpenSearch, RequestsHttpConnection, AWSV4SignerAuth
import boto3

host = '' # cluster endpoint, for example: my-test-domain.us-east-1.es.amazonaws.com
region = 'us-west-2'
credentials = boto3.Session().get_credentials()
auth = AWSV4SignerAuth(credentials, region)
index_name = 'python-test-index3'

client = OpenSearch(
    hosts = [{'host': host, 'port': 443}],
    http_auth = auth,
    use_ssl = True,
    verify_certs = True,
    connection_class = RequestsHttpConnection
)

q = 'miller'
query = {
  'size': 5,
  'query': {
    'multi_match': {
      'query': q,
      'fields': ['title^2', 'director']
    }
  }
}

response = client.search(
    body = query,
    index = index_name
)

print('\nSearch results:')
print(response)
```

## Project Resources

* [Project Website](https://opensearch.org/)
* [Downloads](https://opensearch.org/downloads.html)
* [Documentation](https://opensearch.org/docs/)
* Need help? Try [Forums](https://discuss.opendistrocommunity.dev/)
* [Project Principles](https://opensearch.org/#principles)
* [Contributing to OpenSearch](https://github.com/opensearch-project/opensearch-py/blob/main/CONTRIBUTING.md)
* [Maintainer Responsibilities](https://github.com/opensearch-project/opensearch-py/blob/main/MAINTAINERS.md)
* [Release Management](https://github.com/opensearch-project/opensearch-py/blob/main/RELEASING.md)
* [Admin Responsibilities](https://github.com/opensearch-project/opensearch-py/blob/main/ADMINS.md)
* [Security](https://github.com/opensearch-project/opensearch-py/blob/main/SECURITY.md)

## Code of Conduct

This project has adopted the 
[Amazon Open Source Code of Conduct](https://github.com/opensearch-project/opensearch-py/blob/main/CODE_OF_CONDUCT.md).
For more information see the [Code of Conduct FAQ](https://aws.github.io/code-of-conduct-faq), or contact 
[opensource-codeofconduct@amazon.com](mailto:opensource-codeofconduct@amazon.com) with any additional questions or comments.

## License

This project is licensed under the
[Apache v2.0 License](https://github.com/opensearch-project/opensearch-py/blob/main/LICENSE.txt).

## Copyright

Copyright OpenSearch Contributors. See 
[NOTICE](https://github.com/opensearch-project/opensearch-py/blob/main/NOTICE.txt) for details.
