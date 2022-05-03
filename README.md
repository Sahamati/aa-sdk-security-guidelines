# AA SDK Security Guidelines

These security guidelines have to be implemented in the account aggregator (AA) ecosystem by AAs that offer SDKs as AA Clients, which can be embedded into third-party apps (such as FIU apps).

The AA SDK should be able to capture information securely without exposing the information captured to the embedding FIU app.


## Platform agnostic guidelines
1. The SDK should support only consent approval/rejection flow. The SDK can also include listing the consents, but only consents created by the respective FIU.
2. Traceability – The AA should be able to trace any API call made from the SDK to the FIU.
3. SDK should authenticate and specify version with and to the AA using a certificate or a token per build. 
4. The SDK should not expose any information related to user's account information like the accounts discovered and number of accounts to the FIU or any other source.
5. The AA should have the ability to block API calls from a particular FIU app if the FIU app is found to be abusing the SDK.
6. The UI built using the SDK should be certified by the AA, where necessary information of the consent is displayed. Similar to the information shown by AA's web portal.


Currently, the guidelines are available for Web and Android platforms.
### Quick links:

1. [Guidelines for Web based SDKs](web-sdk.md)
2. [Guidelines for Android platform based SDKs](android-sdk.md)
