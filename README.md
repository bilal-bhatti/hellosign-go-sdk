[![build status](https://travis-ci.org/jheth/hellosign-go-sdk.svg?branch=master)](https://travis-ci.org/jheth/hellosign-go-sdk)

# HelloSign Go SDK
A Go wrapper for the HelloSign API.

The unofficial library for using the HelloSign API for golang.

https://app.hellosign.com/api/reference

## Installation

```go
go get github.com/bilal-bhatti/hellosign-go-sdk
```
## Usage

### Client

```go
client := hellosign.Client{APIKey: "ACCOUNT API KEY"}
```

### Embedded Signature Request

__using FileURL__

```go
request := hellosign.EmbeddedRequest{
  TestMode: true,
  ClientID: os.Getenv("HELLOSIGN_CLIENT_ID"),
  FileURL:  []string{"http://www.pdf995.com/samples/pdf.pdf"},
  Title:    "My First Document",
  Subject:  "Contract",
  Signers: []hellosign.Signer{
    hellosign.Signer{
      Email: "jane@example.com",
      Name:  "Jane Doe",
    },
  },
}

response, err := client.CreateEmbeddedSignatureRequest(request)
if err != nil {
  log.Fatal(err)
}
// type SignatureRequest
fmt.Println(response.SignatureRequestID)
```

__using File__

```go
request := hellosign.EmbeddedRequest{
  TestMode: true,
  ClientID: "APP_CLIENT_ID",
  File:     []string{"public/offer_letter.pdf"},
  Title:    "My First Document",
  Subject:  "Contract",
  Signers:  []hellosign.Signer{
    hellosign.Signer{
      Email: "jane@doe.com",
      Name:  "Jane Doe",
    },
    hellosign.Signer{
      Email: "john@gmail.com",
      Name:  "John DOe",
    },
  },
}

response, err := client.CreateEmbeddedSignatureRequest(request)
if err != nil {
  log.Fatal(err)
}
// type SignatureRequest
fmt.Println(response.SignatureRequestID)
```

__Full Feature__

```go
request := hellosign.EmbeddedRequest{
  TestMode: true,
  ClientID: os.Getenv("HS_CLIENT_ID"),
  File: []string{
    "public/offer_letter.pdf",
    "public/offer_letter.pdf",
  },
  Title:     "My Document",
  Subject:   "Please Sign",
  Message:   "A message can go here.",
  Signers: []hellosign.Signer{
    hellosign.Signer{
      Email: "freddy@hellosign.com",
      Name:  "Freddy Rangel",
      Pin:   "1234",
      Order: 1,
    },
    hellosign.Signer{
      Email: "frederick.rangel@gmail.com",
      Name:  "Frederick Rangel",
      Pin:   "1234",
      Order: 2,
    },
  },
  CCEmailAddresses: []string{
    "no@cats.com",
    "no@dogs.com",
  },
  UseTextTags:  false,
  HideTextTags: true,
  Metadata: map[string]string{
    "no":   "cats",
    "more": "dogs",
  },
  FormFieldsPerDocument: [][]hellosign.DocumentFormField{
    []hellosign.DocumentFormField{
      hellosign.DocumentFormField{
        APIId:    "api_id",
        Name:     "display name",
        Type:     "text",
        X:        123,
        Y:        456,
        Width:    678,
        Required: true,
        Signer:   0,
      },
    },
    []hellosign.DocumentFormField{
      hellosign.DocumentFormField{
        APIId:    "api_id_2",
        Name:     "display name 2",
        Type:     "text",
        X:        123,
        Y:        456,
        Width:    678,
        Required: true,
        Signer:   1,
      },
    },
  },
}

response, err := client.CreateEmbeddedSignatureRequest(request)
if err != nil {
  log.Fatal(err)
}
// type SignatureRequest
fmt.Println(response.SignatureRequestID)
```

### Get Signature Request

```go
// uses SignatureRequestID
res, err := client.GetSignatureRequest("6d7ad140141a7fe6874fec55931c363e0301c353")

// res is SignatureRequest type
res.SignatureRequestID
res.Signatures
```

### Get Embedded Sign URL

```go
// uses SignerID
res, err := client.GetEmbeddedSignURL("deaf86bfb33764d9a215a07cc060122d")

res.SignURL =>  "https://app.hellosign.com/editor/embeddedSign?signature_id=deaf86bfb33764d9a215a07cc060122d&token=TOKEN"
```

### Get PDF

```go
// uses SignatureRequestID
fileInfo, err := client.GetPDF("6d7ad140141a7fe6874fec55931c363e0301c353", "/tmp/download.pdf")

fileInfo.Size() => 98781
fileInfo.Name() => "download.pdf"
```

### Get Files

```go
// uses SignatureRequestID
fileInfo, err := client.GetFiles("6d7ad140141a7fe6874fec55931c363e0301c353", "zip",  "/tmp/download.zip")

fileInfo.Size() => 98781
fileInfo.Name() => "download.zip"
```

### List Signature Requests

```go
res, err := client.ListSignatureRequests()

res.ListInfo.NumPages => 1
res.ListInfo.Page => 1
res.ListInfo.NumResults => 19
res.ListInfo.PageSize => 20

len(res.SignatureRequests) => 19
```

### Update Signature Request

```go
res, err := client.UpdateSignatureRequest(
  "9040be434b1301e31019b3dad895ed580f8ca890", // SignatureRequestID
  "deaf86bfb33764d9a215a07cc060122d", // SignatureID
  "joe@hello.com", // New Email
)

res.SignatureRequestID => "9040be434b1301e31019b3dad895ed580f8ca890"
res.Signatures[0].SignerEmailAddress => "joe@hello.com"
```

### Cancel Signature Request

```go
// uses SignatureRequestID
res, err := client.CancelSignatureRequest("5c002b65dfefab79795a521bef312c45914cc48d")

// res is *http.Response
res.StatusCode => 200
```
