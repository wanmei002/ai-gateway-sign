## Inference API Requires Signature Verification
### Client-Side Signature Implementation
```go
var r *http.Request
var parts []string
// Add HTTP method
parts = append(parts, r.Method)

// Add request path
parts = append(parts, r.URL.Path)

// Add query parameters if present
if r.URL.RawQuery != "" {
    parts = append(parts, "?"+r.URL.RawQuery)
}

// Add nonce (a random string for each request)
if nonce := r.Header.Get("X-Nonce"); nonce != "" {
    parts = append(parts, nonce)
}

message := strings.Join(parts, "\n")
hash := sha256.Sum256([]byte(message))
signature, err := ecdsa.SignASN1(rand.Reader, privateKey, hash[:])
signStr := hex.EncodeToString(signature)
fmt.Println("sign string: ", signStr)
```

### Adding Headers for Server API Requests
curl -X POST "http://127.0.0.1:8080/infer" \
-H "Content-Type: application/json" \
-H "X-Nonce: 123456" \
-H "X-Signature: sign-string" \
-H "X-Public-Key: public-key"
