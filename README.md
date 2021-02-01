# aws_auth

[![CI](https://github.com/jakub-bacic/aws_auth/workflows/CI/badge.svg?branch=master)](https://github.com/jakub-bacic/aws_auth/actions?query=workflow%3ACI+branch%3Amaster) 
[![codecov](https://codecov.io/gh/jakub-bacic/aws_auth/branch/master/graph/badge.svg?token=AJ39TH4ESN)](https://codecov.io/gh/jakub-bacic/aws_auth)
[![Pub](https://img.shields.io/pub/v/aws_auth.svg?style=flat-square)](https://pub.dartlang.org/packages/aws_auth)

A low-level library for signing AWS API requests in Dart.

## Using

Create `AWSCredentials` object with your AWS secrets:

```dart
final credentials = AWSCredentials(
  'AWS_ACCESS_KEY_ID',
  'AWS_SECRET_ACCESS_KEY',
  sessionToken: 'AWS_SESSION_TOKEN',  // this is optional
);
```

Initialize `AWS4Signer` for the given region and service:

```dart
final signer = AWS4Signer(credentials, 'eu-central-1', 'sts');
```

Create `AWSRequest` object and pass it to `sign` or `presign` method of the 
signer (see examples below). Signer will modify request in place and add
required headers or query parameters (in case of presigning) with authentication
information.

### POST request with an authorization header

```dart
final req = AWSRequest.formData(
  'https://sts.eu-central-1.amazonaws.com/',
  body: {
    'Action': 'GetCallerIdentity',
    'Version': '2011-06-15',
  },
);
signer.sign(req);

print(req.headers['Authorization'])
print(req.headers['X-Amz-Date'])
```

### GET request with authentication information in the query string (presigned url)

```dart
final req = AWSRequest(
  'https://sts.eu-central-1.amazonaws.com/',
  queryParameters: {
    'Action': 'GetCallerIdentity',
    'Version': '2011-06-15',
  },
);
signer.presign(req);

print(req.url);
```