# RNPinnedCertValidator

RNPinnedCertValidator simplifies validating ["pinned" SSL certificates](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning). A pinned certificate means that your app only trusts a specific list of certificates rather than the entire trusted root store for the device. This improves security by limiting the number of trusted certificates, and frustrates attacks that modify the trusted root store.

## Usage

1. Put your trusted certificate in your bundle.
2. Create a validator with `-initWithCertificatePath:`.

## If using NSURLConnection (iOS6 or earlier)

3. Create an `NSURLConnection` with your object as a delegate.
4. In `-connection:willSendRequestForAuthenticationChallenge:`, call `[validator validateChallenge:challenge]`.

```objc
- (void)connection:(NSURLConnection *)connection 
willSendRequestForAuthenticationChallenge:(NSURLAuthenticationChallenge *)challenge {
  RNPinnedCertValidator *validator = [[RNPinnedCertValidator alloc] initWithCertificatePath:kPathToCerFile];
  [validator validateChallenge:challenge];
}
```
## If using NSURLSession (iOS7 or later)

3. Create an `NSURLSession` with your object as a delegate.
4. In `URLSession:didReceiveChallenge:completionHandler:`, call `[validator validateChallenge:challenge completionHandler:completionHandler]` passing the completion handler through.

```objc
- (void)URLSession:(NSURLSession *)session didReceiveChallenge:(NSURLAuthenticationChallenge *)challenge
                                             completionHandler:(void (^)(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential *credential))completionHandler {
  RNPinnedCertValidator *validator = [[RNPinnedCertValidator alloc] initWithCertificatePath:kPathToCerFile];
  [validator validateChallenge:challenge completionHandler:completionHandler];
}
```

If you don't have your certificate in a handy file, pull it from your server:

``` 
openssl s_client -connect myserver:443 </dev/null 2>/dev/null | openssl x509 -outform DER > myserver.cer
```

See the `PinnedCertExample` project for an example that includes a `UIWebView`.
