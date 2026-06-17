# NetSapiens Softphone Evaluation Checklist

> A practical, vendor-neutral checklist for evaluating softphones before deploying them on the NetSapiens platform. Built from real deployment experience, not marketing material.

If you're a service provider, telecom reseller, or MSP picking a softphone to run on NetSapiens, this checklist covers the things that actually determine whether the deployment works. Most of these come from deployments that went wrong. Each item exists because skipping it cost someone time.

Fork it, adapt it, use it in your own evaluation process. Pull requests welcome if you've got tests worth adding.

## Why this exists

NetSapiens is a mature, well-understood platform. The platform decision is rarely where deployments fail. The softphone layer is. Two softphones can both claim NetSapiens compatibility, both demo cleanly, and behave completely differently in production, especially once user counts climb past a few hundred.

Vendor demos are designed to hide these differences. This checklist is designed to surface them before you sign anything.

## How to use this

Run every applicable test during your evaluation period, before committing to a contract. Score each item. A softphone that fails the critical tests (marked below) is not ready for a production reseller deployment regardless of how good its feature list looks.

---

## 1. Provisioning at scale `CRITICAL`

- [ ] Provision 50+ users at once, not just a handful
- [ ] Confirm all devices land on the same config version
- [ ] Push a config change and verify it propagates to every device
- [ ] Rotate a SIP credential and confirm no devices keep using the old one
- [ ] Test the "thundering herd" case: many devices re-provisioning simultaneously after a restart
- [ ] Confirm provisioning pulls cleanly from the NetSapiens Manager Portal

**Failure mode this catches:** Softphones that work at small scale and choke when a reseller onboards a large customer in a single batch.

## 2. Push notification reliability `CRITICAL`

- [ ] Lock an iPhone, wait one hour, place a call to it. Does it ring?
- [ ] Repeat on a real Android device with battery optimization fully enabled
- [ ] Confirm incoming calls use CallKit (iOS) and ConnectionService (Android)
- [ ] Verify the call shows as a native call screen, not an app notification
- [ ] Check that push uses APNs and FCM, not a persistent background SIP registration

**Failure mode this catches:** The most common cause of mobile deployment failure. Clients relying on background registration miss calls because the OS kills the process.

## 3. Network handoff and call continuity

- [ ] Start a call on Wi-Fi, walk to cellular, confirm the call survives
- [ ] Switch back from cellular to Wi-Fi mid-call
- [ ] Check whether audio cuts out during the handoff and whether it recovers
- [ ] Confirm the client does an ICE restart rather than dropping the call

**Failure mode this catches:** Calls that drop the moment a mobile user steps out of Wi-Fi range.

## 4. Audio quality on real networks

- [ ] Test calls on 4G, not just Wi-Fi
- [ ] Test on a congested or low-bandwidth connection
- [ ] Confirm the codec being negotiated is Opus or G.722, not G.711 by default
- [ ] Check for one-way audio symptoms (usually a NAT/SBC issue, not the client)
- [ ] Test on a deliberately poor network and confirm graceful degradation

**Failure mode this catches:** Softphones that sound perfect in the demo and choppy in production.

## 5. White-label branding depth `CRITICAL for resellers`

- [ ] Confirm the app ships under YOUR developer account, not the vendor's
- [ ] Confirm your branding appears end to end, not just a logo on the splash screen
- [ ] Confirm you control the app store listing under your own brand
- [ ] Verify per-tenant branding works if you run multiple reseller tenants

**Failure mode this catches:** "White-label" that's actually co-branding, giving your customers near-zero switching cost and making your service look generic.

## 6. Mobile call center capability

*Only relevant if any downstream customers run agent/contact center operations.*

- [ ] Queue login/logout works on mobile, not just desktop
- [ ] Agent status control works on mobile
- [ ] Queue monitoring is available on mobile
- [ ] Call transfer (blind and attended) works cleanly on mobile

**Failure mode this catches:** Softphones that support call center features on desktop and treat mobile as an afterthought.

## 7. Voicemail, call history, and contact sync

- [ ] Voicemail syncs through the NetSapiens API, not stored only on device
- [ ] Voicemail actions (delete, forward) sync back to NetSapiens
- [ ] Call history shows correct caller ID, including across domains
- [ ] Contacts pull from NetSapiens phone books and sync edits back

**Failure mode this catches:** Quietly broken sync that nobody notices for weeks, then surfaces as wrong caller IDs and missing voicemails.

## 8. Security and compliance

- [ ] TLS for SIP signaling, enabled by default
- [ ] SRTP for media encryption, enabled by default
- [ ] Confirm encryption is on by default, not buried in advanced settings
- [ ] Verify compliance alignment (HIPAA, PCI DSS) if serving regulated industries
- [ ] Request documentation, not just verbal claims

**Failure mode this catches:** Vendors who claim security support but ship it disabled or undocumented.

## 9. Vendor support quality

- [ ] File a real support ticket during the trial period
- [ ] Measure the response time, then multiply by three for production expectations
- [ ] Ask about update cadence for iOS and Android platform changes
- [ ] Check how quickly they have historically responded to OS-breaking changes

**Failure mode this catches:** Slow vendor support that turns a minor production issue into a multi-day outage.

## 10. Cross-platform consistency

- [ ] Test desktop (Windows, macOS) and mobile (iOS, Android)
- [ ] Confirm same login, same number, same core features across platforms
- [ ] Check that the experience feels consistent, not like two different products
- [ ] Verify web/browser access if your deployment needs it

**Failure mode this catches:** Softphones strong on one platform and weak on another, generating support tickets from whichever side got neglected.

---

## Scoring guide

| Result | Meaning |
|---|---|
| Passes all `CRITICAL` items | Worth serious consideration |
| Fails any `CRITICAL` item | Not ready for production reseller deployment |
| Passes critical, fails some others | Acceptable depending on your specific needs |

The three `CRITICAL` tests (provisioning at scale, push notifications, white-label depth for resellers) are the ones that most often determine whether a deployment succeeds or quietly fails. Weight them accordingly.

## A note on methodology

These tests came from watching real NetSapiens deployments succeed and fail over several years. The pattern is consistent: the platform is the easy, predictable part, and the softphone layer is where the variance lives. A softphone that passes this checklist tends to scale smoothly. One that fails the critical items tends to get replaced within a couple of years.

If you want more detail on the reasoning behind each test, there's a longer writeup on [softphone evaluation for NetSapiens resellers](https://tragofone.com/softphone-evaluatuation-for-netsapiens-resellers/) that goes deeper on the why behind several of these.

## Contributing

If you've run NetSapiens deployments and have tests worth adding, open an issue or send a pull request. Keep additions practical and vendor-neutral. The goal is a checklist that helps people evaluate honestly, not one that favors any specific vendor.

## License

[![CC0](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/cc-zero.svg)](https://creativecommons.org/publicdomain/zero/1.0/)

To the extent possible under law, contributors have waived all copyright and related or neighboring rights to this work.
