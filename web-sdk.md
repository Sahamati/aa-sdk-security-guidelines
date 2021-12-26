---
description: Secure AA Web SDK development and integration with third-party apps
---

# AA Web SDK

## General guidelines

Follow are some general guidelines for creating secure Account Aggregator Web SDK which can be embedded in FIU applications without exposing any sensitive information.

### 1. Prevent storing sensitive information such as Personal Information or Financial information in the browser’s local storage

1. Don't store any sensitive information such as Personal Information of the user, Financial information in browser local storage, and store only data which is non-sensitive.
2. Disable caching for responses that contain sensitive data. **Cache-Control** HTTP header can be used to prevent any sensitive data from getting cached in the browser
   1. No Caching - Using `Cache-Control: no-store` will not store anything about the client request or server response. A request is sent to the server and a full response is downloaded each and every time.
   2. No Storing - Use `Cache-Control: no-cache` will send the request to the origin server for validation before releasing a cached copy.
   3. Validation - Use `Cache-Control: must-revalidate` will verify the status of the stale resources before using and expired ones should not be used.

### 2. Prevent auto-filling/auto-completion for sensitive fields

By default, browsers remember information entered by the user for input fields, and this enables browsers to offer auto-completion. For sensitive information submitted by the user, such as an id or security code can be prevented by using autocomplete attribute.

1. Autocomplete attributes can be set to off for an entire form or specific fields in the form.&#x20;
2. It tells the browser not to save data inputted by the user for later autocompletion on similar forms.
3. It stops the browser from caching form data in the session history

### 3. Securing cookies created by AA Web SDK

Securing cookies is very important to keep applications and data secure. If cookies are not secured, any information stored in cookies could be accessed by FIU or any third-party applications. To secure cookies created by AA WebSDK:

1. Do not store any sensitive data in Cookie.
2. Restrict subdomains and paths the cookies should be sent to
   1. Path attribute specifies what paths on the site to send the cookie.
   2. Domain attribute allows restricting sharing the cookies with sub-domains
   3. SameSite attribute restricts cookies sent to cross-site requests and provides protection against CSRF.
3. Secure attribute restricts cookies sent to the server only over the HTTPS protocol, never with unsecured HTTP, and therefore can't easily be accessed by a man-in-the-middle attacker
4. HttpOnly attribute is used to tell the browser that it should not allow javascript to access the contents of the cookie. This is primarily a defense against cross-site scripting, as it will prevent hackers from being able to retrieve and use the session through such an attack.

### 4. IFrame Integration

Embedding AA Web SDK in IFrame will enhance user experience but they also bring security risks.

1. Don't use iframes at any point in time. This could lead to various vulnerabilities like clickjacking etc. Use DIVs to load your content.&#x20;
2. Use frame bursting codes to prevent your page from being vulnerable to clickjacking attacks.
3. X-Frame-Options is an HTTP header that allows sites control over how your site may be framed within an iframe.
4. X-Frame-Options has been superseded by the Content Security Policy’s frame-ancestors directive, which allows considerably more granular control over the origins allowed to frame a site:
   1. DENY - disallow allow attempts to iframe site
   2. SAMEORIGIN - allow the site to iframe itself

### 5. Widget-like integration

Use a widget concept like how payment gateway widgets get integrated into a website. For more details, you can explore how PayPal or Stripe, or Razorpay payment gateway widgets are designed.

### 6. Use of Custom/Virtual Keyboards for entering sensitive information

Use of Custom/Virtual Keyboards for entering any sensitive information such as AA ids, passwords, etc. can prevent keylogging attacks and capturing sensitive information. The custom/virtual keyboard can be a number only to capture numerical information like PIN/OTP, last 6 digits of debit card and debit card expiry date, etc.

### 7. Data Validation

It is important to validate that all data is syntactically and semantically valid as it arrives and enters the system.

1. Assume all data entering the system is untrusted and must be validated.
2. An allow list can be created for validating the syntax/pattern of the incoming data.
3. Input Validation must also take place on the server-side and should check for input data, file uploads, HTTP headers, etc.

