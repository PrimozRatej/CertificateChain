
# Certificate chain
QUESTION: How to validate all certificates in a certificate chain.

## What are SSL certificates
- An **SSL certificate** is a digital certificate that **authenticates a website's identity** and enables an encrypted connection. 
- SSL stands for **Secure Sockets Layer**, a security protocol that creates an encrypted link between a web server and a web browser.

## **What is a [Certificate Chain](https://www.appviewx.com/education-center/what-is-a-certificate-chain/)?**
### [Links in the Certificate Chain](https://www.appviewx.com/education-center/links-in-the-certificate-chain/)
![Image](https://github.com/PrimozRatej/CertificateChain/blob/b66bf8ba9673d8fdf4065f0011763ad3bb0665d4/PKI%20Infrastructure.PNG)
## [SSL Pinning](https://medium.com/flawless-app-stories/ssl-pinning-254fa8ca2109)
## [SSL Pinning in Flutter Apps](https://medium.com/surfstudio/ssl-pinning-in-flutter-apps-254e01e57965)
- The SSL certificate chain can be traced from a private SSL certificate through intermediate certificates to the root certificate of a trusted certificate authority.

root Certificate Authorities(CAs)
 Intermediate CAs.
 
 ## Certificate chain for *solve-x.app
![image](https://github.com/PrimozRatej/CertificateChain/blob/afdab263149ec220c0ab2bcebca55c23de334195/Certificat_chain_in_browser.PNG)

[Apache: Validate SSL chain of trust to prevent MITM-attacks?](https://serverfault.com/questions/915563/apache-validate-ssl-chain-of-trust-to-prevent-mitm-attacks)

[Mobile Security via Flutter — SSL Pinning](https://medium.com/kbtg-life/mobile-security-via-flutter-ep-1-ssl-pinning-c57f18b711f6)



# Certificate chain
QUESTION: How to validate all certificates in a certificate chain.

## What are SSL certificates
- An **SSL certificate** is a digital certificate that **authenticates a website's identity** and enables an encrypted connection. 
- SSL stands for **Secure Sockets Layer**, a security protocol that creates an encrypted link between a web server and a web browser.

## **What is a [Certificate Chain](https://www.appviewx.com/education-center/what-is-a-certificate-chain/)?**
### [Links in the Certificate Chain](https://www.appviewx.com/education-center/links-in-the-certificate-chain/)
![Image](https://github.com/PrimozRatej/CertificateChain/blob/b66bf8ba9673d8fdf4065f0011763ad3bb0665d4/PKI%20Infrastructure.PNG)
## [SSL Pinning](https://medium.com/flawless-app-stories/ssl-pinning-254fa8ca2109)
## [SSL Pinning in Flutter Apps](https://medium.com/surfstudio/ssl-pinning-in-flutter-apps-254e01e57965)
- The SSL certificate chain can be traced from a private SSL certificate through intermediate certificates to the root certificate of a trusted certificate authority.

root Certificate Authorities(CAs)
 Intermediate CAs.
 
 ## Certificate chain for *solve-x.app
![image](https://github.com/PrimozRatej/CertificateChain/blob/afdab263149ec220c0ab2bcebca55c23de334195/Certificat_chain_in_browser.PNG)

[Apache: Validate SSL chain of trust to prevent MITM-attacks?](https://serverfault.com/questions/915563/apache-validate-ssl-chain-of-trust-to-prevent-mitm-attacks)

[Mobile Security via Flutter — SSL Pinning](https://medium.com/kbtg-life/mobile-security-via-flutter-ep-1-ssl-pinning-c57f18b711f6)

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
	
**Viris_thesis**:If I run the application via a proxy, I should get login information on the login page.
Because the app allows a connection to the api with a self sign cert.
**My_thesis**:   According to my research HttpClient after default do not allow connection to self sign certificate so an error should occur.
### 5. Proxy
#### Set up a Proxy
	Using Burp suite community
	Make proxy listener on localhost:8080 (127.0.0.1:8080)
![enter image description here](https://www.howtogeek.com/wp-content/uploads/2017/02/img_589cc143a0d61.png?trim=1,1&bg-color=000&pad=1,1)

	1. Set up an Android emulator to use our proxy
	2. Set up Android Emulattor Settings > Proxy
		- Check Manual proxy config
		- Host name 127.0.0.1, Port 8080
		- Apply settings
![enter image description here](https://www.howtogeek.com/wp-content/uploads/2017/02/img_589cc143a0d61.png?trim=1,1&bg-color=000&pad=1,1)

	3. Restart the emulator and run the app.
**The system reports an erro**r: ```CERTIFICATE_VERIFY_FAILED: self signed certificate in certificate chain```.   
Based on the research, I would refuse viris_thesis but one big but refuse thesis from Viris when it comes to pen testing.I do not dare because I am not a pen tester.
			
  
	4. Why did Viris successfully intercept packets? 
		4.1	Did they uploaded their certificate to the device? 
		4.2 When was pen testing done (date)? 
		4.3 Has been HttpClient updated since pentesting (depends on 4.2)?
		4.4 Has our code been updated (depends on 4.2)? 
		4.5 In which environment did pen testing take place (qa-logatec?, production?)? 
		4.6 Is it possible that another version of httpClient is running in the environment in which they performed the test? 
		4.7 And lastly, Maybe I'm looking at it completely wrong, again they are pen testers and I'm not.
	
