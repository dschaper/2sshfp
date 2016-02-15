# 2sshfp

Very basic shell scripts to create SSHFP DNS records.

## Description

I wrote those very basic script to create SSHFP DNS records when I realized 
[sshfp tool](https://github.com/xelerance/sshfp) didn't support ECDSA (i.e. 
ecdsa-sha2-nistp256) and [my patch](https://github.com/xelerance/sshfp/pull/2) 
would take years before getting in to production. See [draft-os-ietf-sshfp-ecdsa-sha2-00](http://tools.ietf.org/html/draft-os-ietf-sshfp-ecdsa-sha2-00).

Now it has evolved and also support [ed25519](http://tools.ietf.org/html/draft-moonesamy-sshfp-ed25519-01) 
as the sshfp tool seems to be dead.

## Features
Supports rsa, dsa, **ecdsa and ed25519** ciphers and sha1/sha256 hash algorithms.

## SSHFP records?

SSHFP are basically host ssh key fingerprints stored in DNS records. If you can trust DNS 
query (i.e. DNSSEC) you can validate SSH's host fingerprint automatically.

Here's one: 
```soundwave.mantor.org. IN SSHFP 1 1 F48459337A91E833FA259C8F95D751D22D8541C2```

The first number refers to the cipher of the key (1=rsa, 2=dsa, 3=ecdsa, 4=ed25519), the second number is the 
hash algorithm (1=sha1, 2=sha256) used and the last long string is the hash of the key itself.

## What is it used for?

Merge the resulting DNS records in your zone and use them: 

  - configure SSH client to verify SSHFP records via 'VerifyHostKeyDNS yes';
  - make sure you're using DNSSEC. ;)

### ssh-hostkeys2sshfp

Recommended - If using OpenSSH, try `ssh-keygen -r <hostname>` command. Otherwise, run this script 
locally. Both creates SSHFP record out of `/etc/ssh/ssh_host_*_key.pub` keys.

    > ./ssh-hostkeys2sshfp soundwave.mantor.org
    soundwave.mantor.org. IN SSHFP 1 1 F48459337A91E833FA259C8F95D751D22D8541C2
    soundwave.mantor.org. IN SSHFP 1 2 0BB723AF065650613CDD4783422240BBF8C9583113FEDFD31221B435CD966791
    soundwave.mantor.org. IN SSHFP 2 1 D51B4C702C772C5BAF889EBB1DA4CC7BD8402257
    soundwave.mantor.org. IN SSHFP 2 2 9BE8B4A3DB23C0E7135BB66CE4852D124583CA04ADE4CBA22F024048A1F9F6E8
    soundwave.mantor.org. IN SSHFP 3 1 AEA8CA091F2267D543A19A9CAD80A1F524E2E7A7
    soundwave.mantor.org. IN SSHFP 3 2 4AFA3D9CE5C1C296DDA5624D5331575F202A67C40A425F930F12113CD20644AB

### ssh-keyscan2sshfp

Not recommended - This one used ssh-keyscan and connects to ssh servers to extract SSH keys remotely. 
Oviously, you need to trust your network.. which you don't right? - ARP/DNS poisoning, etc. 

    > ./ssh-keyscan2sshfp bombshock.mantor.org shockwave.mantor.org
    ####################################################
    Should not be used unless you can really trust your
    network... but who can?!?
    ####################################################
    bombshock.mantor.org. IN SSHFP 2 1 021866EBA03E5F338EE63762A70C767204490467
    bombshock.mantor.org. IN SSHFP 2 2 3955BB4170D723C2EDA27F7AD7A79CF438B940EA2D92A7929AE77ED9561F70C4
    bombshock.mantor.org. IN SSHFP 1 1 74676382DC8459218C42983A01902552529BDF9F
    bombshock.mantor.org. IN SSHFP 1 2 76C19705F568AF6E50D775FCA7BA2C029D34018D29C1502FEB06992005EC7B38
    bombshock.mantor.org. IN SSHFP 3 1 3FB05E3FC65428C8B030FB91E88EFA17C918E97D
    bombshock.mantor.org. IN SSHFP 3 2 1BCB86FA73AD341B7DFBEEA84439FA316F6916E1D912D1B71C03CA42F5B4AE89
    shockwave.mantor.org. IN SSHFP 2 1 4AC5A2243CEB9D7174DB456613B454D6299F30A8
    shockwave.mantor.org. IN SSHFP 2 2 15A706840F1FDEF2A764F42A528E8A10415219C5470DB62E924D9778B222452E
    shockwave.mantor.org. IN SSHFP 1 1 D989A5820A0E4FD4D3A03A1297E0D9897BA59063
    shockwave.mantor.org. IN SSHFP 1 2 9DDB3AF589D4063CEF5F188C1420509C118408855218865934A05F32FE2FDB31

## license

Public domain - A gift to the Internet