### 8. Exposing sensitive information in console logs/error messages

1. Do not display sensitive information in messages displayed to the user.
2. Proper Exception Management - In case of any failure, never display/log any actual system messages which do not do the end-user any good, and instead work as valuable clues for potentially threatening entities.

### 9. Update and Patch regularly

1. Use a CDN-based JS source rather than distributing the actual AA Web SDK source file(s).
2. This allows to update and fix any issue in the future and easily be rolled out to all websites using CDN.
3. Installing software updates and patches is one of the most effective ways to keep your software secure.

### 10. Obfuscate code

1. Obfuscate and minify the javascript code before giving it to any third party.
2. Prevent attackers from reverse engineering by obfuscating the code.
3. This will help prevent attackers from gaining information about internal systems and modifying code to perform any attack.

### 11. Static Application Security Testing Tools

Static Application Security Testing (SAST) Tools are helpful in analyzing source code and identifying security vulnerabilities. Here are some of the SAST tools recommended by OWASP for source code analysis - [https://owasp.org/www-community/Source\_Code\_Analysis\_Tools](https://owasp.org/www-community/Source\_Code\_Analysis\_Tools).

## Best Practices

Below are some of the guidelines which are general best practices and should be implemented by default.

### 1. Encrypt Data

1. Implement HTTPs and redirect all http traffic to https
2. Load all resources like javascripts, css files, images etc.. through only secure channels

### 2. Sufficient Logging & Monitoring

1. Ensure all activities are sufficiently logged with user context to identify any malicious/suspicious activity.
2. While logging these error information, do not log any information visible to the user causing information leakage.
3. Do not log any sensitive information like passwords, identity data.
4. Distinguish traffic from different customers for monitoring.

### 3. Limit Incorrect attempts

It is recommended to set the maximum number of incorrect password/OTP submissions no more than three.

## References

* [https://www.pluralsight.com/courses/javascript-security-best-practices](https://www.pluralsight.com/courses/javascript-security-best-practices)
* [https://code.tutsplus.com/articles/client-side-security-best-practices--net-35677](https://code.tutsplus.com/articles/client-side-security-best-practices--net-35677)
* [https://cheatsheetseries.owasp.org/cheatsheets/REST\_Security\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/REST\_Security\_Cheat\_Sheet.html)
* [https://info.veracode.com/secure-coding-best-practices-hand-book-guide-resource.html](https://info.veracode.com/secure-coding-best-practices-hand-book-guide-resource.html)
* [https://owasp.org/www-project-top-ten/](https://owasp.org/www-project-top-ten/)&#x20;
* [https://owasp.org/www-project-top-ten/2017/A3\_2017-Sensitive\_Data\_Exposure](https://owasp.org/www-project-top-ten/2017/A3\_2017-Sensitive\_Data\_Exposure)
* [https://infosec.mozilla.org/guidelines/web\_security](https://infosec.mozilla.org/guidelines/web\_security)
* [https://developer.mozilla.org/en-US/docs/Web/Security/Securing\_your\_site/Turning\_off\_form\_autocompletion](https://developer.mozilla.org/en-US/docs/Web/Security/Securing\_your\_site/Turning\_off\_form\_autocompletion)
* [https://developer.mozilla.org/en- US/docs/Web/HTTP/Cookies#restrict\_access\_to\_cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#restrict\_access\_to\_cookies)
* [https://owasp.org/www-community/Free\_for\_Open\_Source\_Application\_Security\_Tools](https://owasp.org/www-community/Free\_for\_Open\_Source\_Application\_Security\_Tools)

### Change History

| Date            | Contributor   | Description                                           |
| --------------- | ------------- | ----------------------------------------------------- |
| Initial release | Tamaghna Basu | Initial guidelines for Web SDK.                       |
| 26 Dec, 2021    | Lendingkart   | Extended guidelines, including common best practices. |
