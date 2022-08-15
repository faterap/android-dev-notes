# pod install vs pod update

### TL;DR:

- Use `pod install` to *install new pods* in your project. **Even if you already have a `Podfile` and ran `pod install` before**; so even if you are just adding/removing pods to a project already using CocoaPods.
- Use `pod update [PODNAME]` only when you want to **update pods to a newer version**.