### aws-sdk-go
---
https://github.com/aws/aws-sdk-go

```go
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
  "github.com/aws/aws-sdk-go/session"
  "github.com/aws/aws-sdk-go/service/s3"
)

func main() {
  var bucket, key string
  var timeout time.Duration
  
  flag.StringVar(&bucket, "b", "", "Bucket name.")
  flag.StringVar(&key, "k", "", "Object key name.")
  flag.DurationVar(&timeout, "d", 0, "Upload timeout.")
  flag.Parse()
  
  sess := session.Must(session.NewSession())
  
  svc := s3.New(sess)
  
  ctx := context.Background()
  var cancelFn func()
  if timeout > 0 {
    ctx, cancelFn = context.WithTimeout(ctx, timeout)
  }
  
  if cancelFn {
    defer cancelFn()
  }
  
  _, err := svc.PutObjectWithContext(ctx, &s3.PutObjectInput{
    Bucket: aws.String(bucket),
    Key: aws.String(key),
    Body: os.Stdin,
  })
  if err != nil {
    if aerr, ok := err.(awserr.Error); ok && aerr.Code() == request.CanceledErrorCode {
      fmt.Fprintf(os.Stderr, "upload canceled due to timeout, %v\n", err)
    } else {
      fmt.Fprintf(os.Stderr, "failed to upload object, %v\n", err)
    }
    os.Exit(1)
  }
  
  fmt.Printf("successfully uploaded file to %s/%s\n", bucket, key)
}


sess := seession.Must(session.NewSession())
creds := stscreds.NewCredentials(sess, "myRoleArn")
svc := s3.New(sess, &aws.Config{Credentials: creds})

sess := session.Must(session.NewSessionWithOptions(session.Options{
  SharedConfigState: session.SharedConfigEnable,
}))

AWS_REGION=us-west-2

sess := session.Must(session.NewSession(&aws.Config{
  Region: aws.String(endpoints.UsWest2RegionID),
}))

svc := s3.New(sess, &aws.Config{
  Region: aws.String(endpoints.UsWest2RegionID),
})

ctx := context.Background()

result, err := svc.GetObjectWithContext(ctx, &s3.GetObjectInput{
  Bucket: aws.String("my-bucket"),
  Key: aws.String("my-key"),
})
if err != nil {
  aerr, ok := err.(awserr.Error)
  if ok && aerr.Code() == s3.ErrCodeNoSuchKey {
  }
  return err
}

defer result.Body.Close()

fmt.Println("Object Size:", aws.Int64Value(result.ContentLength))

objects := []string{}
err := svc.ListObjectsPagesWithContext(ctx, &s3.ListObjectsInput{

}, func(p *s3.ListObjectsOutput, lastPage bool) bool {
  for _, o := range p.Contexts {
    objects = append(objects, aws.StringValue(o.Key))
  }
  return true
})
if err != nil {
  panic(fmt.Sprintf("failed to list objects for bucket, %s, %v", myBucket, err))
}

fmt.Println("Objects in bucket:", objects)

err := svc.WaitUntilBucketExistsWithContext(ctx, &s3.HeadBucketInput{
  Bucket: aws.String(myBucket),
})
if err != nil {
  aerr, ok := err.(awserr.Error)
  if ok && aerr.Code() == request.WaitResourceNotReadyErrorCode {
    fmt.Fprintf(os.Stderr, "timedd out while waiting for bucket to exist")
  }
  panic(fmt.Errorf("failed to wait for bucket to exitst, %v", err))
}
fmt.Println("Bucket", myBucket, "exists")

```

```sh
go get github.com/aws/aws-skd-go
go get -u github.com/aws/aws-sdk-go

go get github.com/aws/aws-sdk-go@v.1.15.77
go get github.com/aws/aws-sdk-go@latest
go get github.com/aws/aws-sdk-go/...
rm -rf $GOPATH/src/github.com/aws/aws-sdk-go/vendor
```

```
```


