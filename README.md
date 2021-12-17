

# Validation of Certificates in flutter

### 1. If intrusion is possible then the application does not use ```SecurityContext.defaultContext```.
```dart
  /// The [defaultContext] object uses a list of well-known trusted
  /// certificate authorities as its trusted roots. On Linux and Windows, this
  /// list is taken from Mozilla, who maintains it as part of Firefox. On,
  /// MacOS, iOS, and Android, this list comes from the trusted certificates
  /// stores built in to the platforms.
```
Lists: [Mozilla](https://ccadb-public.secure.force.com/mozilla/CACertificatesInFirefoxReport), [MacOS, iOS](https://support.apple.com/en-us/HT202858)

App is using ```SecurityContext.defaultContext```. 
**Then why were they able to sneak in between the mobile app and the api?**

### 2.	If trusted certificates pinning would be used. It would look something like this.
 ```dart
 (dio.httpClientAdapter as DefaultHttpClientAdapter).onHttpClientCreate  = (client) {  
	  SecurityContext sc = new SecurityContext();  
	  //file is the path of certificate  
	  sc.setTrustedCertificates(file);  
	  HttpClient httpClient = new HttpClient(context: sc);  
	 return httpClient;  
};
``` 

### 3.   Application already use ```SecurityContext.defaultContext``` or is it really?

#### 3.1 Testing with a proxy
- Put a proxy between mobile app and api
- Proxy will have self signed certificate
	
**Viris_thesis**: If I run the application via a proxy, I should get login information on the login page.
Because the app allows a connection to the api with a self sign cert.

**My_thesis**: According to my research HttpClient after default do not allow connection to self sign certificate so an error should occur.
### 4. Proxy
#### Set up a Proxy
	Using Burp suite community
	Make proxy listener on localhost:8080 (127.0.0.1:8080)
![Proxy setup](https://github.com/PrimozRatej/CertificateChain/blob/20db081750aa0b8a1b5f1294ff6f12f9125dd40e/proxy_set_up.PNG)

	1. Set up an Android emulator to use our proxy
	2. Set up Android Emulattor Settings > Proxy
		- Check Manual proxy config
		- Host name 127.0.0.1, Port 8080
		- Apply settings
![emulator proxy setup](https://github.com/PrimozRatej/CertificateChain/blob/1a6193686a7d14271f13b5c09a22a34609c0479f/emulator_proxy_setup.PNG)

	3. Restart the emulator and run the app.
**The system reports an erro**r: ```CERTIFICATE_VERIFY_FAILED: self signed certificate in certificate chain```.   
Based on the research, I would refuse viris_thesis but one big but refuse thesis from Viris when it comes to pen testing. I do not dare because I am not a pen tester.
			
  
	4. Why did Viris successfully intercept packets?
		4.1 Did they uploaded their certificate to the device? 
		4.2 When was pen testing done (date)? 
		4.3 Has been HttpClient updated since pentesting (depends on 4.2)?
		4.4 Has our code been updated (depends on 4.2)? 
		4.5 In which environment did pen testing take place (qa-logatec?, production?)? 
		4.6 Is it possible that another version of httpClient is running in the environment in which they performed the test? 
		4.7 And lastly, Maybe I'm looking at it completely wrong, again they are pen testers and I'm not.
	
### 5. How could you still get the data when snooping?🕵️‍♂️...(That I think of) 
#### 5.1 override ```createHttpClient```
```dart
// In main
Future<void> main() async {  
 HttpOverrides.global = DevHttpOverrides();   
 () async => runApp();  
}
// Allowing all the certificates to be accepted.
class DevHttpOverrides extends HttpOverrides {  
  @override  
  HttpClient createHttpClient(SecurityContext context) {  
  return super.createHttpClient(context)  
  ..badCertificateCallback = (cert, host, port) => true;  
  }  
}
```

