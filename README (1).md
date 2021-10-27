---
description: Secure AA Web SDK development and integration with third-party apps
---

# AA Web SDK

1. Use a CDN-based JS source rather than distributing the actual source file. This way, you can update and fix any issue in the future and that will be applied to all the websites that are using it.
2. Obfuscate and minify the javascript code before giving it to any third party.
3. Use a widget concept like how payment gateway widgets get integrated into a website. For more details, you can explore how PayPal or Stripe, or Razorpay payment gateway widgets are designed.
4. Don't use iframes at any point in time. This could lead to various vulnerabilities like clickjacking etc. Use DIVs to load your content.
5. Use frame bursting codes to prevent your page from being vulnerable to clickjacking attacks. Read more about it and see the various recommendations to prevent it. If you can afford it, ask your developers to go through some good courses on javascript security best practices.

### References

* [https://www.pluralsight.com/courses/javascript-security-best-practices](https://www.pluralsight.com/courses/javascript-security-best-practices)
* [https://code.tutsplus.com/articles/client-side-security-best-practices--net-35677](https://code.tutsplus.com/articles/client-side-security-best-practices--net-35677)
* [https://cheatsheetseries.owasp.org/cheatsheets/REST\_Security\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/REST\_Security\_Cheat\_Sheet.html)
* [https://info.veracode.com/secure-coding-best-practices-hand-book-guide-resource.html](https://info.veracode.com/secure-coding-best-practices-hand-book-guide-resource.html)
