# RealCoin verification pass

Applied functional fixes without redesigning the existing landing/registration/login visual surfaces.

Implemented:
- Fixed BEP-20 deposit address: 0x8e54105bed3243e1ca44a0cccc6b62cf2bff9df4
- Minimum deposit enforced at 25 USD.
- Deposit calculation includes 10% REAL bonus.
- Direct/legacy deposit path now creates a pending request instead of crediting immediately.
- Withdrawal minimum enforced at 1000 REAL.
- Withdrawal address validation requires a valid 0x + 40-hex BEP-20 address.
- Withdrawal UI warning explicitly says BEP-20 only.
- KYC submission requires both front and back ID attachments and always enters PENDING review.
- KYC front/back attachments now use the Android gallery picker instead of fake URI placeholders.
- Normal users cannot self-change KYC status; the normal KYC screen no longer exposes status mutation callbacks.
- Non-admin, non-approved users are gated to KYC until approval.
- P2P ad creation and escrow trading require approved KYC.
- Users can only delete their own ads; deletion is blocked while an ad has active escrow orders.
- Wheel outcomes use an 80/20 weighted outcome split: prizes above 10 REAL are selected with 20% probability.
- Admin market-rate calculation bug fixed (USD/REAL and USD/ETB are no longer double-multiplied).
- Admin balance display shows REAL, USD and ETB values in the admin controls.
- Help Center screenshot attachment now uses the Android gallery picker.

Important limitation of the supplied ZIP:
- It does not contain landing, registration, or login screen source files, so those screens were not redesigned or altered.
- The supplied project is a local Room-based prototype rather than a multi-user backend. Admin approvals therefore operate on the local stored user/request model. A real production wallet needs server-side authentication, authorization, transaction processing and custody/payment integration.

Build note:
- An APK could not be compiled in this environment because the ZIP contains gradle-wrapper.properties but no gradle-wrapper.jar, and no Gradle installation/dependency cache is available here. The project remains an Android Studio/Gradle project and should be opened and built there.


## Final P2P / Escrow hardening
- Removed the Admin-role toggle from the Admin control UI; administrator elevation is not exposed as a user-facing control. No admin password is displayed in the app UI.
- New user wallet defaults are 0 REAL.
- Added saved Ethiopian payment account fields (bank/provider + account/phone).
- P2P payment provider is selected from: Telebirr, CBE, Dashen, Awash, Abyssinia, CBE Birr, M-Pesa.
- Creating an ad saves the selected payment account and shows it to counterparties before order confirmation.
- SELL ads cannot be created above the user's available REAL balance.
- P2P orders enforce each advertisement's minimum and maximum limits.
- Escrow payment confirmation is buyer-only; escrow release is seller-only and only after payment confirmation.
- Escrow completion credits the buyer and releases the seller's held REAL.
- Saved payment account deletion is blocked while any related active escrow order is pending/paid/disputed.
- Advertisement deletion remains blocked while active escrow orders exist.

## 2026-09-15 Onboarding Update
- Added a new polished landing screen with Create account / Login actions.
- Added registration fields: P2P nickname, legal name, phone, email, password and confirmation.
- Added dedicated KYC onboarding page with ID type dropdown: National ID, Passport, Driver License.
- Added Android gallery pickers for ID front and ID back.
- KYC submission is forced to PENDING and cannot be self-approved.
- Added KYC waiting state: submitted users see an approval waiting screen and cannot enter the main app while PENDING.
- Once the stored KYC state becomes APPROVED, the root flow automatically enters the existing main app.
- New accounts start with 0 REAL and are not seeded with demo balances.
- Added local credential hashing and login by email or P2P nickname.
- Room schema bumped to version 6 for password/KYC document-type fields.

Note: this Android project remains a local/offline prototype architecture. A production multi-user admin/KYC service should use a server-side identity/authentication database and server-side admin authorization; a client APK must never contain an administrator password.


## Final settings / rewards pass
- KYC remains accessible from Profile/Settings and the Home-screen KYC shortcut was removed so the main app KYC page is not exposed as a Home service.
- Main-app KYC submissions now persist the selected document type (National ID, Passport, or Driver License) instead of hard-coding National ID.
- Removed the client-side KYC "Demo Instant Approval" control; user submissions are always PENDING until the protected admin workflow approves them.
- Daily rewards require APPROVED KYC and retain the 24-hour cooldown.
- Removed the user-facing free-spin reset control; the 24-hour free-spin timestamp can no longer be reset from the UI.
- The spinning wheel now enforces APPROVED KYC server-side and retains the 10 REAL paid-spin / 1 free spin per 24h rules.
- Updated level/reward unit tests to match the configured Starter/Silver/Gold/Platinum/Diamond/VIP thresholds and daily rewards.


## Daily level/reward update (2026-09-15)
- Base reward: every user receives 10 REAL per 24 hours before reaching Starter.
- Starter: $100 cumulative approved deposit -> 30 REAL/day.
- Silver: $150 -> 50 REAL/day.
- Bronze: $200 -> 75 REAL/day.
- Gold: $250 -> 100 REAL/day.
- Diamond: $300 -> 150 REAL/day.
- Removed the old Platinum/VIP deposit tiers from level logic.
- Dashboard/Profile progress bars now track the user's cumulative approved deposit toward the next level, with Diamond as the maximum tier.
- Daily reward is calculated from cumulative approved deposit volume and retains the 24-hour cooldown.

## Market price admin increase control
- Administrator can increase the REAL/USD market price to any positive finite value; there is no upper limit.
- Market price changes are protected at repository level so non-admin users cannot change the rate through client calls.
- USD/ETB peg must also be positive and finite.

## Market-price propagation update
- When Admin changes the REAL/USD market price, active P2P advertisements are immediately repriced using `REAL/USD × USD/ETB`.
- New P2P escrow orders always use the latest market price rather than a stale ad price.
- Pending withdrawal requests refresh their USD value from the latest REAL/USD rate; the REAL withdrawal amount remains the user's requested amount.
- Pending deposits are credited at Admin approval using the latest market price, so the REAL amount + 10% bonus is never calculated from an obsolete rate.
- Dashboard balance valuation continues to use the live stored market rate: REAL → USD → ETB.
- Historical/completed escrow orders are not rewritten when the market price changes.
