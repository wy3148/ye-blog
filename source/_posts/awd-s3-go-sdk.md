---
title: Go sdk for amazon AWs
date: 2017-09-11 22:01:27
tags: aws-go-sdk
---



##get the aws-go sdk
cd $GOPATH/src

```

go get github.com/aws/aws-sdk-go
```


##get the asw access key and secret

get the access key and secret
reference:
http://docs.aws.amazon.com/general/latest/gr/aws-access-keys-best-practices.html

get the data center region information
aws-sdk go requires region information of the data center 
http://docs.aws.amazon.com/general/latest/gr/rande.html

```

export AWS_ACCESS_KEY_ID=your access key
export AWS_SECRET_ACCESS_KEY=your access secret
export AWS_REGION=ap-southeast-2
```


sample code
 
```java

package main

import (
	"context"
	"flag"
	"fmt"
	"os"
	"time"

	"github.com/aws/aws-sdk-go/aws"
	"github.com/aws/aws-sdk-go/aws/awserr"
	"github.com/aws/aws-sdk-go/aws/request"
	"github.com/aws/aws-sdk-go/aws/session"
	"github.com/aws/aws-sdk-go/service/s3"
)

// Uploads a file to S3 given a bucket and object key. Also takes a duration
// value to terminate the update if it doesn't complete within that time.
//
// The AWS Region needs to be provided in the AWS shared config or on the
// environment variable as `AWS_REGION`. Credentials also must be provided
// Will default to shared config file, but can load from environment if provided.
//
// Usage:
//   # Upload myfile.txt to myBucket/myKey. Must complete within 10 minutes or will fail
//   go run withContext.go -b mybucket -k myKey -d 10m < myfile.txt
func main() {
	var bucket, key string
	var timeout time.Duration

	flag.StringVar(&bucket, "b", "", "Bucket name.")
	flag.StringVar(&key, "k", "", "Object key name.")
	flag.DurationVar(&timeout, "d", 0, "Upload timeout.")
	flag.Parse()

	// All clients require a Session. The Session provides the client with
	// shared configuration such as region, endpoint, and credentials. A
	// Session should be shared where possible to take advantage of
	// configuration and credential caching. See the session package for
	// more information.
	sess := session.Must(session.NewSession())

	// Create a new instance of the service's client with a Session.
	// Optional aws.Config values can also be provided as variadic arguments
	// to the New function. This option allows you to provide service
	// specific configuration.
	svc := s3.New(sess)

	// Create a context with a timeout that will abort the upload if it takes
	// more than the passed in timeout.
	ctx := context.Background()
	var cancelFn func()
	if timeout > 0 {
		ctx, cancelFn = context.WithTimeout(ctx, timeout)
	}
	// Ensure the context is canceled to prevent leaking.
	// See context package for more information, https://golang.org/pkg/context/
	defer cancelFn()

	// Uploads the object to S3. The Context will interrupt the request if the
	// timeout expires.

	inputObj := &s3.PutObjectInput{
		Bucket: aws.String(bucket),
		Key:    aws.String(key),
		Body:   os.Stdin,
	}

	inputObj.SetACL(s3.BucketCannedACLPublicRead)

	_, err := svc.PutObjectWithContext(ctx, inputObj)
	if err != nil {
		if aerr, ok := err.(awserr.Error); ok && aerr.Code() == request.CanceledErrorCode {
			// If the SDK can determine the request or retry delay was canceled
			// by a context the CanceledErrorCode error code will be returned.
			fmt.Fprintf(os.Stderr, "upload canceled due to timeout, %v\n", err)
		} else {
			fmt.Fprintf(os.Stderr, "failed to upload object, %v\n", err)
		}
		os.Exit(1)
	}

	fmt.Printf("successfully uploaded file to %s/%s\n", bucket, key)
}

```


so run the main.go file to upload the local 'myfile.txt' file
```

go run main.go -b mybucket -k myKey -d 10m < myfile.txt
```

##how to change 'ACL' (access control) 

S3 API support different ACL policy of an object, here is the example about how to change the file to 'public' read
```
inputObj.SetACL(s3.BucketCannedACLPublicRead)
```

