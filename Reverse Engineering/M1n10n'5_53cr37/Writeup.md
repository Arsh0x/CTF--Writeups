Challenge Writeup

Category: Reverse Engineering
Difficulty: Medium

1. Initial Analysis

The challenge provides an APK file, suggesting that the solution involves analyzing an Android application.

To understand the application's behavior, I first installed and ran the APK in an Android emulator using Genymotion.

When launching the application, the following hint appeared on the screen:

"Look into me my Banana Value is interesting"

This message strongly suggested that the keyword "banana" might be embedded somewhere in the application's resources or code.

2. Decompiling the APK

To inspect the internal files of the APK, I used Apktool to decompile it.

apktool d minions.apk

This extracts the application's resources and smali code into a readable directory structure.

3. Searching for the Hint

Since the hint referenced "Banana Value", I searched the entire decompiled project for occurrences of the word banana.

grep -r "Banana" .

This revealed a promising file:

minions/res/values/strings.xml

Inside this file, there was a suspicious Base32-encoded string, which appeared to be ciphertext.

4. Decoding the Ciphertext

The encoded string looked like Base32 because:

It used characters A–Z and 2–7

It followed the typical Base32 structure

so I decoded it "echo "OBUWG32DKRDHWMLUL53TI43OG5PWQNDSMRPXK3TSGR3DG3BRNY4V65DIGNPW2MDCGFWDGX3DGBSDG7I=" | base32 -d"

5. Flag

6. picoCTF{1t_w4sn7_h4rd_unr4v3l1n9_th3_m0b1l3_c0d3}
